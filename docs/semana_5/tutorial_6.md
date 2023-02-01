---
title: Tutorial 6 - Liberación de datos con CDC y Outbox
layout: default
parent: Semana 5
nav_order: 1
---

# Tutorial 6 - Liberación de datos: CDC y Outbox

## Introducción
{: .no_toc }

Durante el transcurso de la semana comprendimos los retos para desmentalar el monolito y los posibles patrones y tácticas para dicho propósito. Uno de los puntos mencionados fue que las tácticas y métodos que involucran refactoring en el código pueden incurrir en riesgos y mantenimiento que como negocio estamos poco dispuestos a tolerar. Cuando comenzamos a aplicar los principios y prácticas de arquitecturas basadas en eventos, nos damos cuenta que podemos usar mecanismos no invasivos para poder distribuir los datos a través de los nuevos y existentes servicios. La identificación y publicación de esos conjuntos de datos a través de diferentes dominios (servicios) a sus correspondientes flujos de eventos es lo que llamamos [liberación de datos](https://learning.oreilly.com/library/view/building-event-driven-microservices/9781492057888/ch04.html#idm45380323922504){:target="_blank"}.

En este tutorial vamos implementar dos de los patrónes más populares para la liberación de datos: CDC y Outbox. Para ello, usaremos [Debezium](https://debezium.io/){:target="_blank"} como nuestra solución CDC y el [conector de Debezium para Apache Pulsar](https://archive.apache.org/dist/pulsar/pulsar-2.10.1/connectors/pulsar-io-debezium-mysql-2.10.1.nar){:target="_blank"} (sobra decir que seguiremos usando Apache Pulsar como nuestro broker de eventos).

Por último, vamos a usar [MySQL 8](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/){:target="_blank"} como nuestra base datos transaccional. Cabe aclarar que esta podría haber sido cualquier otro sistema manejador de bases de datos, tanto relacional como no relacional.

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Preparación

Haga un fork del template de [AeroAlpes](https://github.com/MISW4406/tutorial-6-cdc){:target="_blank"} en su repositorio de preferencia. Si necesita ayuda en como realizar este paso, use alguno de los siguientes links:

1. [Fork Github a Github](https://docs.github.com/en/get-started/quickstart/fork-a-repo){:target="_blank"}
2. [Fork de Github a Gitlab](https://stackoverflow.com/questions/50973048/forking-git-repository-from-github-to-gitlab){:target="_blank"}
3. [Fork de Github a Bitbucket](https://stackoverflow.com/questions/8137997/forking-from-github-to-bitbucket){:target="_blank"}

Si esta usando Gitpod puede ejecutar su código de forma inmediata. De lo contrario, instale las dependencias en su máquina o ambiente de desarrollo, usando el archivo de `requirements.txt`. La versión de Python que se usa es 3.10 pero el requerimiento mínimo es de 3.7. Para crear ambiente de desarrollo virtuales en su máquina local, es sugerible usar [Conda](https://docs.conda.io/en/latest/){:target="_blank"}, en los recursos adicionales del sitio puede encontrar un [documento](/docs/recursos_adicionales/conda){:target="_blank"} sobre su configuración.

En el caso de Docker, es necesario que instale [Docker Desktop](https://www.docker.com/products/docker-desktop/){:target="_blank"} en su máquina de desarrollo y ejecute los comandos que puede encontrar en el archivo `.gitpod.yml`.

Para configurar Apache Pulsar debe configurar el cluster completo con los brokers, bookies y zookeepper. En ambientes locales lo mejor que puede hacer es usar el archivo [docker-compose.yml](https://github.com/MISW4406/tutorial-5-cqrs-eventos/blob/main/docker-compose.yml){:target="_blank"} que encuentra en el repositorio. Así mismo, puede encontrar los archivos `.Dockerfile` para cada uno de los servicios que se deben desplegar. **Es importante tener en cuenta las configuraciones de red de los servicios**, si tiene muchos problemas entre ellos, deje que todos los servicios exponan en la interface 0.0.0.0 y todos se expongan en su localhost.

Para la configuración de Debezium puede usar el conector que ya se encuentra en el directorio `connectors/pulsar/` llamado `pulsar-io-debezium-mysql-2.10.1.nar` y el archivo de configuración `debezium-mysql-source-config.yaml`. Si desea descargar un versión distinta del conector puede usar el siguiente [link](https://pulsar.apache.org/download/){:target="_blank"}.

En el caso de la base de datos puede usar una de las imágenes oficiales de MySQL 8. Para ver su uso vea el archivo `docker-compose.yml`.

{: .note }
> Gitpod puede tardar un poco en instalar las dependencias la primera vez que crea el workspace. Usted puede observar el progreso de la instalación por medio de la vista de terminal en la parte inferior del IDE. Así mismo, en la esquina inferior derecha puede ver los pasos en ejecución con su correspondiente output.

{: .note }
Dado que para ejecutar Debezium es necesario tener el Broker de eventos funcionando, y por ende la base datos, el orden para desplegar los componentes debe ser: Base de datos, Cluster Apache Pulsar y Conector de Debezium.

## Manos a la obra

Comience por entender los cambios en la estructura del proyecto. Este ya contiene un README con una breve explicación de la arquitectura de alto nivel y los cambios realizados. 

Durante este tutorial, vamos a enfocarnos a:

1. Entender el funcionamiento de CDC y Outbox.

### 1. Despliegue los servicios

Tal como se mostró en el video tutorial, despliegue todos los servicios. En el README del proyecto puede encontrar como desplegar cada servicio de forma autónoma o vea nuevamente dichos pasos en el video tutorial. Recuerde que en nuestro caso puede usar los comandos de docker-compose por medio de `profiles`. 

### 2. Despliegue el conector Debezium

En el README y video tutorial puede ver los comandos para ejectuar el conector de debezium usando el CLI Admin de Pulsar. También es buena idea desplegar el consumidor de eventos que el CLI Client de Pulsar nos provee para ver los cambios en tiempo real.

### 3. Cree una nueva tabla

Cree una nueva tabla en el dataset `reservas` en su base de datos MySQL de forma manual. Por ejemplo algo del estilo:

```sql
CREATE TABLE cupones (codigo VARCHAR(20), DECIMAL(10,2), vencimiento DATE);
```

Use el comando para revisar tópicos y eventualmente use el CLI para oir dicho tópico. Revise el cliente y ¿Vio algún dato llegar? Inserte un registro cualquiera ¿Ve cambios? Ahora modifique la tabla y agregue una nueva columna:

```sql
ALTER TABLE cupones
ADD COLUMN valido_desde DATE AFTER vencimiento;
```

Revise el cliente ¿Vio algún dato llegar? Nuevamente agreggue un nuevo registro ¿Vio cambios?
Como tal vez ya lo habrá notado, en los tópicos específicos de cada tabla solo vemos llegar los cambios en los datos: nuevas filas, cambios en los mismas y la posible eliminación. Pero ¿qué pasa si quiero detectar cambios en los DDL? esto puede no sonar tan importante pero la verdad es que por medio de éste que podemos ser reactivos para cambiar esquemas en nuestras bases de datos sink. Piense por ejemplo en el caso ya mencionado y suponga que estos eventos que estamos capturando están siendo persistidos en nuestro Data Lake semi-estructurado o Vault. Puede que ya hayamos creado la tabla para persistir una copia exacta de nuestra tabla pero en formato append-only. Sin embargo, ahora un desarrollador en el backend realiza un cambio y no le avisa al equipo de datos (algo clásico). Probablemente nos encontremos en dos situaciones: 1. Insertamos sin el nuevo campo o 2. La inserción falla (dependiendo de como haya cambiado la tabla). 

Para evitar los anterior, Debezium también nos permite vigilar los cambios de DDL usando el tópico con el nombre del namespace o dataset. Por ende, si usted comienza a oir el tópico `reservas` y al mismo tiempo hace cambios en los esquemas de las tablas, podrá ver eventos llegar ¡Pruebelo! (este [link](https://debezium.io/documentation/reference/2.1/connectors/mysql.html#mysql-schema-change-topic){:target="_blank"} elabora un poco más sobre ello)

### 4. Manejo de fallos

Asuma ahora que hay fallos con la base de datos. Por ejemplo, pare ahora mismo la base de datos ¿Que vio? Vuelvala a subir y agregue registros ¿Sigue funcionando? Aunque CDC es una gran tecnología sigue siendo una herramienta más que nos puede dar dolores de cabeza. Piense como usted podría tolerar y manejar dichos fallos. El siguiente [link](https://pulsar.apache.org/download/){:target="_blank"} oficial de Debezium elabora un poco más acerca de este tópico.

{: .note }
> Si se siente más "osado" en el directorio `/data` busque los archivos de bin logs y elimine uno.

### 5. Juegue con la herramienta

Ya habiendo comprendido los features de Debezium y CDC, juegue un poco con la herramienta. Una buena forma es modificar el archivo de configuración y ver como cambia la ingestión de los datos. Para ello, use la [siguiente tabla](https://debezium.io/documentation/reference/2.1/connectors/mysql.html#_required_debezium_mysql_connector_configuration_properties){:target="_blank"}. Por ejemplo, ¿Cómo podríamos en este ejemplo evitar traer el código de los cupones? probablemente transmitir dicha información es riesgosa, pues alguién podría usar esos cupones para su propia beneficio. Revise en la documentación de Debezium como podemos limitar transferencia de tablas y columnas.
