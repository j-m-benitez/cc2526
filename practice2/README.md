# Práctica 2

Implementación de un servicio usando Functions-as-a-Service


## Objetivos de la práctica: 

- Instalar e implementar una herramienta de orquestación de contenedores: Kubernetes.
- Implementar la funcionalidad del catálogo de funciones y del servicio de funciones basado en OpenFaaS.
- Implementar diferentes funciones disponibles para FaaS.
- Implementar una función escalable que sirva de componente para la identificación biométrica de usuarios a partir de imágenes faciales. 

La idea principal de la práctica es crear una o más funciones que permitan:

- Capturar/recoger una imagen (por ejemplo, desde una URL) como entrada a la función.
- La función debe detectar las caras que aparecen.
- La función debe devolver la imagen con los rostros detectados enmarcados en un rectángulo.


## Software de base (no necesario en el servidor de UGR)

Para ello, necesitarás instalar las siguientes plataformas o herramientas:

- Instalar Kubernetes (por ejemplo, [Minikube](https://minikube.sigs.k8s.io)). Encontrarás instrucciones detalladas en [Orquestación de contenedores](../session4/)  
- Instalar una plataforma RAS: [OpenFaaS](https://www.openfaas.com) sobre Kubernetes. Encontrarás instrucciones detalladas en [Funciones como servicio (FaaS)](../session7/)
  
## Cómo implementar una función como servicio

OpenFaaS cuenta con un almacén de plantillas de funciones en el que ya hay algunas funciones disponibles. Este almacén se implementa mediante un manifiesto JSON, que se encuentra en un repositorio público de GitHub. Se pueden enviar solicitudes de incorporación de cambios (PR) al archivo para ampliarlo y actualizarlo, y las empresas pueden incluso tener sus propios almacenes. Se puede acceder al almacén de funciones a través de la CLI utilizando el comando raíz `faas-cli store`. Desde allí, puedes buscar una función con `faas-cli store list` e implementar la que desees con `faas-cli store deploy`.

Se puede obtener un listado completo de las funciones disponibles con:

```
$ faas-cli store list

FUNCTION              AUTHOR       DESCRIPTION
nodeinfo              openfaas     NodeInfo
env                   openfaas     env
sleep                 openfaas     sleep
shasum                openfaas     shasum
figlet                openfaas     Figlet
curl                  openfaas     curl
printer               openfaas     printer
youtube-dl            openfaas     youtube-dl
sentimentanalysis     openfaas     SentimentAnalysis
hey                   openfaas     hey
nslookup              openfaas     nslookup
certinfo              stefanprodan SSL/TLS cert info
colorise              alexellis    Colorization
inception             alexellis    Inception
alpine                openfaas     alpine
face-detect-pigo      esimov       Face Detection with Pigo
ocr                   viveksyngh   Tesseract OCR
qrcode-go             alexellis    QR Code Generator - Go
nmap                  openfaas     Nmap Security Scanner
cows                  openfaas     ASCII Cows
text-to-speech        rorpage      OpenFaaS Text-to-Speech
mquery                rgee0        Docker Image Manifest Query
face-detect-opencv    alexellis    face-detect with OpenCV
face-blur             esimov       Face blur by Endre Simo
normalisecolor        alexellis    normalisecolor
coherent-line-drawing esimov       Line Drawing Generator from a photograph
openfaas-exif         servernull   Image EXIF Reader
openfaas-opennsfw     servernull   Open NSFW Model
identicon             rgee0        Identicon Generator
```
Para buscar algún tema, puedes utilizar `grep`. Por ejemplo, para buscar funciones de *face recognition*:

```
$ faas-cli store list | grep face
face-detect-pigo      esimov       Face Detection with Pigo
face-detect-opencv    alexellis    face-detect with OpenCV
face-blur             esimov       Face blur by Endre Simo
```

Podemos ver que ya hay dos funciones de reconocimiento facial disponibles en la tienda de plantillas de OpenFaaS. Podemos obtener más información sobre ellas con el comando `faas-cli store inspect`: 
```
$ faas-cli store inspect face-detect-pigo
Title:       Face Detection with Pigo
Author:      esimov
Description: 
Detect faces in images using the Pigo face detection library. You provide an
image URI and the function draws boxes around the detected faces.

Image:    esimov/pigo-openfaas:0.1
Process:  
Repo URL: https://github.com/esimov/pigo-openfaas
Environment:
-  output_mode: image
-  input_mode:  url

Labels:
-  com.openfaas.ui.ext: jpg

$ faas-cli store inspect face-detect-opencv
Title:       face-detect with OpenCV
Author:      alexellis
Description: 
Detect faces in images. Send a URL as input and download a file with boxes drawn
around detected faces. From our friend Nic Jackson

Image:    alexellis2/facedetect:0.1
Process:  
Repo URL: https://github.com/alexellis/facedetect-openfaas
Environment:
-  output_mode: image
-  input_mode:  url

Labels:
-  com.openfaas.ui.ext: jpg


```
En estas descripciones podemos encontrar los repositorios, los autores y unas breves instrucciones sobre cómo funcionan ambas funciones. Por ejemplo, ambas toman como entrada la URL de una imagen y devuelven como salida un archivo de imagen con recuadros dibujados alrededor de los rostros detectados.

Para implementar estas funciones, ejecuta: 
```
$ faas-cli store deploy face-detect-pigo

Deployed. 202 Accepted.
URL: http://127.0.0.1:8080/function/face-detect-pigo

$ faas-cli store deploy face-detect-opencv

Deployed. 202 Accepted.
URL: http://127.0.0.1:8080/function/face-detect-opencv
```

Aquí tienes las URL de las dos funciones. También puedes verlas ahora en la interfaz gráfica de usuario del portal de OpenFaaS: `http://127.0.0.1:8080/ui/`. **En el servidor de la UGR, reemplaza esto con el URL y puertos correctos, tal y como se explicó en [Funciones como servicio (FaaS)](../session7/)**

Puedes ejecutar las funciones en la interfaz de usuario introduciendo la URL de una imagen en el campo *Request body* como se muestra en la siguiente captura:

![](invoke-face-detect.png)

y pulsando sobre *INVOKE*. También puedes ejecutar la funcón desde la línea de órdenes con curl:

```
curl -d https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSCdio_Sf9aON6NjLHo5fXjG1HNZzWCaTsUjQ http://127.0.0.1:8080/function/face-detect-pigo -o test.png
```

donde: 
- *-d* es para especificar que este URL son datos de entrada para el segundo URL (the function)
- *-o* es para almacenar la salida en un fichero (llamado *test.png* en este caso) 

Evalúa las dos funciones, `face-detect-pigo` y `face-detect-opencv`, sobre algunas imágenes de prueba que puedes encontrar en línea tales como `https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSCdio_Sf9aON6NjLHo5fXjG1HNZzWCaTsUjQ` para verificar que funcionan correctamente. 



## Desarrollar tus propias funciones para FaaS

**Para el desarrollo de funciones, puedes elegir cualquier lenguaje compatible con la plataforma de funciones como servicio. Si deseas utilizar otro lenguaje para el diseño de la función, tendrás que implementarla desde un contenedor.**

Si utilizas Node.js, Java u otros lenguajes, consulta los [ejemplos.](https://github.com/openfaas/faas/tree/master/sample-functions)

## Mi primera función (plantilla con Python)

En el caso concreto del ejemplo de la plataforma OpenFaaS, la función se crearía de la siguiente manera:

```
$ faas-cli new --lang python3-http facesdetection-python
```

Esto crea los siguientes ficheros:
```
stack.yaml
facesdetection-python/handler.py
facesdetection-python/handler_test.py
facesdetection-python/requirements.txt
facesdetection-python.yml
```

El fichero `handler.py` contiene el código que responde a la llamada a una función:
```
def handle(event, context):
    return {
        "statusCode": 200,
        "body": "Hello from OpenFaaS!"
    }
```
Puedes editar el fichero `handler.py` para que muestre la solicitud al usuario:

```
import sys
import json

def handle(event, context):
    version = sys.version

    def inspect_object(obj, name):
        result = {}
        for attr in dir(obj):
            if attr.startswith("__"):
                continue
            try:
                val = getattr(obj, attr)
                result[attr] = str(val)
            except Exception as e:
                result[attr] = f"<Error: {e}>"
        return f"{name}:\n" + json.dumps(result, indent=2)

    return {
        "statusCode": 200,
        "body": (
            f"Python version: {version}\n\n"
            f"{inspect_object(event, 'Event')}\n\n"
            f"{inspect_object(context, 'Context')}"
        )
    }
```


El fichero `requirements.txt` se puede utilizar para instalar módulos de pip (o con uv) durante la compilación. Los módulos de pip añaden bibliotecas o paquetes con funcionalidad extra como MySQL, Numpy o sk-learn.


`stack.yaml` contiene información sobre cómo compilar y desplegar tu función:

```
version: 1.0
provider:
  name: openfaas
  gateway: http://127.0.0.1:8080
functions:
  facesdetection-python:
    lang: python3-http
    handler: ./facesdetection-python
    image: yourRegistryPrefixWillBeHere/facesdetection-python:latest
```

Los campos principales que queremos estudiar aquí son:

- `gateway`: edita el puerto según sea corresponda (entre los que se te han asignado).
- `lang`: el nombre de la plantilla con la que se va a compilar.
- `handler`: la carpeta (no el archivo) donde se encuentra el código del controlador.
- `image`: el nombre de la imagen de Docker (poman) con el prefijo adecuado para su uso con podman build / push. Hay información adicional sobre esto más abajo. Se recomienda cambiar la etiqueta cada vez que modifiques el código, aunque también puedes dejarla como «latest».

Puedes ignorar en gran medida los campos `provider`, que son opcionales, pero te permiten codificar de forma fija una dirección de puerta de enlace (gateway) alternativa distinta de la predeterminada.

La versión comunitaria de OpenFaaS que utilizamos solo admite imágenes públicas, por lo que tendrás que publicar tu imagen en un sitio como Docker Hub. Para ello, debes crear una cuenta en `https://hub.docker.com/` y, a continuación, sustituir `yourRegistryPrefixWillBeHere` en `stack.yaml` por tu nombre de usuario de Docker Hub. Después, tendrás que iniciar sesión en Docker Hub así:

```
docker login --username <your username here> --password <your password here>
```

Ahora, hay tres pasos que debes seguir para poner en marcha tu función, tanto inicialmente como para actualizarla. Ejecuta los siguientes mandatos desde la carpeta donde se encuentra el archivo `stack.yaml`:

- `faas-cli build`: Crea una imagen de contenedor local e instala cualquier otro archivo necesario, como los que figuran en el archivo requirements.txt.
- `faas-cli push`: transfiere la imagen del contenedor de la función desde nuestra biblioteca local de Docker al registro alojado.
- `faas-cli deploy`: utilizando la API REST de OpenFaaS, crea una implementación dentro del clúster de Kubernetes y un nuevo pod para gestionar el tráfico.

Todos estos mandatos se pueden combinar con la orden `faas-cli up` para mayor brevedad, tanto inicialmente como para las actualizaciones.

```
$ faas-cli up -f stack.yaml
```

Espera unos instantes y verás aparecer una URL `http://127.0.0.1:8080/function/facesdetection-python`

¡Ya está! Ahora puedes llamar a tu función mediante la interfaz de usuario, curl, tu propio código de aplicación independiente o la herramienta faas-cli. El mandato curl es el siguiente (es posible que tengas que ajustar el puerto):


```
$ curl --data "Hello!" http://127.0.0.1:8080/function/facesdetection-python 
Input: ¡Hola!
```

Ya tienes tu primera función implementada en OpenFaaS. Esta función devuelve cualquier mensaje que reciba como entrada (según el código de `handler.py`). Ahora tienes que modificar el código para que realmente detecte caras. 



### Dectección de caras con Python


Para el diseño de la función de reconocimiento facial, puedes utilizar modelos preentrenados que te permiten realizar la detección sin tener que crear un modelo desde cero.

El pseudocódigo de la función podría tener el siguiente aspecto:

```
def function(input_URL)
  model=load_faces_models
  image=read_image_from_URL
  faces=detect_faces(model,image)
  imagefaces=add_detection_frames_to_the_original_image(faces, image)
  return imagefaces or save_image(output_URL)  
```

A continuación se muestra un ejemplo de código que detecta caras utilizando un clasificador preentrenado llamado `haarcascade_frontalface_default.xml` procedente del [proyecto OpenCV](https://github.com/opencv/opencv/tree/4.x/data/haarcascades):

```
import cv2

# Load the cascade Classifier
face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades +'haarcascade_frontalface_default.xml')
# Read the input image
img = cv2.imread('test.jpg')
# Convert into grayscale
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
# Detect faces
faces = face_cascade.detectMultiScale(gray, 1.1, 4)
# Draw rectangle around the faces
for (x, y, w, h) in faces:
    cv2.rectangle(img, (x, y), (x+w, y+h), (255, 0, 0), 2)
# Display the output
cv2.imshow('img', img)
```

**Debes adaptar este código para que acepte la URL de una imagen, realice la detección de rostros en la imagen y el resultado se envíe al usuario o se guarde en el servicio para que pueda descargarse o visualizarse. Cualquier modificación destinada a mejorar este código y la precisión de la detección de rostros se tendrá en cuenta a la hora de evaluar la práctica.** 

## Alternativas al reconocimiento facial

Si te interesa montar una aplicación diferente como servicio con OpenFaaS, envía tu propuesta al profesor para que la apruebe. Una alternativa muy popular en este momento es la segmentación de imágenes como FaaS con el [Segment Anything Model by META](https://github.com/facebookresearch/segment-anything), por ejemplo, pero no dudes en sugerir cualquier otra aplicación útil para tu contexto académico, de investigación o profesional en la que te gustaría trabajar. 


## Práctica evaluable
Como parte de las actividades evaluables para la parte práctica de la asignatura es necesario realizar las tareas un conjunto de tareas y entregar documentación. Se detalla en las subsecciones que siguen.

### Tareas a realizar

1. Desarrollo y descripción de los pasos para configurar la plataforma del servicio de funciones FaaS en OpenFaas (si prefieres otra plataforma FaaS ya existente, informa al profesor).
2. Implementación de la función de detección de caras en el lenguaje seleccionado (Python, Node.js, etc.). Cualquier mejora de la función básica de detección de rostros proporcionada deberá describirse y se tendrá en cuenta para la evaluación del trabajo práctico. 
3. Despliegue de la función implementada dentro de la plataforma OpenFaaS seleccionada.
4. Realizar un programa de prueba que use la función desplegada.

## Documentación a elaborar y entregar

Una vez realizadas las tareas previas, el alumno elaborará un informe con la siguiente estructura y contenidos:

1. Portada con nombre del curso, nombre de la práctica, nombre completo del alumno y dirección de correo electrónico.
2. Índice de contenidos del documento.
3. Una sección por cada una de las tareas indicadas en la [sección previa](#tareas-a-realizar). Cada sección describirá el objetivo e incluirá el listado de los ficheros generados, explicando su contenido. También se incluirán capturas de pantalla mostrando evidencias de la ejecución del proceso. Si has realizado alguna mejora o extensión, explícala.
4. Conclusiones derivadas del trabajo.
5. Referencias utilizadas en la realización del trabajo y confección del informe.

## Entrega del material
El informe completo, en formado pdf, junto con los ficheros generados, debidamente organizados en una jerarquía de carpetas se empaquetarán en un fichero .zip, que será entregado en actividad correspondiente de Prado, en la página del curso.

Fecha límite de entrega: 23:59:00h de **12 de junio de 2026**.


## Referencias 

- [Ejemplos de FaaS e implementación de funciones](https://github.com/openfaas/faas/tree/master/sample-functions)
- [OpenCV](https://github.com/opencv/opencv)
