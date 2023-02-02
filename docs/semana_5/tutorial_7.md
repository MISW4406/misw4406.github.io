---
title: Tutorial 7 - Event Sourcing
layout: default
parent: Semana 5
nav_order: 1
---

# Tutorial 7 - Event Sourcing

## Introducción
{: .no_toc }

En el anterior tutorial, pudimos ver un primer acercamiento a Event Sourcing en el momento en que creamos nuestra tabla de outbox siguiendo un modelo de append-only y persistimos los eventos de integración en una de las columnas de la misma. Al usar CDC podíamos usar estos datos si queríamos para poder hacer nuestras nuevas proyecciones. Sin embargo, tratar de hacer Event Sourcing usando una herramienta de liberación de datos puede involucrar múltiples puntos de fallo y posibles inconsistencias en la distribución de los eventos. Así mismo, de alguna manera estamos usando una base de datos que no fue diseñada para este propósito. 

Por tal motivo, en este tutorial vamos a seguir enfoque más "canonico" de cuando Event Sourcing se trata. Veremos como usar un broker de eventos como nuestra event store y crear los componentes necesarios para oir y materilizar vistas usando CQRS + Event Sourcing.

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Preparación

Haga un fork del template de [AeroAlpes](tutorial-7-event-sourcing){:target="_blank"} en su repositorio de preferencia. Si necesita ayuda en como realizar este paso, use alguno de los siguientes links:

1. [Fork Github a Github](https://docs.github.com/en/get-started/quickstart/fork-a-repo){:target="_blank"}
2. [Fork de Github a Gitlab](https://stackoverflow.com/questions/50973048/forking-git-repository-from-github-to-gitlab){:target="_blank"}
3. [Fork de Github a Bitbucket](https://stackoverflow.com/questions/8137997/forking-from-github-to-bitbucket){:target="_blank"}

Si esta usando Gitpod puede ejecutar su código de forma inmediata. De lo contrario, instale las dependencias en su máquina o ambiente de desarrollo, usando el archivo de `requirements.txt`. La versión de Python que se usa es 3.10 pero el requerimiento mínimo es de 3.7. Para crear ambiente de desarrollo virtuales en su máquina local, es sugerible usar [Conda](https://docs.conda.io/en/latest/){:target="_blank"}, en los recursos adicionales del sitio puede encontrar un [documento](/docs/recursos_adicionales/conda){:target="_blank"} sobre su configuración.

En el caso de Docker, es necesario que instale [Docker Desktop](https://www.docker.com/products/docker-desktop/){:target="_blank"} en su máquina de desarrollo y ejecute los comandos que puede encontrar en el archivo `.gitpod.yml`.

Para configurar Apache Pulsar debe configurar el cluster completo con los brokers, bookies y zookeepper. En ambientes locales lo mejor que puede hacer es usar el archivo [docker-compose.yml](https://github.com/MISW4406/tutorial-7-event-sourcing/blob/main/docker-compose.yml){:target="_blank"} que encuentra en el repositorio. Así mismo, puede encontrar los archivos `.Dockerfile` para cada uno de los servicios que se deben desplegar. **Es importante tener en cuenta las configuraciones de red de los servicios**, si tiene muchos problemas entre ellos, deje que todos los servicios exponan en la interface 0.0.0.0 y todos se expongan en su localhost.

En el caso de la base de datos puede usar una de las imágenes oficiales de MySQL 8. Para ver su uso vea el archivo `docker-compose.yml`.

{: .note }
> Gitpod puede tardar un poco en instalar las dependencias la primera vez que crea el workspace. Usted puede observar el progreso de la instalación por medio de la vista de terminal en la parte inferior del IDE. Así mismo, en la esquina inferior derecha puede ver los pasos en ejecución con su correspondiente output.

## Manos a la obra

Comience por entender los cambios en la estructura del proyecto. Este ya contiene un README con una breve explicación de la arquitectura de alto nivel y los cambios realizados. 

Durante este tutorial, vamos a enfocarnos a:

1. Extender las vistas y proyecciones de nuestra arquitectura para brindar vistas materializadas sobre la información almacenada en el Event Store.
2. Extender nuestro API para que acepte nuevos queries y use las proyecciones y vistas definidas anteriormente.

### 1. Despliegue los servicios

Tal como se mostró en el video tutorial, despliegue todos los servicios. En el README del proyecto puede encontrar como desplegar cada servicio de forma autónoma o vea nuevamente dichos pasos en el video tutorial. Recuerde que en nuestro caso puede usar los comandos de docker-compose por medio de `profiles`. 

### 2. Valide las políticas de retencion del Event Broker

En el README y video tutorial puede ver los comandos validar y cambiar las políticas de retención de un conjunto de tópicos parte de un namespace. Ejecute los comandos de validación y en caso que la retención no sea -1, ejecute los comandos de cambio de políticas.

### 3. Extienda la recepción de eventos

En el archivo **src/aeroalpes/modulos/vuelos/infraestructura/consumidores.py** modifique el método `suscribirse_a_eventos` para que no solo se entienda eventos de tipo `CrearReserva`, pero tambien `ReservaCancelada`. `ReservaPagada` y `ReservaAprobada`. No olvide seguir las notas en el TODO.

### 4. Extender proyección

Extienda la proyección `ProyeccionReservasLista` que se encuentra en el archivo **src/aeroalpes/modulos/vuelos/infraestructura/proyecciones.py**. Piense como implementar el Merge de forma correcta reemplazando solo los campos que cambiaron. Opcionalmente trate de usar una Unidad de Trabajo para el manejo transaccional. Este es un buen ejemplo para el manejo de locks de lectura, dado que no queremos que una lectura sucia modifique nuestros datos de forma erronea.

### 5. Modificar consulta

Modifique el archivo **src/aeroalpes/modulos/vuelos/aplicacion/queries/obtener_reserva.py** para que en vez de usar el repositorio use la vista definida en **src/aeroalpes/modulos/vuelos/infraestructura/vistas.py**. Modifique la vista para que devuelva objetos de dominio y no DTOs de infraestructura (no olvide validar si la consulta es correcta).

### 6. Modifique API

Modifique el endpoint `dar_reserva_usando_query` en **src/aeroalpes/api/vuelos.py** para que pueda filtar tambien `estado` y/o `id_cliente` (piense si es necesario crear un nuevo query o extender el actual). Una vez con ello ¡Despligue y pruebe!

### 7. Prueba final

{: .task}
> Cree una nueva proyección, vista, query y endpoint en el API para ver una tabla pivote entre fechas (día) contra estado de la reserva y como valor la cantidad de reservas.
