---
title: Tutorial 8 - Sagas
layout: default
parent: Semana 6
nav_order: 1
---

# Tutorial 8 - Sagas

## Introducción
{: .no_toc }

Durante el transcurso de la semana, ya hemos podido entender la importancia de los flujos de trabajo y las diferentes maneras en que podemos implementarlos. Concretamente, me refiero a los patrones de orquestación y coreografía. Así mismo, vimos que cuando usamos arquitecturas basadas en eventos, el uso del patrón saga es una buena forma de satisfacer las necesidades transaccionales de nuestros sistemas. 

Durante este tutorial veremos como nuestros servicios fueron desmantelados del monolito y el código base para implementar un Coordinador Saga usando Orquestación. Por último, veremos como los servicios fueron implementados usando FastAPI por medio de un servidor [Non-blocking I/O Single-Thread](https://www.youtube.com/watch?v=wB9tIg209-8){:target="_blank"}. Esto es una pequeña introducción de lo que veremos en el módulo de sistemas reactivos.

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Preparación

Haga un fork del template de [AeroAlpes](https://github.com/MISW4406/tutorial-8-sagas){:target="_blank"} en su repositorio de preferencia. Si necesita ayuda en como realizar este paso, use alguno de los siguientes links:

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

1. Completar la implementación del Coordinador de Saga, para que pueda oír los eventos del Broker eventos.
2. Extender coordinador para que persista en disco el Saga Log.
3. Realizar una implementación de Sagas usando coreografía.

### 1. Despliegue los servicios

Tal como se mostró en el video tutorial, despliegue todos los servicios. En el README del proyecto puede encontrar como desplegar cada servicio de forma autónoma o vea nuevamente dichos pasos en el video tutorial. Para la implementación de las Sagas los servicios que nos interesan son los servicios de `pagos` y `gds`.

### 2. Oir los eventos

En el archivo **src/aeroalpes/modulos/sagas/aplicacion/coordinadores/saga_reservas.py** modifique decore la función `oir_mensaje` para que pueda oir eventos de dominio. Otra opción es que usted decida que esta función oiga eventos de integración y modifique estos eventos a dominio. Cabe aclarar que a nivel de capa de aplicación, usamos los eventos de dominio y comandos de aplicación para evitar quedarnos atados a conceptos de infraestructura. Recuerde que los eventos de integración van a contar con diferentes versiones y por ende no queremos que nuestra lógica quede atada a dichos conceptos.

**Tip:** En el archivo **src/aeroalpes/modulos/vuelos/aplicacion/__init__.py** puede encontrar un ejemplo de registro de handlers usando `pydispatch`. Puede usar este mismo patrón con la función de `oir_eventos`.

### 3. Construir el comando

En el mismo archivo modifique el método `construir_comando` para que, dado un evento de dominio, se capture como argumentos o parametros de entrada para construir un comando. Para entender un poco más, lea el `# TODO`

### 4. Implementar el Saga Log

En el mismo archivo modifique el método `persistir_en_saga_log` para que se persiste en alguna base de datos cada evento y comando. En este caso, usted puede crear un nuevo repositorio usando el adaptador de SQLAlchemy.

### 5. Ejecutar y probar

Ya una vez con ello, ejecute los servicios y use los endpoints de prueba de cada uno de ellos, para generar eventos y comandos de prueba (puede verlos en los archivos `main.py` de cada servicio).


### 6. Prueba final

{: .task}
> Intente crear una implementación de Sagas usando coreografía en vez de orquestación. Use los endpoints de prueba de cada servicio para probar la interacción.
