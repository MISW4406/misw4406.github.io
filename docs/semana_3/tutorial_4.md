---
title: Tutorial 4 - Sidecar, adaptadores y embajadores
layout: default
parent: Semana 3
nav_order: 1
---

# Tutorial 4 - Sidecar, adaptadores y embajadores

## Introducción
{: .no_toc }

Hasta ahora en la semana, hemos aprendido acerca de los diferentes tipos de patrones en sistemas distribuidos. Conocimos que la primera categoría es la llamada **Single-Node** y que de ella podemos encontrar los patrones: Sidecar, adaptador y embajador. En este tutorial, entraremos aún más en los detalles de implementación de dichos patrones, enfocándonos especialmente en los [adaptadores](https://learning.oreilly.com/library/view/kubernetes-patterns-2nd/9781098131678/ch08.html){:target="_blank"}. En la web usted podrá encontrar [varios ejemplos](https://www.weave.works/blog/kubernetes-patterns-the-adapter-pattern){:target="_blank"} *"Hello World"* acerca de los Sidecars, para el uso de [logging y monitoreo](https://sensu.io/resources/whitepaper/whitepaper-monitoring-kubernetes-the-sidecar-pattern){:target="_blank"}. Sin embargo, aunque dichos ejemplos siguen siendo relevantes, estos patrones pueden ser de aún mayor utilidad para desmantelar nuestro monolito. 

Por tal motivo, durante este tutorial también conoceremos acerca de [gRPC](https://grpc.io/){:target="_blank"} y [Protocol Buffers](https://developers.google.com/protocol-buffers/docs/proto3?hl=es-419){:target="_blank"} (Protobuf). Veremos en AeroAlpes un ejemplo en donde deseamos crear un adaptador gRPC para que servicios externos se comuniquen con AeroAlpes, sin que sea necesario hacer un refactoring en el código de AeroAlpes.

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Preparación

Haga un fork del template de [AeroAlpes](https://github.com/MISW4406/tutorial-4-sidecar){:target="_blank"} en su repositorio de preferencia. Si necesita ayuda en como realizar este paso, use alguno de los siguientes links:

1. [Fork Github a Github](https://docs.github.com/en/get-started/quickstart/fork-a-repo){:target="_blank"}
2. [Fork de Github a Gitlab](https://stackoverflow.com/questions/50973048/forking-git-repository-from-github-to-gitlab){:target="_blank"}
3. [Fork de Github a Bitbucket](https://stackoverflow.com/questions/8137997/forking-from-github-to-bitbucket){:target="_blank"}

Si esta usando Gitpod puede ejecutar su código de forma inmediata. De lo contrario, instale las dependencias en su máquina o ambiente de desarrollo, usando el archivo de `requirements.txt`. La versión de Python que se usa es 3.10 pero el requerimiento mínimo es de 3.7. Para crear ambiente de desarrollo virtuales en su máquina local, es sugerible usar [Conda](https://docs.conda.io/en/latest/){:target="_blank"}, en los recursos adicionales del sitio puede encontrar un [documento](/docs/recursos_adicionales/conda){:target="_blank"} sobre su configuración.

En el caso de Docker, es necesario que instale [Docker Desktop](https://www.docker.com/products/docker-desktop/){:target="_blank"} en su máquina de desarrollo y ejecute los comandos que puede encontrar en el archivo `.gitpod.yml`.

{: .note }
> Gitpod puede tardar un poco en instalar las dependencias la primera vez que crea el workspace. Usted puede observar el progreso de la instalación por medio de la vista de terminal en la parte inferior del IDE. Así mismo, en la esquina inferior derecha puede ver los pasos en ejecución con su correspondiente output.

{: .warning }
Este tutorial parte de la base que usted ya entiende los conceptos de contenedoras, orquestadoras (en especial Kubernetes) y registros de contenedoras. Si todavía tiene dudas al respecto, puede usar el Slack para que los tutores, profesor o sus mismos colegas, le brinden ayuda.

## Manos a la obra

Comience por entender los cambios en la estructura del proyecto. Este ya contiene un README con una breve explicación de la arquitectura de alto nivel y los cambios realizados. 

Durante este tutorial, vamos a enfocarnos en crear un adaptador por fuera del proyecto `aeroalpes` y desplegarlo en una contenedora aparte para extender las capacidades de nuestro sistema sin necesidad de entrar a hacer refactoring en él.

Específicamente queremos ser capaces de:

1. Consultar las reservas dado un ID por medio de una comunicación punto a punto usando gRPC.
2. Desplegar el adaptador y sistema legado en un cluster Kubernetes (o cualquier servicio de orquestación de su preferencia).

### 1. Modificar archivo Proto

Entre en el archivo `src/sidecar/aeroalpes/protos/vuelos.proto` e identifique si es necesario actualizar el servicio `Vuelos`. Piense si la definición del Request y Respuesta son suficiente para satisfacer el requerimiento funcional solicitado. 

### 2. Compilar proto

En caso de que haya hecho alguna modificación en el archivo, es necesario que vuelva a compilar el archivo `.proto`, de esa manera sus cambios van a verse reflejados en el código Python. Para compilar archivos `.proto` siga las instrucciones descritas en el README del proyecto.

### 3. Implementar servicio

Ya con la nueva definición de nuestro servicio, es necesario hacer la implementación del servicio. Diríjase al archivo `src/sidecar/aeroalpes/servicios/vuelos.py` y complete el método `ConsultarReserva` de la clase `Vuelos`. Recuerde que debe ser capaz de transformar de gRPC al formato que el REST API de AeroAlpes entiende. Para ello, vea el archivo `src/aeroalpes/api/vuelos.py` y entienda el formato de recepción y entrega de datos del servicio `dar_reserva`.

### 4. Modificar cliente

Ya con su código de servidor funcionando, ahora es momento de probarlo. Para ello, modifique el archivo `src/sidecar/cliente.py` para que una vez cree una reserva, inmediatamente la consulte usando el ID que llega como respuesta. 

Una vez con los cambios, corra el servidor y el cliente para probar. En el archivo README del repositorio puede encontrar los comandos.

### 5. Recrear la imagen Docker

Dado que su código ha cambiado, vuelva a crear la imagen para el servicio de adaptador. Si no recuerda como recrear imágenes en Docker puede consultar los comandos en el archivo README del repositorio.

### 6. Publicar imágenes

Una vez con las imágenes, publíquelas en algún [Container Registry](https://www.redhat.com/en/topics/cloud-native-apps/what-is-a-container-registry){:target="_blank"}. Si usa GCP puede seguir las siguientes [instrucciones](https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling){:target="_blank"}.

### 7. Despliegue su servicio

Usando el template `.yml` que encuentra en el repositorio, adáptelo a sus necesidades y despliéguelo en su cluster Kubernetes (o si desea usar otro servicio siéntase libre de hacerlo). 

### 8. Prueba final

{: .task}
> Una vez desplegado el servicio, cambie el `hostname` en el cliente y trate de ejectuar los cambios: crear una reserva y consultarla.
