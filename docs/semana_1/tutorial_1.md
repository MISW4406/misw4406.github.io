---
title: Tutorial 1 - Lenguajes Ubicuos
layout: default
parent: Semana 1
nav_order: 1
---

# Tutorial 1 - Lenguajes Ubicuos

## Introducción
{: .no_toc }

Como ya vimos en las lecciones del curso, la definición del lenguaje ubicuo es una tarea primordial para todo proyecto que desee utilizar [Diseño Guiado por el Dominio (DDD)](https://martinfowler.com/bliki/DomainDrivenDesign.html){:target="_blank"}. En este tutorial, exploraremos diferentes métodos para diseñar y ejecutar [lenguajes ubicuos](https://martinfowler.com/bliki/UbiquitousLanguage.html){:target="_blank"} de una forma coordinada y eficiente.

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}

## Bottom Up

Los métodos Bottom-up, son aquellos en donde partiendo desde la infraestructura o modelo de datos comenzamos a abstraer los términos, conceptos y reglas para modelar nuestro dominio. 

### Diccionario de datos

El primer método es crear un [diccionario de datos](https://learning.oreilly.com/library/view/database-systems-concepts/9788177585674/9788177585674_ch01lev1sec3.html#:~:text=It%20is%20a%20repository%20of,each%20table%20in%20the%20database.){:target="_blank"} usando nuestras tablas. Algo importante a resaltar es que no es solo exportar nuestros metadatos y mostrarlos en una hoja de cálculo, el objetivo es comenzar a agrupar conceptos, ideas y reglas de negocio.

Comencemos usando el template del curso:

1. [Formato Excel](/assets/file/tutorial-1-diccionario-datos-template.xlsx)
2. [Formato Numbers](/assets/file/tutorial-1-diccionario-datos-template.numbers)

Esta hoja de cálculo viene con un par de ideas acerca de como documentar los objetos de dominio y hacerlo en forma de agregaciones (tema que tocaremos más adelante en el curso).

Podrá pensar que hacerlo en una hoja de cálculo es rudimentario y muy de los 90s... ¡y lo es! Pero desafortunadamente (o afortunadamente, depende del punto de vista) los stakeholders de dominio siguen amando esta tecnología y recuerde que el objetivo de un lenguaje ubicuo es, al final de cuentas, diseñar un modelo común entre todas las partes involucradas. Si todos saben como usar y entender de forma más simple la herramienta... tal vez es la mejor manera.

{: .important}
> No confundamos la herramienta con el modelo. Aunque se sugiera el uso de una hoja de cálculo no significa que este sea el modelo. Podría más bien afirmarse que el diseño del modelo es tabular. Por ende, si existen otros tipos de herramientas que lo hagan y sus stakeholders se sienten cómodos con ella ¡Hágalo!

Ahora si pasemos a los pasos a seguir para el modelo Bottom-up.

#### 1. Exportar Esquemas

Lo primero es exportar los esquemas de sus tablas para poder crear esta estructura tabular. Dado que estamos tratando de reconstruir el lenguaje por medio de la historia en los datos, es una buena idea poner uno o dos ejemplos de los valores a encontrar en el campo. i.e En el caso de una reserva puede pensar el `estado` de la misma. Es una buena idea en este caso mostrar los diferentes estados. Esto también nos ayudará desde un punto de vista de calidad de datos, puesto que es posible que encontremos valores o términos inconsistentes.

A continuación se presentan algunos ejemplos en diferentes manejadores de bases de datos.

##### MySQL

```console
mysqldump -h <IPoDNSdelHost> -u root -p --no-data <BaseDeDatos> > schema.sql
```

##### Postgres

```console
pg_dump -s <esquema>
```

##### BigQuery

```sql
SELECT *
FROM <DATASET>.INFORMATION_SCHEMA.COLUMNS
```

#### 2. Estructurar

Una vez con la información, es momento de organizarla de forma correcta en la hoja de cálculo. Puede usar un tab por tabla o un tab por agregación. De tal manera, conceptos relacionados como reserva, vuelo, odos, etc se encontrarán en el mismo lugar.

{: .note }
> Si usted o si equipo no tienen un buen conocimiento sobre el dominio de negocio no intente hacer este paso antes del tercer paso. En tal caso, haga ambos al mismo tiempo.

#### 3. Coordinar

Una vez con los archivos listos para ser llenados, debemos organizar una reunión con nuestros expertos de dominio. Como se ha mencionado en las lecciones, **no hay punto en incluir a todo el mundo**, así que sea selectivo (recuerde el [número de Miller](https://en.wikipedia.org/wiki/The_Magical_Number_Seven,_Plus_or_Minus_Two){:target="_blank"}).

En términos de sesiones, lo mejor que puede hacer es una mezcla entre sesiones síncronas y trabajo asíncrono. Va a darse cuenta que no es de mucha utilidad tener 7 u 8 personas en un cuarto discutiendo fila por fila. Las sesiones síncronas se deben usar para coordinar responsabilidades sobre la completitud y correctitud de los datos, o solucionar dudas acerca del modelo o proceso. Por ejemplo, si está discutiendo la terminología para el sub-dominio de manejo de clientes, es probable que el equipo de marketing o servicio al cliente sean incluidos y responsables de la calidad de los términos y definiciones.

#### 4. Otras opciones

Como mencioné anteriormente, el uso de hojas de cálculo puede ser un poco arcaico y difícil de mantener a futuro. Es por eso, que el uso de herramientas especializadas puede ser la mejor opción a usar una vez se llegó a un acuerdo con los expertos de dominio. Las herramientas de [linaje y providencia de los datos](https://learning.oreilly.com/library/view/business-intelligence-cookbook/9781849685481/ch08s06.html#ch08lvl2sec13){:target="_blank"} pueden ser de gran utilidad en este proceso. Además, está ayudando al proceso de gobierno de datos.

Para ver un [ejemplo en vivo](https://demo.datahubproject.io/glossary){:target="_blank"} puede usar el producto [DataHub](https://datahubproject.io/){:target="_blank"}. Juegue con el ejemplo y si cree que éste le puede servir para el proyecto o trabajo siga los siguientes tutoriales de instalación.

1. [Despliegue en máquina virtual GCP](https://datahubproject.io/docs/deploy/gcp){:target="_blank"}
2. [Despliegue en Kubernetes](https://datahubproject.io/docs/deploy/kubernetes){:target="_blank"}

#### 5. Completar

{: .task}
Use toda la información que tiene acerca de AeroAlpes y trate de completar el diccionario de datos para el contexto de **reservas**.

## Top Down

### Event Storming

Es un método basado en un formato de workshop brainstorm para la exploración colaborativa de dominios de negocio complejos propuesto por [Alberto Brandolini](https://www.avanscoperta.it/en/trainer/a-brandolini/){:target="_blank"}. El resultado se expresa por medio de post-its en una pared o tablero. La idea básica, es  reunir a desarrolladores y expertos de dominios para que cada grupo aprenda del otro. Este proceso lo podemos partir en dos tipos de formato: [**Big Picture**](#big-picture) o [**Nivel de Diseño**](#nivel-de-diseño). A continuación presentaremos ambos formatos y como cada uno nos ayudará de acuerdo el objetivo en mente.

#### Big Picture

El formato Big Picture es usualmente usado para descubrir dominios de negocio y compartir conocimiento. Normalmente consiste en su mayoría de expertos de dominio: stakeholders, product managers, ventas, etc. A continuación presentamos los pasos y áreas de trabajo para este formato.

##### 1. Alcance

Este tipo de formato puede hacerse para el negocio completo o subdominios concretos. Depende de usted decidir que tanto quiere abarcar en estas sesiones. Desde mi punto de vista, puede haber dos maneras:
1. **Holístico:** Primera sesión global para descubrir las grande épicas del negocio y despues en sesiones separadas abarcar cada sub-dominio.
2. **Reduccionista:** Si ya se entiende el negocio y hay una estructura lógica departamental, se puede comenzar  sub-dominio por sub-dominio, para finalmente tener una visión global a compartir al final del proceso.

{: .note}
> Sea cual sea el método que use, debe ser selectivo en las personas que incluya en la sesión. Recuerde el número de Miller y seleccionar un panel diverso para tener una visión completa del negocio.

##### 2. Herramientas

Como se ha dicho anteriormente, Event Storming nació para ser un proceso co-localizado. Sin embargo, en este tutorial vamos a usar herramientas colaborativas para poder trabajar y documentar nuestro proceso de forma remota. Las siguientes son algunas de las herramientas que puede utilizar:

1. [Miro](https://miro.com/){:target="_blank"}
2. [Jamboard](https://jamboard.google.com/){:target="_blank"}
3. [Lucid](https://lucid.app/){:target="_blank"}
4. [Figma](https://www.figma.com/){:target="_blank"}

En este tutorial, el [template](https://miro.com/app/board/uXjVPBntbg8=/){:target="_blank"} está desarollado en [Miro](https://miro.com/){:target="_blank"}. Sin embargo, sientase en la libertad de usar cualquier herramienta que le brinde los beneficios colaborativos y de diseño que este método requiere.

##### 3. Convención

A través de la web podrán encontrar diferentes convenciones para los diferentes colores y tamaños de los post-its. A continuación se presenta una convención basada en los principios de Brandolini y otros autores.

| Tipo                   | Descripción | Color       |
| -----------            | ----------- | ----------- | 
| [Evento de dominio](https://learning.oreilly.com/library/view/implementing-domain-driven-design/9780133039900/ch08.html){:target="_blank"}      | Son ocurrencias de algo que pasó en el dominio. Este tópico se explicará con mayor detalle en el lecciones posteriores del curso. Por ahora, tenga presente que debe escribirlos con verbos en pretérito perfecto. i.e `Orden creada`, `Orden cancelada`, etc.                                   | ![Evento de dominio](/assets/img/tutorial-1-eventstorm-evento-dominio.png)     |
| Comando                | Representan intenciones/acciones/decisiones de un actor. Similar a los eventos, debe usarse verbos para representar las acciones, solo que éstos en tiempo presente. i.e `Crear nueva orden`, `Cancelar orden`, etc. | ![Comando](/assets/img/tutorial-1-eventstorm-comando.png)       |
| Actor                  | El quién o que inicia una acción | ![Actor](/assets/img/tutorial-1-eventstorm-actor.png)       |
| Definición             | Definen términos de negocio. En cualquier caso donde se encuentre con un término específico del negocio, acrónimo o incluso del argot común, use este post-it | ![Definición](/assets/img/tutorial-1-eventstorm-definicion.png)       |
| UI             | Bosquejos de la UI para hacernos una idea del sistema. Aunque la estamos describiendo en esta sección, la verdad es que este post-it tiene más sentido en el formato nivel de diseño | ![Definición](/assets/img/tutorial-1-eventstorm-ui.png)       |
| Reglas de negocio             | Documentan condiciones y reglas de negocio. Normalmente se localizan entre eventos y siguen la sintáxis Gherkin: `Dado <evento en presente> cuando <condición> entonces <evento en presente>` | ![Definición](/assets/img/tutorial-1-eventstorm-regla-negocio.png)       |
| Modelo de lectura            | Los datos que debemos proveer para un paso en nuestro workflow | ![Definición](/assets/img/tutorial-1-eventstorm-modelo-lectura.png)      |
| Sistema externo           | Organizaciones, servicios o aplicaciones externas o de terceros| ![Definición](/assets/img/tutorial-1-eventstorm-sistema-externo.png)       |
| [Agregación](https://learning.oreilly.com/library/view/implementing-domain-driven-design/9780133039900/ch10.html){:target="_blank"}          | Son agrupamiento de objetos asociados que trataremos como unidad para el propósito de cambios. Por ahora, no vamos a entrar en detalle acerca de ellas. | ![Definición](/assets/img/tutorial-1-eventstorm-agregacion.png)       |

![Convención](/assets/img/tutorial-1-eventstorm-convencion.jpg)

##### 4. Proceso

Siga los pasos sugeridos en el video tutorial del curso, si aún tiene dudas puede leer en detalle el paso a paso en la sección [Material Adicional](#material-adicional).

##### 5. Completar

{: .task}
Use toda la información que tiene acerca de AeroAlpes y diseñe un lenguaje ubicuo usando Event Storm como medio. Enfóquese únicamente en el contexto de **reservas**.

#### Nivel de diseño

El formato a nivel de diseño se centra en el diseño del sistema e involucra desarolladores, product managers, diseñadores y managers de ingeniería. Tiende a ser un poco menos caótico que el formato Big Picture, el cual se enfoca en exploración, mientras que este tiene como prioridades el diseño y construcción.

Este tutorial no va a entrar en detalle acerca de este formato, pero puede ver un paso en el [Material Adicional](#material-adicional).

#### Material adicional

1. [Muestra del libro](https://leanpub.com/introducing_eventstorming/read_sample){:target="_blank"} de Event Storming de Alberto Brandolini.
2. [Formato Big Picture](https://medium.com/@chatuev/big-picture-event-storming-7a1fe18ffabb){:target="_blank"} 
3. [Formato Nivel de diseño](https://philippe.bourgau.net/detailed-agenda-for-a-ddd-design-level-event-storming-part-1){:target="_blank"} 


