# Práctica 2

Implementar XXX usando Functions-as-a-Service


## Objetivos de la práctica: 

- Instalar e implementar una herramienta de orquestación de contenedores: Kubernetes.
- Implementar la funcionalidad del catálogo de funciones y del servicio de funciones basado en OpenFaaS.
- Implementar diferentes funciones disponibles para FaaS destinadas a XXX.
- Implementar una función escalable que sirva de componente para la identificación biométrica de usuarios a partir de imágenes faciales. 

La idea principal de la práctica es crear una o más funciones que permitan:

- Capturar/recoger una imagen (por ejemplo, desde una URL) como entrada a la función.
- La función debe detectar los rostros que aparecen.
- La función debe devolver la imagen con los rostros detectados enmarcados en un rectángulo.


## Software de base (no necesario en el servidor de UGR)

Para ello, necesitarás instalar las siguientes plataformas o herramientas:

- Instala Kubernetes (por ejemplo, Minikube). Encontrarás instrucciones detalladas en [Sesión 4](../session4/)  
- Instala una plataforma RAS: OpenFaaS sobre Kubernetes. Encontrarás instrucciones detalladas en [Sesión 7](../session7/)
  
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

Aquí tienes las URL de las dos funciones. También puedes verlas ahora en la interfaz gráfica de usuario del portal de OpenFaaS: `http://127.0.0.1:8080/ui/`. **En el servidor de la UGR, reemplaza esto con el URL y puertos correctos, tal y como se explicó en [Sesión 7](../session7/)**

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



## Developing your own functions for FaaS

**For function development you can choose any language that is supported by the functions-as-a-service platform. If you want to use another language for function design, you will need to implement the function from a container.**

If you are using node.js, java, or other languages, check the examples [here.](https://github.com/openfaas/faas/tree/master/sample-functions)

## My first function (template with Python)

Specifically for the OpenFaaS platform example, the function would be created in this way:

```
$ faas-cli new --lang python3-http facesdetection-python
```

This creates the following files for you:

```
stack.yaml
facesdetection-python/handler.py
facesdetection-python/handler_test.py
facesdetection-python/requirements.txt
facesdetection-python.yml
```

The `handler.py` file contains your code that responds to a function invocation:
```
def handle(event, context):
    return {
        "statusCode": 200,
        "body": "Hello from OpenFaaS!"
    }
```
You can edit the `handler.py` file to the following so that it will print back the request to the user:

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


The `requirements.txt` file can be used to install pip modules at build time. Pip modules add support for add-ons like MySQL or Numpy (machine learning).


The `stack.yaml` contains information on how to build and deploy your function:

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


The main fields we want to study here are:

- `gateway`: edit the port accordingly if needed
- `lang`: The name of the template to build with.
- `handler`: The folder (not the file) where the handler code is to be found.
- `image`:  The Docker image name to build with its appropriate prefix for use with docker build / push, more information below. It is recommended that you change the tag on each change of your code, but you can also leave it as latest.

You can largely ignore the `provider` fields, which are optional, but they do allow you to hard-code an alternative gateway address other than the default.

The community version of OpenFaaS that we use only supports public images, so you'll need to publish your image to a place like dockerhub. For that, you should create an account at `https://hub.docker.com/` then, you replace `yourRegistryPrefixWillBeHere` in `stack.yaml` with your username from dockerhub. You then will need to log in to dockerhub with the following.

```
docker login --username <your username here> --password <your password here>
```


Now, there are three parts to getting your function up and running both initially and for updating. Run the following from the folder where `stack.yaml` is located:

- `faas-cli build`: Create a local container image, and install any other files needed, like those in the requirements.txt file.
- `faas-cli push`: Transfer the function’s container image from our local Docker library up to the hosted registry.
- `faas-cli deploy`: Using the OpenFaaS REST API, create a Deployment inside the Kubernetes cluster and a new Pod to serve traffic.

All of those commands can be combined with the `faas-cli up` command for brevity both initially and for updating.

```
$ faas-cli up -f stack.yaml
```

Wait a few moments, and then you will see a URL printed `http://127.0.0.1:8080/function/facesdetection-python`

That’s it! You can now invoke your function using the UI, curl, your own separate application code, or the faas-cli. The curl command is as follows (you may need to adjust the port):

```
$ curl --data "Hello!" http://127.0.0.1:8080/function/facesdetection-python 
Input: Hello!
```

Now you have your first function deployed to OpenFaaS. This function echoes whatever message it gets as input (according to the code in `handler.py`). Now you need to change the code so it actually detects faces. 


### Face detection function with Python

For the design of the face recognition function you can use pre-trained models that allow you to do the detection without having to create a model from scratch. 

The pseudocode function could look like the following:

```
def function(input_URL)
  model=load_faces_models
  image=read_image_from_URL
  faces=detect_faces(model,image)
  imagefaces=add_detection_frames_to_the_original_image(faces, image)
  return imagefaces or save_image(output_URL)  
```

The following is an example of code that detects faces using a pretrained classifier called `haarcascade_frontalface_default.xml` from the [OpenCV project](https://github.com/opencv/opencv/tree/4.x/data/haarcascades):

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

**You should adapt this code so it takes the URL of an image, performs the face detection on the image and the result is sent to the user or saved in the service so that it can be downloaded or viewed. Any modification to improve this code and the face detection precision will be taken into account for the evaluation of the assignment.** 

## Alternatives to Face Recognition

If you are interested in a different application to be provided as a service with OpenFaaS, please suggest your proposed application to the teacher for his approval. A hot alternative right now is Image Segmentation as FaaS with the [Segment Anything Model by META](https://github.com/facebookresearch/segment-anything), for example, but feel free to suggest any other useful application for your academic/research/professional context you would like to work on. 


##  Delivery of practice

The delivery of the practice consists of 3 parts:

1. Development and description of the steps to set up the platform for the FaaS functions service in OpenFaas (if you prefer some other existing platform for FaaS, please inform the teacher).
2. Implementation of the face detection function in the selected language (python, node.js, etc.). Any improvements of the basic face detection function provided should be described and will be taken into account for the evaluation of the practice. 
3. Steps for the deployment of the implemented function within the selected OpenFaaS platform.

All these steps must be documented in the delivery of the practice. For the delivery, all the material must be packaged in a zip file and uploaded to PRADO by the  deadline set. 

The zip file must contain the following:

- Platform deployment material
- Implemented functions with a detailed description of any improvements you added to the basic code to achieve a better  performance. 
- Script to deploy the function on OpenFaaS.

Deadline for submission: 24-April-2025 23:59:00


## References 

- FaaS Examples and Function deployment: https://github.com/openfaas/faas/tree/master/sample-functions
- OpenCV: https://github.com/opencv/opencv
