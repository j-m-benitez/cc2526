# Funciones como servicio, Function-as-a-Service (FaaS)


## Introducción a Función como servicio, Function-as-a-Service 


Como antesala a la presentación de "Funciones como servicio", Function-as-a-Service (FaaS), es interesante ver el recorrido: servidores físicos > máquinas virtuales > contenedores > funciones.

Anteriormente, se utilizaban servidores físicos para ejecutar aplicaciones. Aunque ofrecían un buen rendimiento, estaban diseñados para soportar únicamente el funcionamiento de aplicaciones específicas. Si se ejecutaba otra aplicación en ellos, esto afectaba a los flujos de trabajo de ambas. El auge de las máquinas virtuales en 2001 permitió transferir recursos específicos de aplicaciones de máquinas físicas a instancias de máquinas virtuales. Esto redujo las preocupaciones relacionadas con la infraestructura de aplicaciones concretas en servidores físicos. Posteriormente llegaron los contenedores Docker, que proporcionaron una alternativa ligera a las máquinas virtuales al encapsular únicamente la aplicación y sus dependencias específicas dentro de un contenedor. 

Las funciones hacen que el proceso del desarrollador sea aún más autónomo. Son fragmentos de código dentro del contenedor. Se ejecutan en función de determinados eventos. 

Por ejemplo, se puede crear una función que genere una base de datos y, al crearla, la rellene con valores. Sin funciones, los desarrolladores tendrían que esperar a que se creara la base de datos y, a continuación, actualizarla manualmente.


## ¿Qué es Function-as-a-Service (FaaS)?

«Function as a Service» es un modelo de ejecución de servicios en la nube que utiliza funciones para este fin. Como se ha mencionado anteriormente, una función es un fragmento de código de lógica de negocio, más concretamente, que se activa en respuesta a eventos. Esto significa que se activa y realiza su tarea cuando se produce un evento concreto, hasta que la tarea se completa. Las funciones pueden ser de diversos tipos, tales como:

- Función para procesar una solicitud web
- Función para cualquier tarea programada
- Función que se ejecuta manualmente.

Además, también podemos encadenar funciones, lo que significa que una función concreta, al completarse, puede activar la ejecución de otra función. Por ejemplo, una función para solicitudes web, al completarse, puede activar cualquier función de tarea programada. De esta forma, el proceso se vuelve más autónomo.

En resumen, FaaS es un caso particular de computación sin servidor (*serverless*) para la ejecución de fragmentos de código modulares. FaaS permite a los desarrolladores escribir y actualizar un fragmento de código sobre la marcha, que luego se puede ejecutar en respuesta a un evento, como el clic de un usuario en un elemento de una aplicación web. Esto facilita la escalabilidad del código y es una forma rentable de implementar microservicios.


### ¿Cuáles son las ventajas de usar FaaS?

*Mayor rapidez en el desarrollo*: Con FaaS, los desarrolladores pueden dedicar más tiempo a escribir la lógica de la aplicación y menos a preocuparse por los servidores y la implementación. Esto suele traducirse en un ciclo de desarrollo mucho más rápido.

*Escalabilidad integrada*: Dado que el código FaaS es intrínsecamente escalable, los desarrolladores no tienen que preocuparse por imprevistos debidos a un tráfico elevado o un uso intensivo. El proveedor de servicios sin servidor se encargará de todos los aspectos relacionados con la escalabilidad.

*Eficiencia de costes*: A diferencia de los proveedores de nube tradicionales, los proveedores de FaaS sin servidor no cobran a sus clientes por el tiempo de computación inactivo. Por ello, los clientes solo pagan por el tiempo de computación que utilizan y no tienen que malgastar dinero en un aprovisionamiento excesivo de recursos en la nube.


### ¿Cuáles son las deventajas de FaaS?

*Menor control del sistema*: el hecho de que un tercero gestione parte de la infraestructura dificulta la comprensión del sistema en su conjunto y complica la depuración.

*Mayor complejidad en las pruebas*: puede resultar muy difícil integrar el código FaaS en un entorno de pruebas local, lo que convierte las pruebas exhaustivas de una aplicación en una tarea más laboriosa.

