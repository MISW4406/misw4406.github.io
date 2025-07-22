---
title: Devcontainers y Codespaces
layout: default
parent: Recursos adicionales
nav_order: 1
---

# Devcontainers y Codespaces

## Introducción
{: .no_toc }

Los entornos de desarrollo en la nube (CDE) permiten trabajar con ambientes preconfigurados que contienen todas las herramientas necesarias. Con GitHub Codespaces y los [devcontainers](https://containers.dev){:target="_blank"} podemos replicar de forma sencilla el entorno de desarrollo de un proyecto.
Compañías como Google, Facebook, Uber, Spotify, entre otras, han decidido crear ambientes virtuales de desarrollo para sus desarrolladores. Ello permite, que los ingenieros no requieran instalar librerias, aplicaciones y de más dependencias para correr el código en sus máquinas personales. El código es desarrollado y ejecutado en clusters con ambientes virtuales que incluso pueden ser más cercanos a los ambientes de despliegue (haciendo que la brecha entre los ambientes de producción y desarrollo sea mínima).


## ¿Qué es y cómo funciona?
{: .no_toc }

[GitHub Codespaces](https://github.com/features/codespaces){:target="_blank"} es una plataforma de desarrollo en la nube totalmente integrada con GitHub que permite crear **espacios de trabajo preconfigurados y listos para codificar**, directamente desde cualquier repositorio.

Estos entornos se basan en **contenedores Linux** y se personalizan mediante archivos de configuración como `devcontainer.json`, lo que permite definir:

- El sistema operativo base
- Herramientas de desarrollo
- Extensiones de Visual Studio Code
- Dependencias y comandos de inicialización del proyecto

Cada Codespace corre en una **máquina virtual aislada y efímera**, con acceso completo al código fuente. Puede usarse desde el navegador o mediante el cliente de escritorio de **Visual Studio Code**, ofreciendo una experiencia de desarrollo completa desde cualquier lugar.

### Ventajas principales

- 🔁 **Entornos consistentes y reproducibles** para todo el equipo
- 🚫 Elimina el problema de "funciona en mi máquina"
- ⚡ **Inicio rápido** de espacios de trabajo sin instalaciones locales
- 🧩 **Integración nativa con GitHub**, incluyendo Actions y CI/CD
- 🌐 Soporte para aplicaciones web y redirección de puertos
- 🖥️ Soporte para interfaces gráficas vía extensiones o port forwarding

> Cada integrante del equipo puede comenzar a trabajar en el código en segundos, sin preocuparse por configuraciones locales ni dependencias específicas.

### Video explicativo

<iframe width="560" height="315" src="https://www.youtube.com/embed/Lseaqxg8NaY?si=60KYVWKI0oYNDixM" title="GitHub Codespaces Demo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


## Acceso a la plataforma
{: .no_toc }

Para usar Codespaces basta con contar con una cuenta en GitHub y habilitarlo en el repositorio. También puede abrir devcontainers de manera local usando Visual Studio Code y la extensión *Remote - Containers*.
 Los IDEs de JetBrains también ofrecen soporte (actualmente en beta) para trabajar con devcontainers y Codespaces.

## Un espacio de trabajo por tarea
{: .no_toc }

En un día cualquiera, usted puede estar involucrado en tareas como las siguientes:

- Implementar una nueva característica
- Corregir un error
- Revisar una solicitud de extracción/fusión
- Explorar el código fuente de un proyecto de código abierto

Para cada una de estas tareas, usted puede crear un Codespace limpio y efímero.
Incluso puede iniciar múltiples Codespaces en paralelo. Por ejemplo, mientras trabaja en una función,
puede iniciar un segundo Codespace para revisar una revisión de producción.
Cuando se completa la revisión, cierre la pestaña del navegador de ese Codespace y continúe trabajando en su función.
Esto funciona para cualquier proyecto GitLab, GitHub o Bitbucket.

Para conocer más casos de uso puede consultar la [documentación oficial de Codespaces](https://docs.github.com/codespaces){:target="_blank"} y la guía de [devcontainers](https://containers.dev){:target="_blank"}.

## Codespaces vs desarrollo en máquina local
{: .no_toc }
Un Codespace es similar a su entorno de desarrollador local, excepto por dos diferenciadores clave:
- Está configurado como código mediante devcontainers.
- Es efímero y sólo vive mientras trabajas en una tarea.

### Archivo de configuración versus configuración manual

Los archivos del devcontainer controlan qué herramientas estarán disponibles en su Codespace. Todos estos archivos tienen control de versiones y permiten monitorear los cambios en el entorno de desarrollo a lo largo del tiempo. Ya no es necesario enviar mensajes a su equipo para que actualicen dependencias de forma manual.

### Efímero vs duradero

Gracias a que los Codespaces están configurados como código, puede iniciarlos y detenerlos tantas veces como quiera. Cada Codespace tiene las herramientas que necesita y el código más reciente de la rama predeterminada.

## Ejemplo

Puede consultar la [documentación oficial de Codespaces](https://docs.github.com/codespaces){:target="_blank"} para ver ejemplos completos de configuración y creación de devcontainers.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Lseaqxg8NaY?si=60KYVWKI0oYNDixM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
