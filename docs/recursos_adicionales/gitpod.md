---
title: Gitpod
layout: default
parent: Recursos adicionales
nav_order: 1
---

# Gitpod

## Introducción
{: .no_toc }

Los entornos de desarrollo en la nube (CDE) son entornos de desarrollo bajo demanda que están preconfigurados con todas las herramientas, 
librerías y dependencias necesarias para escribir y revisar código. Se pueden duplicar y compartir fácilmente entre equipos.

Compañías como Google, Facebook, Uber, Spotify, entre otras, han decidido crear ambientes virtuales de desarrallo para sus desarrolladores. Ello permite, 
que los ingenieros no requieran instalar librerias, aplicaciones y de más dependencias para correr el código en sus máquinas personales. 
El código es desarrollado y ejecutado fuera en clusters con ambientes virtuales que incluso pueden ser más cercanos a los ambientes de despliegue 
(haciendo que la brecha entre los ambientes de producción y desarrollo sea mínima).

## ¿Qué es y cómo funciona?
{: .no_toc }

[Gitpod](https://www.gitpod.io/){:target="_blank"} Gitpod es una plataforma en la nube que permite el desarrollo de software por medio de la creación de "espacios de trabajo" (workspaces) personales. Los espacios de trabajo se basan en Linux y se pueden automatizar mediante la configuración de un gitpod.yml y/o una imagen del espacio de trabajo, como Docker. Los espacios de trabajo son efímeros y están completamente aislados, con permisos completos de superusuario.
Todo lo que puede hacer en Linux, puede hacerlo en Gitpod. Puede utilizar Gitpod para aplicaciones de interfaz de usuario de escritorio nativa y 
de interfaz gráfica de usuario (GUI) a través de Virtual Networking Computing (VNC), para lograr una experiencia comparable a la infraestructura de 
escritorio virtual (VDI) pero totalmente optimizada para la experiencia del desarrollador.

La ventaja de los workspaces es la capacidad de tener ambientes de desarrollo totalmente repetibles y predecibles. Cada integrante de un equipo de desarrollo no debe 
perder el tiempo instalando y configurando dependencias, los espacios de trabajo cuentan con el sistema operativo, dependencias, configuración y librerías necesarias para 
que el desarrollador comience a trabajar directamente en el código.

<iframe width="560" height="315" src="https://www.youtube.com/embed/lyoc5TVWM0k?si=a7OQSD5QUTSpkESI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


## Acceso a la plataforma
{: .no_toc }

Puede acceder a Gitpod por medio de su computador personal, tableta o teléfono usando un editor de código (por ejemplo, VS Code), 
IDE (por ejemplo, IntelliJ) o una terminal (por ejemplo, SSH). 


## Un espacio de trabajo por tarea
{: .no_toc }

En un día cualquiera, usted puede estar involucrado en tareas como las siguientes:

- Implementar una nueva característica
- Corregir un error
- Revisar una solicitud de extracción/fusión
- Explorar el código fuente de un proyecto de código abierto
  
Para cada una de estas tareas, usted puede crear un Workspace en Gitpod limpio y efímero. 
Incluso puedes iniciar múltiples workspaces en paralelo. Por ejemplo, mientras trabaja en una función, 
puede iniciar un segundo espacio de trabajo para revisar una revisión de producción. 
Cuando se completa la revisión, cierre la pestaña del navegador de ese workspace y continúe trabajando en su función. 
Esto funciona para cualquier proyecto GitLab, GitHub o Bitbucket.

Para conocer más casos de uso puede consular el siguiente [link](https://www.gitpod.io/docs/introduction/use-cases){:target="_blank"}.

## Gitpod vs desarrollo en máquina local
{: .no_toc }

Un espacio de trabajo de Gitpod es similar a su entorno de desarrollador local, excepto por dos diferenciadores clave:
- Está configurado como código.
- Es efímero y sólo vive mientras trabajas en una tarea.

### Archivo de configuración versus configuración manual

Los archivos `.gitpod.yml` y `.gitpod.Dockerfile` controlan qué herramientas estarán disponibles en su espacio de trabajo de Gitpod. 
Ambos archivos tienen control de versiones y le permiten monitorear los cambios en el entorno del desarrollador a lo largo del tiempo. 
Ya no es necesario usar `@channel   en el software de comunicación de su equipo para decirles a todos que actualicen su versión de Node.js, 
solo para descubrir que algunas personas estaban de vacaciones y no vieron el mensaje.

### Efímero vs duradero

Gracias a que los espacios de trabajo de Gitpod están configurados como código, puedes iniciarlos y detenerlos tantas veces como quiera. 
Usted sabe que cada espacio de trabajo tiene las herramientas que necesita y, lo que es más importante, ¡tiene desprotegido el código más reciente 
de su rama predeterminada! Ya no tiene que extraer la última rama predeterminada varias veces al día porque cada vez que inicia un nuevo espacio de trabajo, 
ya tiene disponible el último código.

## Ejemplo

Para ver un ejemplo de configuración completo, puede utilizar el siguiente [link](https://www.gitpod.io/docs/configure/workspaces){:target="_blank"} y 
seguir los pasos descritos en la documentación oficial.
