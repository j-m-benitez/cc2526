# Funciones como servicio, Function-as-a-Service (FaaS)


## Introducción a Funcón como servicio, Function as a Service 


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
- Algoritmia
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



## ¿Cuál es la arquitectura sin servidor de FaaS?

La arquitectura sin servidor es un concepto mucho más amplio que el FaaS. La computación sin servidor ofrece una serie de ventajas con respecto a la infraestructura tradicional basada en la nube o centrada en servidores. Para muchos desarrolladores, las arquitecturas sin servidor ofrecen una mayor escalabilidad, más flexibilidad y un tiempo de lanzamiento más rápido, todo ello a un coste reducido. Con las arquitecturas sin servidor, los desarrolladores no tienen que preocuparse por la adquisición, el aprovisionamiento y la gestión de los servidores de backend. Sin embargo, la computación sin servidor no es la solución milagrosa para todos los desarrolladores de aplicaciones web.

![FaaS](https://cf-assets.www.cloudflare.com/slt3lc6tev37/7nyIgiecrfe9W6TfmJRpNh/dfc5434659e31300d1918d4163dfb263/benefits-of-serverless.svg)

La computación sin servidor permite a los desarrolladores adquirir servicios de backend con una flexible modalidad de «pago por uso», lo que significa que solo tienen que pagar por los servicios que utilizan. Es como pasar de un plan de datos de telefonía móvil con un límite fijo mensual a uno en el que solo se cobra por cada byte de datos que se consume realmente.

El término «sin servidor» es algo engañoso, ya que sigue habiendo servidores que prestan estos servicios de backend, pero todas las cuestiones relacionadas con el espacio del servidor y la infraestructura las gestiona el proveedor. «Sin servidor» significa que los desarrolladores pueden realizar su trabajo sin tener que preocuparse en absoluto por los servidores.


### ¿Es la computación sin servidor adecuada para tu caso?

Los desarrolladores que deseen reducir el tiempo de comercialización y creación de aplicaciones ligeras y flexibles que puedan ampliarse o actualizarse rápidamente pueden beneficiarse enormemente de la computación sin servidor.

Las arquitecturas sin servidor reducirán el coste de las aplicaciones con un uso irregular, en las que los periodos de máxima actividad se alternan con momentos de poco o ningún tráfico. Para este tipo de aplicaciones, adquirir un servidor o un conjunto de servidores que estén constantemente en funcionamiento y siempre disponibles, incluso cuando no se utilizan, puede suponer un desperdicio de recursos. Una configuración sin servidor responderá al instante cuando sea necesario y no generará costes cuando esté inactiva.

Además, los desarrolladores que deseen acercar algunas o todas las funciones de su aplicación a los usuarios finales para reducir la latencia necesitarán, como mínimo, una arquitectura parcialmente sin servidor, ya que ello requiere trasladar algunos procesos fuera del servidor de origen.


### Ejemplos de servicios suministrados por un proveedor en uan arquitectura sin servidor

Dado que actualmente un gran número de cargas de trabajo se están trasladando a la nube, los proveedores de servicios en la nube deben ofrecer servicios de *backend* como:

- Configuración del equilibrador de carga
- Gestión de clústeres
- Sistema operativo para dar soporte a las cargas de trabajo, etc.

Estos se conocen como BaaS (Backend as a Service). Y la arquitectura sin servidor comprende FaaS y BaaS. Por ejemplo, en las bases de datos, muchos proveedores de soluciones BaaS ofrecen mecanismos de validación de datos para que una aplicación pueda utilizarlos en su backend para autenticarse en la base de datos. Aquí es donde entra en juego FaaS. Consideremos el caso en el que se inserta un nuevo registro en la base de datos. Mediante FaaS, se puede añadir una pequeña función dentro del contenedor de la aplicación, que se activa cuando se añade un nuevo registro a la base de datos. La arquitectura sin servidor hace que FaaS sea más fiable y asequible. Fomenta la tendencia a implementar elementos como servicios y a utilizar pasarelas API para asignar solicitudes HTTP a esas funciones.



## Platforms

### OpenFaaS

OpenFaaS&reg; makes it easy for developers to deploy event-driven functions and microservices to Kubernetes without repetitive, boiler-plate coding. Package your code or an existing binary in an OCI-compatible image to get a highly scalable endpoint with auto-scaling and metrics.


**Highlights**

* Ease of use through UI portal and *one-click* install
* Write services and functions in any language with [Template Store](https://www.openfaas.com/blog/template-store/) or a Dockerfile
* Build and ship your code in an OCI-compatible/Docker image
* Portable: runs on existing hardware or public/private cloud by leveraging [Kubernetes](https://github.com/openfaas/faas-netes)
* [CLI](http://github.com/openfaas/faas-cli) available with YAML format for templating and defining functions
* Auto-scales as demand increases [including to zero](https://docs.openfaas.com/architecture/autoscaling/)
* [Commercially supported distribution by the team behind OpenFaaS](https://openfaas.com/support/)

**Want to dig deeper into OpenFaaS?**

* Trigger endpoints with either [HTTP or events sources such as Apache Kafka and AWS SQS](https://docs.openfaas.com/reference/triggers/)
* Offload tasks to the built-in [queuing and background processing](https://docs.openfaas.com/reference/async/)
* Quick-start your Kubernetes journey with [GitOps from OpenFaaS Cloud](https://docs.openfaas.com/openfaas-cloud/intro/)
* Go secure or go home [with 5 must-know security tips](https://www.openfaas.com/blog/five-security-tips/)
* Learn everything you need to know to [go to production](https://docs.openfaas.com/architecture/production/)
* Integrate with Istio or Linkerd with [Featured Tutorials](https://docs.openfaas.com/tutorials/featured/#service-mesh)
* Deploy to [Kubernetes or OpenShift](https://docs.openfaas.com/deployment/)

## Overview of OpenFaaS (Serverless Functions Made Simple)

Conceptual architecture and stack, [more detail available in the docs](https://docs.openfaas.com/architecture/stack/)

This is the so-called PLONK Stack: 
- Prometheus
- Linux
- OpenFaaS
- NATS
- Kubernetes
- it also requires a Container Runtime and Conainer Registry such as Docker (but there is no letter in the name for this ;)

The following diagram represent the conceptual architecture for OpenFaaS: 

![PLONK STACK](https://blog.alexellis.io/content/images/2019/05/provider-1.png)



The core functionality provided by the OpenFaaS Gateway is to:

- Create, list, update and delete functions.
- Scale function replicas.
- Invoke a function.
- Query the health, metrics, and scaling status of functions. 
- Create, list and delete secrets.
- View the logs from functions.
- Queue-up asynchronous requests.

The three ways of interacting with the REST API tend to be:

- Using the CLI (`faas-cli`).
- Using the built-in UI.
- Or via the REST API directly from your application or via cURL.

All communication within OpenFaaS happens over HTTP using REST. This simple interface is made powerful when coupled with events and triggers.

![](https://docs.openfaas.com/images/connector-pattern.png)


### OpenFaaS and Kubernetes

Our bare essentials for Serverless on Kubernetes are:

- A container image with function code or an executable inside.
- A registry to host the container image.
- A Pod to run the container image.
- A Service to access the Pod.

Often, projects will add many more components on top of this stack, such as a UI, and API gateway, auto-scaling, APIs, and many more.


## OpenFaaS Installation instructions (not needed on the UGR server)

For installing OpenFaaS on top of a Kubernetes installation, we will proceed as follows: 

- Install minikube (See [Session 4](../session4)).
- Install [arkade](https://github.com/alexellis/arkade). 
- Install OpenFaaS on Kubernetes using arkade.

You might choose a different installation pipeline for your system. Check the OpenFaas website for installation manuals and instructions. 

In essence, if you have minikube working, the following to commands should install arkade and OpenFaaS:

```
curl -sLS https://get.arkade.dev | sudo sh
curl -SLsf https://cli.openfaas.com | sudo sh
```

### Installation of Arkade and OpenFaas

Arkade is an App installer for Kubernetes. It relies on Helm3 and Kubernetes, and eases and speeds up the installation of over 50 apps. 
We will use arkade to install OpenFaaS. 

To install and run arkade we first need to run minikube. 

Check [Session 4](../session4) how to run minikube on the UGR server, usually it can be started just with the following: 
```
minikube start
```

Then, we install openfaas using arkade: 
```
arkade install openfaas
```

If you are on the university server with other users also installing it and you run into an error, you can try to use your own temp directory for installation that may fix it, with:

```
TMPDIR="$HOME/.local/tmp" arkade install openfaas
```

After the installation has completed, you will receive the commands you need to run, to log in and access the OpenFaaS Gateway service in Kubernetes.

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

You can get this message again at any time with ``arkade info openfaas``.

The `kubectl rollout status` command checks that all the containers in the core OpenFaaS stack have started and are healthy.

The `kubectl port-forward` command securely forwards a connection to the OpenFaaS Gateway service within your cluster to your laptop on port 8080. It will remain open for as long as the process is running, so if it appears to be inaccessible later on, just run this command again.

The `faas-cli login` command and preceding line populate the PASSWORD environment variable. You can use this to get the password to open the UI at any time.

We then have `faas-cli store deploy figlet` and `faas-cli list`. The first command deploys an ASCII generator function from the Function Store and the second command lists the deployed functions, you should see `figlet` listed.

You will also find the PLONK stack components deployed, such as Prometheus and NATS. You can see them in the openfaas Kubernetes namespace:

```
kubectl get deploy --namespace openfaas

NAME           READY   UP-TO-DATE   AVAILABLE   AGE
alertmanager   1/1     1            1           1m
gateway        1/1     1            1           1m
nats           1/1     1            1           1m
prometheus     1/1     1            1           1m
queue-worker   1/1     1            1           1m
```

**In particular, on the UGR server, you can get things up and running with the following. You need to replace 25146 with one of your assigned ports. The last command will set up and print the admin password for login into the OpenFaaS gateway. Copy this password to use it to log into the UI.**

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

Now you can open a browser to ``http://<put_server_name_here>:25146/ui/`` and log in using username ``admin`` and the password you just copied. 

![](OpenFaaSGateway.png)


## Your first OpenFaaS function

With OpenFaaS you can define functions either via a CLI or via the UI of the OpenFaaS gateway. For creating a new FaaS, you can click on the *Deploy New Function* button on the menu in the left for the UI. Go to the *Custom* tab and copy the data from the screenshot below

![](custom-deploy-ui.png)

Click *deploy*. 

Select the `print-env` function from the left panel and click the `INVOKE` button.  

![](env-invoke.png)

The top of the UI shows the URL you can use to invoke the function from a browser or using curl: 

```
curl -sL http://127.0.0.1:8080/function/print-env
```

The `Invocation Count` is the global count of invocations read from the built-in Prometheus time-series. **The *Function process* is the actual command being called when the function is invoked (you can change it)**. 


### Exercise

Create a function called `print-cal` that runs the `cal` command to print a calendar: 

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


### Stop the service

From the arkade help: 
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

This will stop the FaaS service. 

### The CLI
The CLI for OpenFaaS (`faas-cli`) is written in Golang and acts as an HTTP client to the OpenFaaS Gateway component.

You can get a complete list of commands with `faas-cli --help`.

Search and deploy pre-made functions from the Function Store or find a function template for your specific language:

- `faas-cli store list`
- `faas-cli store deploy`
- `faas-cli template store list`
- `faas-cli template store pull`

Create, build, and publish a function followed by deploying it to your cluster:

- `faas-cli new`
- `faas-cli build`
- `faas-cli push`
- `faas-cli deploy`

List, inspect, invoke, and troubleshoot your functions:

- `faas-cli list`
- `faas-cli describe`
- `faas-cli invoke`
- `faas-cli logs`

Authenticate to the CLI, and create secrets for your functions:

- `faas-cli login`
- `faas-cli secret`

For each command, you can get more information with `faas-cli COMMAND --help` to see example usage and the various flags that are allowed. You can also find help for some of the commands in the OpenFaaS documentation.

We will continue the CLI explanation with more examples in our second assignment: [Practice 2](../practice2).

### Code samples

You can generate new functions using the `faas-cli` and built-in templates or use any binary for Windows or Linux in a container.

Official templates exist for many popular languages and are easily extensible with Dockerfiles. Explore these options with `faas-cli store` and `faas-cli template store`. These are some of the available function templates for different programming languages (the repo id is specified in each function): 

* Node.js (`node12`) example:

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

* Python 3 example:

    ```python
	import requests

	def handle(req):
	    r =  requests.get(req, timeout = 1)
	    return "{} => {:d}".format(req, r.status_code)
    ```
    *handler.py*

* Golang example (`golang-http`)

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


# References
- OpenFaaS official training materials (https://docs.openfaas.com/tutorials/training)
- EdX Course by the Linux Foundation: Serverless, FaaS with OpenFaaS and Kubernetes: (https://learning.edx.org/course/course-v1:LinuxFoundationX+LFS157x+1T2022)









