---
title: Tutorial 9 - Backend for Frontends (BFFs)
layout: default
parent: Semana 6
nav_order: 1
---

# Tutorial 9 - Backend for Frontends (BFFs)

## Introducción
{: .no_toc }

En el anterior tutorial nos enfocamos en el manejo de workflows, transacciones y el como podemos preservar la integridad, exactitud y predictibilidad de las mismas. Sin embargo, aunque nos preocupe de sobre manera los microservicios y la interacción entre los mismos, cualquier sistema, sea distribuido o no, tiene sentido solamente si puede ser usado por alguien. Durante el transcurso de la semana se pudieron comprender mejor los patrones de UI para interactuar con microservicios y sus diferentes mecanismos de composición. Durante dichas lecciones conocimos el patrón Backend for Frontends (BFFs), el cual puede ser definido como un API Gateway de único propósito que expone responsabilidades/capacidades con base a la experiencia de usuario que se desea brindar.

Durante este tutorial veremos como diseñar e implementar un BFF en una arquitectura basada en eventos. La idea es que este BFF va a servir como punto de contacto para nuestros usuarios, transformando los llamados creacionales síncronos en publicación de comandos y de forma asíncrona oir los eventos de integración de los servicios de backend. Aunque en general podemos usar cualquier tipo de framework o lenguaje de API para exponer nuestras capacidades, en este tutorial vamos a explorar GraphQL y como, por medio de Queries y Mutaciones, podemos brindar una experiencia íntegra y segura a nuestros usuarios. 

Por último, para poder publicar de forma reactiva nuestros eventos a los usuarios, vamos a usar la tecnología Server-sent events (SSE) la cual nos permite abrir un canal para la publicación push de eventos a nuestros usuarios, sin la necesidad de usar un protocolo de comunicación distinto como Websockets.

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Preparación

Haga un fork del template de [AeroAlpes](https://github.com/MISW4406/tutorial-9-bff){:target="_blank"} en su repositorio de preferencia. Si necesita ayuda en como realizar este paso, use alguno de los siguientes links:

1. [Fork Github a Github](https://docs.github.com/en/get-started/quickstart/fork-a-repo){:target="_blank"}
2. [Fork de Github a Gitlab](https://stackoverflow.com/questions/50973048/forking-git-repository-from-github-to-gitlab){:target="_blank"}
3. [Fork de Github a Bitbucket](https://stackoverflow.com/questions/8137997/forking-from-github-to-bitbucket){:target="_blank"}

Si esta usando Github Codespaces puede ejecutar su código de forma inmediata. De lo contrario, instale las dependencias en su máquina o ambiente de desarrollo, usando el archivo de `requirements.txt`. La versión de Python que se usa es 3.12. Para crear ambientes de desarrollo virtuales en su máquina local, es sugerible usar [Conda](https://docs.conda.io/en/latest/){:target="_blank"}, en los recursos adicionales del sitio puede encontrar un [documento](/docs/recursos_adicionales/conda){:target="_blank"} sobre su configuración.

En el caso de Docker, es necesario que instale [Docker Desktop](https://www.docker.com/products/docker-desktop/){:target="_blank"} en su máquina de desarrollo y ejecute los comandos que puede encontrar en el archivo `.devcontainer/setup.sh`.

Para configurar Apache Pulsar debe configurar el cluster completo con los brokers, bookies y zookeepper. En ambientes locales lo mejor que puede hacer es usar el archivo [docker-compose.yml](https://github.com/MISW4406/tutorial-7-event-sourcing/blob/main/docker-compose.yml){:target="_blank"} que encuentra en el repositorio. Así mismo, puede encontrar los archivos `.Dockerfile` para cada uno de los servicios que se deben desplegar. **Es importante tener en cuenta las configuraciones de red de los servicios**, si tiene muchos problemas entre ellos, deje que todos los servicios exponan en la interface 0.0.0.0 y todos se expongan en su localhost.

En el caso de la base de datos puede usar una de las imágenes oficiales de MySQL 8. Para ver su uso vea el archivo `docker-compose.yml`.

{: .note }
> Github Codespaces puede tardar un poco en instalar las dependencias la primera vez que crea el workspace. Usted puede observar el progreso de la instalación por medio de la vista de terminal en la parte inferior del IDE. Así mismo, en la esquina inferior derecha puede ver los pasos en ejecución con su correspondiente output.

## Manos a la obra

Comience por entender los cambios en la estructura del proyecto. Este ya contiene un README con una breve explicación de la arquitectura de alto nivel y los cambios realizados. 

Durante este tutorial, vamos a enfocarnos a:

1. Extender nuestro BFF para tener soporte a suscripciones.
2. Agregar mecanismos autenticación y autorización a nuestro API.

### 1. Despliegue los servicios

Tal como se mostró en el video tutorial, despliegue el servicio Flask de AeroAlpes, Apache Pulsar Broker, base de datos MySQL y el BFF. En el README del proyecto puede encontrar como desplegar cada servicio de forma autónoma o vea nuevamente dichos pasos en el video tutorial.

### 2. Oir los eventos

En el directorio **src/bff_web/api/v1/** agregue un nuevo archivo llamado **suscripciones.py** el cual usaremos para definir una suscripción para la lectura de eventos de Reserva. El código puede lucir algo de este estilo:

```python
import asyncio
import strawberry

from .esquemas import *

@strawberry.type
class Suscripcion:
    @strawberry.subscription
    async def eventos_reserva(self, id_correlacion: str) -> Reserva:
        # TODO Oye los eventos de reserva
        ...
```

En el archivo **router.py** no olvide agregar la suscripción:

```python
from .suscripciones import Suscripcion

schema = strawberry.Schema(query=Query, mutation=Mutation, subscription=Suscripcion)
```

Para completar la función `eventos_reserva` use como base la función `stream_mensajes` en el archivo `main.py`. Esta función usa funciones y código del módulo `despachadores.py`. Por tal motivo, observe como funciona la lógica de `app_startup()` y decida como implementar la escucha y lectura de eventos del broker. La diferencia principal es que en estos momentos estamos oyendo todos los eventos, por medio del parámetro de suscripción queremos solamente oir los eventos relacionados a un ID de correlación específico.
Piense que lógica adicional debe implementar para la lectura dado un ID (no de todos los eventos).

### 3. Autenticación y autorización

Aún cuando aislemos la lectura de eventos por medio de suscripciones, hasta el momento todos estos canales siguen siendo públicos. Un usuario con un conocimiento decente de tecnología podría abusar de esta capacidad y comenzar a leer eventos de otros usuarios. 

Por tal motivo, es necesario el uso de mecanismos de autenticación y autorización. A continuación, siga la siguiente [guía](https://strawberry.rocks/docs/general/subscriptions){:target="_blank"} de Strawberry donde de forma simple nos indican como pasar párametros de conexión.

### 4. Ejecutar y probar

Ya una vez con ello, ejecute los servicios y use la UI de GraphQL `/v1` para ejecutar las consultas y comandos al backend.

### 5. Prueba final

{: .task}
> Intente extender su BFF en donde se deba usar mecanimos de autenticación para el uso de los comandos y consultas. Puede usar este [link](https://strawberry.rocks/docs/guides/authentication){:target="_blank"} como ejemplo.
