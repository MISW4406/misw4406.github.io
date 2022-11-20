---
title: Tutorial 2 - Mapas de Contexto
layout: default
parent: Semana 1
nav_order: 1
---

# Tutorial 2 - Mapas de contexto

## Introducción
{: .no_toc }

Como ya vimos en las lecciones del curso, un [contexto acotado](https://learning.oreilly.com/library/view/implementing-domain-driven-design/9780133039900/ch02lev1sec4.html#ch02lev1sec4){:target="_blank"} individual no provee una vista global del proyecto como un todo. Esta vista global se conoce como un [Mapa de contexto](https://learning.oreilly.com/library/view/implementing-domain-driven-design/9780133039900/ch03.html){:target="_blank"} y superpone la administración del proyecto con el diseño de software.

En este tutorial vamos a explorar un [DSL](https://en.wikipedia.org/wiki/Domain-specific_language){:target="_blank"} para la documentación y visualización de mapas de contexto. Veremos un par de herramientas que podemos utilizar para el desarrollo a través del curso y que usted puede aplicar en su propio equipo de desarrollo.

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran en algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Preparación

A lo largo de este curso vamos a usar diferentes herramientas, frameworks y tecnologías que pueden constituir una misión de configurar en nuestras máquinas personales. Es por eso, que en este curso se sugiere el uso de ambientes de desarollo virtuales. A continuación se presentan algunas alternativas a utilizar:

1. [Gitpod](https://www.gitpod.io/){:target="_blank"}
2. [Github Codespaces](https://github.com/features/codespaces){:target="_blank"}
3. [CodeAnywhere](https://codeanywhere.com/){:target="_blank"}
4. [Cloud Code](https://cloud.google.com/code){:target="_blank"}

{: .important}
> Las diferentes soluciones pueden tener **costos asociados**. Algunas cuentan con una capa gratuita con un máximo de horas que en principio debe ser suficiente. Sin embargo, **NO ES OBLIGATORIO EL USO DE ESTAS HERRAMIENTAS**, si usted siente que puede hacerlo de forma local en su máquina, siga adelante.

En nuestro caso usaremos Gitpod, por lo que podrá ver que los repositorios contarán con la configuración e imágenes Docker para su correcta ejecución.

### Repositorio

Cree un repositorio GIT para el proyecto de documentación. Puede usar cualquiera de los servicios cloud, tales como [Github](https://github.com/){:target="_blank"}, [Gitlab](https://about.gitlab.com/){:target="_blank"}, [Bitbucket](https://bitbucket.org/){:target="_blank"}, etc. Tenga en cuenta, que tanto este sitio como los diferentes repositorios se encuentran en Github.

### Gitpod

Para comenzar a usar Gitpod, siga el siguiente [tutorial oficial](https://www.gitpod.io/docs/introduction/getting-started){:target="_blank"}, el cual le ayudará a configurar workspaces y brindar los principios básicos de la herramienta.

## ContextMapper

En este tutorial y a lo largo del curso usaremos la herramienta [Context Mapper](https://contextmapper.org/){:target="_blank"}, la cual es un proyecto Open-source que provee un DSL y herramientas para diseño estratégico en DDD: Mapas de contexto, modelado de contextos acotados y descomposición de servicios. 

### 1. Instalación

Tenga en cuenta los requerimientos de sistema presentados en la [documentación oficial](https://contextmapper.org/docs/getting-started/){:target="_blank"}. En el caso de Gitpod debe instalar la [extensión](https://open-vsx.org/extension/contextmapper/context-mapper-vscode-extension){:target="_blank"} de Visual Studio. Para ver un ejemplo funcionando puede usar el [demo](https://contextmapper.org/demo/){:target="_blank"} y comenzar desde ahí. Este link creará un workspace el cual usted puede usar para ver los archivos y configuración necesaria. 

Al revisar el demo en Gitpod, podrá ver algunos archivos necesarios para la ejecución:

###### gitpod.Dockerfile
```dockerfile
FROM gitpod/workspace-full

# Install Graphviz
RUN sudo apt-get update \
    && sudo apt-get -y install graphviz

# Install JHipster
RUN npm install -g generator-jhipster
```

###### gitpod.yml
```yml
tasks:
  - init: ./gradlew clean build
image:
  file: .gitpod.Dockerfile
vscode:
  extensions:
    - contextmapper.context-mapper-vscode-extension
    - jebbs.plantuml
```

###### build.gradle
```groovy
plugins {
    id "java"
    id "org.xtext.builder" version "2.0.7"
}

group 'org.contextmapper'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    xtextLanguages "org.contextmapper:context-mapper-dsl:${cmlVersion}"

    implementation "org.eclipse.xtend:org.eclipse.xtend.lib:${xtendLibVersion}"
    implementation "org.contextmapper:context-mapper-dsl:${cmlVersion}"

    testImplementation("org.junit.jupiter:junit-jupiter-api:${junitVersion}")
    testRuntimeOnly("org.junit.jupiter:junit-jupiter-engine:${junitVersion}")
}

xtext {
    languages {
        cml {
            fileExtension = 'cml'
            setup = 'org.contextmapper.dsl.ContextMappingDSLStandaloneSetup'
            generator.outlet.producesJava = true
        }
    }

    sourceSets {
        main {
            srcDir 'src/main/cml'
        }
    }
}

```

### 2. Use los ejemplos

Ya habiendo instalado la herramienta y extensions en su IDE, siga esta [guía](https://contextmapper.org/docs/getting-started-create-project/){:target="_blank"} para crear su primer proyecto o haga fork del repositorio de demo que se presentó en la sección anterior. Continúe familiarizandose con los [ejemplos](https://github.com/ContextMapper/context-mapper-examples){:target="_blank"} de la herramienta y vea los diferentes tipos de diagramas que usted puede crear usando este DSL. Para el propósito de este tutorial nos vamos a enfocar en el tipo Mapa de contexto, pero el mapa de equipos es otro excelente modelo para ver como interactúan entre si los diferentes equipos.

### 3. Modelado

Ya con un entendimiento general de la herramienta y sus usos, comience entendiendo el [lenguaje](https://contextmapper.org/docs/language-model/){:target="_blank"}. Una propiedad bastante simpática, es que el lenguaje está totalmente modelado siguiendo los principios estratégicos de DDD. Por ende, usted debería sentirse familiarizado con todos los conceptos y patrones que este DSL usa.

![DDD modelo de dominio](https://contextmapper.org/img/Strategic_DDD_Domain_Model.png)

Una vez entendido el modelo, entienda la sintáxis y reglas semánticas del DSL para el [modelado de Mapas de contexto](https://contextmapper.org/docs/context-map/){:target="_blank"}. Entienda como usar el atributo de estado: `AS_IS` y `TO_BE`, así como la forma de relacionar contextos y su patrón de integración.

{: .note}
> Es recomendable revisar el [tutorial de modelado de EventStorming en ContextMapper](https://contextmapper.org/docs/event-storming/){:target="_blank"}. De esta manera, usted puede ver como podemos integrar lo desarrollado en el anterior tutorial con el actual. 

##### 4. Completar

{: .task}
> Use toda la información que tiene acerca de AeroAlpes y diseñe un Mapa de contexto usando ContextMapper. Enfóquese únicamente en el contexto de **reservas** y **clientes**.