### Proveedores de Function-as-a-Service

- Microsoft Azure
- Amazon Web Services (AWS)
- Cloud Functions
- IBM functions
- Algorithmia
- ...

### Ejemplo
A continuación se muestra un fragmento de una función en Azure:

```
Using System.Net; 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) {     
  log.Info("C# HTTP trigger function processed a request.");     
  // Get request body     
  dynamic data = await req.Content.ReadAsAsync<object>();     
  return req.CreateResponse(HttpStatusCode.OK, "Hello " + data.name);
 }
 ```
 

## ¿Cómo opera Function-as-a-Service?

Cualquiera que desee beneficiarse de las ventajas de FaaS debe recurrir a un proveedor de servicios en la nube para implementar FaaS.

- En el modelo FaaS, los desarrolladores no tienen que preocuparse por la infraestructura ni por los aspectos informáticos relacionados con el servicio, sino que se centran únicamente en escribir funciones.
- Cuando se invocan estas funciones, el proveedor de la nube activa el servidor, y una vez ejecutada con éxito la función, el servidor se apaga.
- Estos servidores se activan bajo demanda cuando se invoca la función y se apagan una vez que esta se ha ejecutado. De este modo, también se ahorran costes a los suscriptores de los servicios en la nube.



## ¿Cuál es la arquitectura "sin servidor" de FaaS?

La computación sin servidor es un concepto mucho más amplio que el FaaS. Ofrece una serie de ventajas con respecto a la infraestructura tradicional basada en la nube o centrada en servidores. Para muchos desarrolladores, las arquitecturas sin servidor ofrecen una mayor escalabilidad, más flexibilidad y un tiempo de lanzamiento más rápido, todo ello a un coste reducido. Con las arquitecturas sin servidor, los desarrolladores no tienen que preocuparse por la adquisición, el aprovisionamiento y la gestión de los servidores de backend. Sin embargo, la computación sin servidor no es la solución milagrosa para todos los desarrolladores de aplicaciones web.

