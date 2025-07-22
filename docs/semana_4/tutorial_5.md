---
title: Tutorial 5 - CQRS y manejo de eventos
layout: default
parent: Semana 4
nav_order: 1
---

# Tutorial 5 - CQRS y manejo de eventos

## Introducción
{: .no_toc }

Durante las últimas semanas hemos conocido un poco más acerca de los microservicios basados en eventos. Entendimos que uno de los principios claves es la autonomía y aislamiento entre otros sistemas, el cual puede darse por medio de una comunicación basada en mensajes en vez de una punto-a-punto. Entendimos que dentro de nuestro microservicio queremos manejar el mismo nivel de desacoplamiento por medio de la transferencia de eventos de dominio entre los diferentes módulos del contexto y enviar evento de integración entre diferentes contextos y sistemas.

En este tutorial vamos a entender los detalles de implementación de un conjunto de microservicios basados en eventos. Para ello, usaremos CQS como nuestro patrón guía en nuestro microservicio (en verdad todavía monolito) y CQRS como nuestra arquitectura global para la interacción entre todos nuestros servicios.

Uno de los retos fundamentales cuando comenzamos a trabajar con sistemas distribuidos y basados en eventos, es poder preservar la consistencia en nuestros diferentes contextos y modelos. Para ello, es necesario pensar como poder manejar transacciones y  preservar propiedades [ACID](https://es.wikipedia.org/wiki/ACID){:target="_blank"} a largo del ciclo de vida las mismas. En este tutorial veremos como implementar una [Unidad de Trabajo (UoW)](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/ch11.html#ch11lev1sec1){:target="_blank"}, el cual tiene como objetivo mantener una lista de objetos afectados por una transacción de negocio y coordinar los cambios de escritura.

Por último, usaremos [Apache Pulsar](https://pulsar.apache.org/){:target="_blank"} como nuestro Broker de eventos para propagar nuestros comandos y eventos a través de los servicios de nuestra arquitectura. 

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Preparación

Haga un fork del template de [AeroAlpes](https://github.com/MISW4406/tutorial-5-cqrs-eventos){:target="_blank"} en su repositorio de preferencia. Si necesita ayuda en como realizar este paso, use alguno de los siguientes links:

1. [Fork Github a Github](https://docs.github.com/en/get-started/quickstart/fork-a-repo){:target="_blank"}
2. [Fork de Github a Gitlab](https://stackoverflow.com/questions/50973048/forking-git-repository-from-github-to-gitlab){:target="_blank"}
3. [Fork de Github a Bitbucket](https://stackoverflow.com/questions/8137997/forking-from-github-to-bitbucket){:target="_blank"}

Si esta usando Gitpod puede ejecutar su código de forma inmediata. De lo contrario, instale las dependencias en su máquina o ambiente de desarrollo, usando el archivo de `requirements.txt`. La versión de Python que se usa es 3.12. Para crear ambiente de desarrollo virtuales en su máquina local, es sugerible usar [Conda](https://docs.conda.io/en/latest/){:target="_blank"}, en los recursos adicionales del sitio puede encontrar un [documento](/docs/recursos_adicionales/conda){:target="_blank"} sobre su configuración.

En el caso de Docker, es necesario que instale [Docker Desktop](https://www.docker.com/products/docker-desktop/){:target="_blank"} en su máquina de desarrollo y ejecute los comandos que puede encontrar en el archivo `.gitpod.yml`.

Para configurar Apache Pulsar debe configurar el cluster completo con los brokers, bookies y zookeepper. En ambientes locales lo mejor que puede hacer es usar el archivo [docker-compose.yml](https://github.com/MISW4406/tutorial-5-cqrs-eventos/blob/main/docker-compose.yml){:target="_blank"} que encuentra en el repositorio. Así mismo, puede encontrar los archivos `.Dockerfile` para cada uno de los servicios que se deben desplegar. **Es importante tener en cuenta las configuraciones de red de los servicios**, si tiene muchos problemas entre ellos, deje que todos los servicios exponan en la interface 0.0.0.0 y todos se expongan en su localhost.


{: .note }
> Gitpod puede tardar un poco en instalar las dependencias la primera vez que crea el workspace. Usted puede observar el progreso de la instalación por medio de la vista de terminal en la parte inferior del IDE. Así mismo, en la esquina inferior derecha puede ver los pasos en ejecución con su correspondiente output.

{: .warning }
Este tutorial parte de la base que usted ya entiende los conceptos de contenedoras, orquestadoras (en especial Kubernetes) y registros de contenedoras. Si todavía tiene dudas al respecto, puede usar el Slack para que los tutores, profesor o sus mismos colegas, le brinden ayuda.

## Manos a la obra

Comience por entender los cambios en la estructura del proyecto. Este ya contiene un README con una breve explicación de la arquitectura de alto nivel y los cambios realizados. 

Durante este tutorial, vamos a enfocarnos a:

1. Extender nuestro comando CrearReserva, para que sea ejecutado de forma asíncrona usando nuestro broker de eventos.
2. Crear un comando y query para la agregación de usuario.
3. Modificar nuestra Unidad de Trabajo para que las transacciones usen un [Lock Pesimista](https://learning.oreilly.com/library/view/patterns-of-enterprise/0321127420/ch16.html#ch16lev1sec2){:target="_blank"}.

### 1. Entienda el nuevo API

Entre al archivo `src/aeroalpes/api/vuelos.py` y entienda las nuevas funciones `reservar_asincrona` y `dar_reserva_usando_query`. Observe como se crean y despachan los queries y comandos usando el patrón mediador. Usted podrá observar que se usan dos librerías distintas para el despacho de objetos. El primer tipo de despacho se conoce como [SingleDispatch](https://en.wikipedia.org/wiki/Dynamic_dispatch#Single_and_multiple_dispatch){:target="_blank"}, en donde utilizando los argumentos de una función podemos enrutas a implementaciones concretas que satisfagan el tipo de objeto pasado por parámetro.

El segundo estilo es un patrón mediador en donde por medio del uso de señales podemos enrutar a uno o múltiples implementaciones. Esta última, la puede encontrar en la publicación de eventos de dominio, donde multiples handler pueden suscribirse a una evento y reaccionar ante él.

### 2. Extienda el comando CrearReserva y Despechador

Ya habiendo entendido un poco mejor el API, en la función `reservar_asincrona` usted podrá encontrar una creación directa de la clase `CrearReserva`. Sin embargo, esto sigue siendo totalmente síncrono (todo nuestro código se ejecuta en el mismo thread). En este caso el objetivo es publicar nuestro comando usando el Despachador que encuentra en `src/aeroalpes/modulos/vuelos/infraestructura/despachadores.py`. Si lee el código podrá ver que por default se usa el comando `CrearReserva` y se transforma en un comando de integración. Modifique este código para que pueda crear múltiples comandos dependiendo del tipo que entra por parametro (tip: puede user SingleDispatch para esto). En general modifique esta clase para que pueda ser usada para despachar sus comandos.

### 3. Extienda los consumidores

Ahora debemos ser capaces de suscribirnos a nuestros comandos. Para ello, modifique las funciones de consumo de eventos y comandos que encuentra en `src/aeroalpes/modulos/vuelos/infraestructura/despachadores.py`. Modifique las funciones para que procesen y reaccionen a los mensajes que reciben (y no solo hagan print del mensaje). En este caso procese del comando de integración al comando de aplicación. Una vez cree el transformador, puede usar el despechador que vio en `src/aeroalpes/api/vuelos.py`. Corra su aplicación y ahora este endpoint debe siempre retornar 202 y ejecutar de forma asíncrona!

### 4. Crear comando para Usuario

Use el código en `src/aeroalpes/modulos/vuelos/aplicacion/comandos/crear_reserva.py` como inspiración para crear el comando `RegistrarUsuario` en el módulo `clientes`. Preste atención al uso de la Unidad de Trabajo y piense en cuales son las operaciones/objetos que usted debe llevar tracking para tener un modelo consistente en los diferentes contextos. No olvide crear eventos de dominio para indicar las ocurrencias importantes, tales como `UsuarioRegistrado` y `UsuarioValidado`.

### 5. Crear query para consultar al Usuario

Al igual que con los comandos, use de ejemplo el archivo `src/aeroalpes/modulos/vuelos/aplicacion/queries/obtener_reserva.py` para crear un query `obtener_usuario` el cual devuelve un usuario dado un id o email.

### 6. Aceptar transacciones con Lock Pesimista

Extienda `src/aeroalpes/seedwork/infraestructura/uow.py` y `src/aeroalpes/config/uow.py` para que podamos tener transacciones con un Lock pesimista. En el caso de SQLAlchemy puede [usar este método](https://stackoverflow.com/questions/40700309/flask-sqlalchemy-with-for-update-row-lock){:target="_blank"} para poder poner locks en una fila de nuestra base datos.

### 7. Prueba final

{: .task}
> Corra el servicio localmente o en su cluster de Kubernetes y valide que pueda: crear una reserva, consultar una reserva, registrar un usuario y consultar un usuario.