![FaaS](https://cf-assets.www.cloudflare.com/slt3lc6tev37/7nyIgiecrfe9W6TfmJRpNh/dfc5434659e31300d1918d4163dfb263/benefits-of-serverless.svg)

La computación sin servidor permite a los desarrolladores adquirir servicios de backend con una flexible modalidad de «pago por uso», lo que significa que solo tienen que pagar por los servicios que utilizan. Es como pasar de un plan de datos de telefonía móvil con un límite fijo mensual a uno en el que solo se cobra por cada byte de datos que se consume realmente.

El término «sin servidor» es algo engañoso, ya que sigue habiendo servidores (y toda la infraestructura asociada, como, servidores operativos, software de sistema, comunicación y almacenamiento) que prestan estos servicios, pero todas las cuestiones de operación y gestión de la infraestructura las gestiona el proveedor. «Sin servidor» significa que los desarrolladores pueden realizar su trabajo sin tener que preocuparse en absoluto por los servidores.


### ¿Es la computación sin servidor adecuada para tu caso?

Los desarrolladores que deseen reducir el tiempo de comercialización y creación de aplicaciones ligeras y flexibles que puedan ampliarse o actualizarse rápidamente pueden beneficiarse enormemente de la computación sin servidor.

Las arquitecturas sin servidor reducirán el coste de las aplicaciones con un uso irregular, en las que los periodos de máxima actividad se alternan con momentos de poco o ningún tráfico. Para este tipo de aplicaciones, adquirir un servidor o un conjunto de servidores que estén constantemente en funcionamiento y siempre disponibles, incluso cuando no se utilizan, puede suponer un desperdicio de recursos. Una configuración sin servidor responderá en el instante en que sea necesario y no generará costes cuando esté inactiva.

Además, los desarrolladores que deseen acercar algunas o todas las funciones de su aplicación a los usuarios finales para reducir la latencia necesitarán, como mínimo, una arquitectura parcialmente sin servidor, ya que ello requiere trasladar algunos procesos fuera del servidor de origen.


### Ejemplos de servicios suministrados por un proveedor en uan arquitectura sin servidor

Dado que actualmente un gran número de cargas de trabajo se están trasladando a la nube, los proveedores de servicios en la nube suelen ofrecer servicios de *backend* como:

- Configuración del equilibrador de carga
- Gestión de clústeres
- Sistema operativo para dar soporte a las cargas de trabajo, etc.

Estos se conocen como BaaS (Backend as a Service). Y la arquitectura sin servidor comprende FaaS y BaaS. Por ejemplo, en las bases de datos, muchos proveedores de soluciones BaaS ofrecen mecanismos de validación de datos para que una aplicación pueda utilizarlos en su backend para autenticarse en la base de datos. Aquí es donde entra en juego FaaS. Consideremos el caso en el que se inserta un nuevo registro en la base de datos. Mediante FaaS, se puede añadir una pequeña función dentro del contenedor de la aplicación, que se activa cuando se añade un nuevo registro a la base de datos. La arquitectura sin servidor hace que FaaS sea más fiable y asequible. Fomenta la tendencia a implementar elementos como servicios y a utilizar pasarelas API para asignar solicitudes HTTP a esas funciones.



## Plataformas

### OpenFaaS

[OpenFaaS](https://www.openfaas.com/)® facilita a los desarrolladores la implementación de funciones y microservicios basados en eventos en Kubernetes sin necesidad de escribir código repetitivo y estándar. Empaqueta tu código o un binario existente en una imagen compatible con OCI para obtener un punto de acceso altamente escalable con autoescalado y métricas.


**Puntos destacados**

* Facilidad de uso gracias al portal de interfaz de usuario y a la instalación *con un solo clic*
* Escribe servicios y funciones en cualquier lenguaje con [Template Store](https://www.openfaas.com/blog/template-store/) o un Dockerfile
* Compila y distribuye tu código en una imagen compatible con OCI/Docker
* Portabilidad: se ejecuta en hardware existente o en la nube pública/privada aprovechando [Kubernetes](https://github.com/openfaas/faas-netes)
* [CLI](http://github.com/openfaas/faas-cli) disponible con formato YAML para crear plantillas y definir funciones
* Se autoescala a medida que aumenta la demanda [incluso hasta cero](https://docs.openfaas.com/architecture/autoscaling/)
* [Distribución con soporte comercial por parte del equipo detrás de OpenFaaS](https://openfaas.com/support/)
  
**¿Quieres profundizar en OpenFaaS?**

* Activa los endpoints mediante [HTTP o fuentes de eventos como Apache Kafka y AWS SQS](https://docs.openfaas.com/reference/triggers/)
* Descarga tareas al [sistema integrado de colas y procesamiento en segundo plano](https://docs.openfaas.com/reference/async/)
* Empieza rápidamente tu andadura con Kubernetes con [GitOps de OpenFaaS Cloud](https://docs.openfaas.com/openfaas-cloud/intro/)
* Apuesta por la seguridad o vete a casa [con 5 consejos de seguridad imprescindibles](https://www.openfaas.com/blog/five-security-tips/)
* Aprende todo lo que necesitas saber para [pasar a producción](https://docs.openfaas.com/architecture/production/)
* Integra Istio o Linkerd con [tutoriales destacados](https://docs.openfaas.com/tutorials/featured/#service-mesh)
* Implementa en [Kubernetes u OpenShift](https://docs.openfaas.com/deployment/)

## Descripción general de OpenFaaS (Serverless Functions Made Simple)

Arquitectura conceptual en forma de pila tecnológica ([más detalle en la documentación](https://docs.openfaas.com/architecture/stack/))

Lo que se conoce como la pila PLONK: 
- Prometheus
- Linux
- OpenFaaS
- NATS
- Kubernetes
- Y, por supuesto, un ejecutor y registro de contenedores como Docker, (aunque no haya una letra en el nombre para este componente ;)

El siguiente diagrama representa la arquitectura conceptual de OpenFaaS: 

![PLONK STACK](https://blog.alexellis.io/content/images/2019/05/provider-1.png)

Las funciones principales que ofrece OpenFaaS Gateway son las siguientes:

- Crear, listar, actualizar y eliminar funciones.
- Escalar réplicas de funciones.
- Invocar una función.
- Consultar el estado, las métricas y el estado de escalado de las funciones.
- Crear, listar y eliminar secretos.
- Ver los registros de las funciones.
- Poner en cola solicitudes asíncronas.

Las tres formas de interactuar con la API REST suelen ser:

- Utilizando la CLI (`faas-cli`).
- Utilizando la interfaz de usuario integrada.
- O a través de la API REST directamente desde su aplicación o mediante cURL.

Toda la comunicación dentro de OpenFaaS se realiza a través de HTTP utilizando REST. Esta sencilla interfaz se vuelve muy potente cuando se combina con eventos y disparadores.

![](https://docs.openfaas.com/images/connector-pattern.png)


### OpenFaaS y Kubernetes

Los elementos básicos para la arquitectura sin servidor en Kubernetes son:

- Una imagen de contenedor que contenga el código de la función o un ejecutable.
- Un registro para alojar la imagen de contenedor.
- Un pod para ejecutar la imagen de contenedor.
- Un servicio para acceder al pod.

A menudo, los proyectos añaden muchos más componentes a esta pila, como una interfaz de usuario, una puerta de enlace de API, escalado automático, API y muchos otros.

## Instrucciones de instalación de OpenFaaS 

Para instalar OpenFaaS sobre una instalación de Kubernetes, procederemos de la siguiente manera: 

- Instala minikube (consulta la [Sesión 4](../session4)).
- Instala [arkade](https://github.com/alexellis/arkade). 
- Instala OpenFaaS en Kubernetes utilizando arkade.

Es posible que elijas un proceso de instalación diferente para tu sistema. Consulta el sitio web de OpenFaaS para obtener manuales e instrucciones de instalación. 

En esencia, si tienes minikube en funcionamiento, los siguientes comandos deberían instalar arkade y OpenFaaS:


```
curl -sLS https://get.arkade.dev | sudo sh
curl -SLsf https://cli.openfaas.com | sudo sh
```

### Instalación de Arkade y OpenFaaS

Arkade es un instalador de aplicaciones para Kubernetes. Se basa en Helm3 y Kubernetes, y facilita y agiliza la instalación de más de 50 aplicaciones. 
Utilizaremos Arkade para instalar OpenFaaS. 

Para instalar y ejecutar Arkade, primero debemos ejecutar Minikube. 

Consulte la [Sesión 4](../session4) para saber cómo ejecutar Minikube en el servidor de la UGR. Normalmente, se puede iniciar simplemente con lo siguiente: 
```
minikube start
```
Pero puede ser conveniente detallar opciones de configuración (de recursos o gestores de contenedores):
```
minikube start \
    --driver=podman \
    --cpus=2 \
    --memory=4096 \
    --disk-size=20g \
    --container-runtime=crio
```

A continuación, instalamos openfaas usando arkade: 
```
arkade install openfaas
```

Este paquete ya está instalado en el servidr y disponible para todos los usuarios, por tanto, no necesitas hacerlo en ese servidor. Sí habrás de hacerlo si estás en un equipo distinto como, por ejemplo, tu ordenador personal. Si hubiese conflictos en el uso de la instalación global del servidor de UGR, puedes hacer una instalación en tu cuenta personal así:

```
TMPDIR="$HOME/.local/tmp" arkade install openfaas
```

Una vez finalizada la instalación, recibirás los comandos que debes ejecutar para iniciar sesión y acceder al servicio OpenFaaS Gateway en Kubernetes.

```
Info for app: openfaas 
# Get the faas-cli 
curl -SLsf https://cli.openfaas.com | sudo sh

# Forward the gateway to your machine 
kubectl rollout status -n openfaas deploy/gateway 
kubectl port-forward -n openfaas svc/gateway 8080:8080 &

# If basic auth is enabled, you can now log into your gateway: 
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o 
jsonpath="{.data.basic-auth-password}" | base64 --decode; echo) 
echo -n $PASSWORD | faas-cli login --username admin --password-stdin

faas-cli store deploy figlet 
faas-cli list
```

Puedes volver a obtener este mensaje en cualquier momento con ``arkade info openfaas``.

El mandato `kubectl rollout status` comprueba que todos los contenedores de la pila principal de OpenFaaS se hayan iniciado y estén en buen estado.

El mandato `kubectl port-forward` reenvía de forma segura una conexión al servicio OpenFaaS Gateway dentro de tu clúster a tu ordenador portátil en el puerto 8080. Permanecerá abierta mientras el proceso esté en ejecución, por lo que, si más adelante parece inaccesible, solo tienes que volver a ejecutar este mandato.

La orden `faas-cli login` y la línea anterior rellenan la variable de entorno PASSWORD. Puedes utilizarla para obtener la contraseña y abrir la interfaz de usuario en cualquier momento.

A continuación, tenemos `faas-cli store deploy figlet` y `faas-cli list`. El primer mandato implementa una función generadora de ASCII desde el Function Store y el segundo mandato muestra una lista de las funciones implementadas; deberías ver `figlet` en la lista.

También encontrarás los componentes de la pila PLONK implementados, como Prometheus y NATS. Puedes verlos en el espacio de nombres de Kubernetes de openfaas:

```
kubectl get deploy --namespace openfaas

NAME           READY   UP-TO-DATE   AVAILABLE   AGE
alertmanager   1/1     1            1           1m
gateway        1/1     1            1           1m
nats           1/1     1            1           1m
prometheus     1/1     1            1           1m
queue-worker   1/1     1            1           1m
```

**En concreto, en el servidor de la UGR, puedes ponerlo todo en marcha con la siguiente orden. Debes sustituir 25146 por uno de los puertos que se te hayan asignado. El último comando configurará y mostrará la contraseña de administrador para iniciar sesión en la pasarela de OpenFaaS. Copia esta contraseña para utilizarla al iniciar sesión en la interfaz de usuario.**

```
minikube tunnel --bind-address=0.0.0.0 &
```

```
kubectl port-forward -n openfaas --address 0.0.0.0 svc/gateway 25146:8080 &
```

```
export OPENFAAS_URL=http://127.0.0.1:25146
```

```
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
```

```
echo -n $PASSWORD | faas-cli login --username admin --password-stdin
```

```
echo -n $PASSWORD
```

Ahora puedes abrir un navegador y acceder a ``http://<introduce_el_nombre_del_servidor_aquí>:25146/ui/`` e iniciar sesión con el nombre de usuario ``admin`` y la contraseña que acabas de copiar. 
Alternativamente, puede ser cómodo crear un tunel ssh desde tu ordenador hasta el servidor, por ejemplo:

```
ssh -L 8080:localhost:25146 usuario@ip-del-servidor
```
y, luego lanzar un navegador conectándote al puerto local:

```
http://localhost:8080
```

![](OpenFaaSGateway.png)


## Tu primera función OpenFaaS

Con OpenFaaS puedes definir funciones tanto a través de la CLI como de la interfaz de usuario de la pasarela de OpenFaaS. Para crear una nueva función FaaS, haz clic en el botón *Deploy New Function* del menú de la izquierda de la interfaz de usuario. Ve a la pestaña *Custom* y copia los datos de la captura de pantalla que aparece a continuación
![](custom-deploy-ui.png)

Click *deploy*. 

Selecciona la función `print-env` en el panel de la izquierda y haz clic en el botón `INVOKE`.  

![](env-invoke.png)

En la parte superior de la interfaz de usuario aparece la URL que puedes utilizar para llamar a la función desde un navegador o mediante curl: 

```
curl -sL http://127.0.0.1:8080/function/print-env
```

El «Invocation Count» es el recuento global de invocaciones que se lee de la serie temporal integrada de Prometheus. **El *Function process* es el mandto concreto que se ejecuta cuando se invoca la función (se puede modificar)**. 



### Ejercicio

Crea una función llamada `print-cal` que ejecute el comando `cal` para mostrar un calendario: 
```
$ curl -sL http://127.0.0.1:8080/function/print-cal
Handling connection for 8080
     April 2023
Su Mo Tu We Th Fr Sa
                   1
 2  3  4  5  6  7  8
 9 10 11 12 13 14 15
16 17 18 19 20 21 22
23 24 25 26 27 28 29
30
```


### Detección del servicio

De la ayuda de arkade: 
```
Apps installed to Kubernetes can rarely be uninstalled in a single command 
and often leave clusters in an inconsistent state. Kubernetes does not 
track resources created by applications in the same way that something like 
Windows 10 or MacOS would do, so  it is often much easier for you to 
create a new cluster, than to remove an application.

With a tool like kind, you just run:

kind delete cluster; kind create cluster

Most arkade apps are installed with helm, so you can simply use helm to 
remove them, but beware that each project generally provides very specific 
guidance on how to clear up all resources that it may have created 
at installation time.

Get helm:
arkade get helm

List charts:
helm list --all-namespaces

Delete a chart:
helm delete -n openfaas openfaas

Delete any namespaces it created:
kubectl delete namespace openfaas openfaas-fn
```

Esto detendrá el servicio FaaS. 

### La interfaz de línea de órdenes (CLI)

La interfaz de línea de órdenes (CLI) de OpenFaaS (`faas-cli`) está escrita en Golang y actúa como cliente HTTP del componente OpenFaaS Gateway.

Puedes obtener una lista completa de mandatos con `faas-cli --help`.

Busca e implementa funciones ya creadas en el Function Store o encuentra una plantilla de función para tu lenguaje específico:

- `faas-cli store list`
- `faas-cli store deploy`
- `faas-cli template store list`
- `faas-cli template store pull`

Crea, compila y publica una función, y a continuación impleméntala en tu clúster:

- `faas-cli new`
- `faas-cli build`
- `faas-cli push`
- `faas-cli deploy`

Visualiza, revisa, ejecuta y soluciona problemas en tus funciones:

- `faas-cli list`
- `faas-cli describe`
- `faas-cli invoke`
- `faas-cli logs`

Inicia sesión en la CLI y crea claves secretas para tus funciones:

- `faas-cli login`
- `faas-cli secret`

FPara cada mandato, puedes obtener más información con `faas-cli COMANDO --help` para ver ejemplos de uso y los distintos parámetros permitidos. También puedes encontrar ayuda sobre algunos de los comandos en la documentación de OpenFaaS.

### Ejemplos de código

Puedes crear nuevas funciones utilizando `faas-cli` y las plantillas integradas, o bien utilizar cualquier binario para Windows o Linux en un contenedor.

Existen plantillas oficiales para muchos lenguajes populares y se pueden ampliar fácilmente con archivos Dockerfile. Explora estas opciones con `faas-cli store` y `faas-cli template store`. Estas son algunas de las plantillas de funciones disponibles para diferentes lenguajes de programación (el ID del repositorio se especifica en cada función): 

* Node.js (`node12`):

    ```js
	"use strict"

	module.exports = async (event, context) => {
	    return context
		.status(200)
		.headers({"Content-Type": "text/html"})
		.succeed(`
		<h1>
		    👋 Hello World 🌍
		</h1>`);
	}
    ```
    *handler.js*

* Python 3:

    ```python
	import requests

	def handle(req):
	    r =  requests.get(req, timeout = 1)
	    return "{} => {:d}".format(req, r.status_code)
    ```
    *handler.py*

* Golang (`golang-http`)

    ```golang
	package function

	import (
	    "fmt"
	    "net/http"

	    handler "github.com/openfaas/templates-sdk/go-http"
	)

	// Handle a function invocation
	func Handle(req handler.Request) (handler.Response, error) {
	    var err error

	    message := fmt.Sprintf("Body: %s", string(req.Body))

	    return handler.Response{
		Body:       []byte(message),
		StatusCode: http.StatusOK,
	    }, err
	}
    ```


# Referencias
- OpenFaaS official training materials (https://docs.openfaas.com/tutorials/training)
- EdX Course by the Linux Foundation: Serverless, FaaS with OpenFaaS and Kubernetes: (https://learning.edx.org/course/course-v1:LinuxFoundationX+LFS157x+1T2022)









