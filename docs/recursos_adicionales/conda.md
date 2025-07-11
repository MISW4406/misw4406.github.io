---
title: Conda
layout: default
parent: Recursos adicionales
nav_order: 1
---

# Conda

## Introducción
{: .no_toc }

Los gestores de entornos y paquetes son herramientas esenciales para los desarrolladores. Permiten crear entornos aislados con las herramientas, librerías y dependencias necesarias para proyectos específicos, evitando conflictos y facilitando la reproducibilidad.

Grandes empresas y equipos de desarrollo utilizan gestores de entornos para estandarizar sus configuraciones de desarrollo. Esto minimiza el tiempo que los ingenieros dedican a instalar y configurar dependencias en sus máquinas personales, asegurando que el código se ejecute en ambientes virtuales cercanos a los de despliegue, lo que reduce la brecha entre producción y desarrollo.

## ¿Qué es y cómo funciona?
{: .no_toc }

[Conda](https://docs.conda.io/projects/conda/en/stable/){:target="_blank"} es un sistema de gestión de paquetes y entornos de código abierto, multiplataforma y agnóstico al lenguaje. Permite instalar, ejecutar y actualizar paquetes y sus dependencias, así como crear, guardar, cargar y cambiar entre entornos de manera sencilla.

Funciona creando entornos virtuales aislados, lo que significa que puedes tener diferentes versiones de Python, librerías o paquetes para distintos proyectos sin que interfieran entre sí. Esto es crucial cuando un proyecto requiere una versión específica de una librería que es incompatible con otro proyecto.

La ventaja de los entornos de Conda es la capacidad de tener ambientes de desarrollo totalmente repetibles y predecibles. Cada integrante de un equipo de desarrollo no tiene que perder el tiempo instalando y configurando dependencias; los entornos de Conda aseguran que las versiones correctas de las librerías y dependencias estén disponibles para que el desarrollador comience a trabajar directamente en el código.

## Acceso a la plataforma
{: .no_toc }

Conda se utiliza a través de la línea de comandos. Una vez instalado, puedes crear y gestionar entornos con comandos como conda create, conda activate, conda install, entre otros. Es compatible con sistemas operativos como Windows, macOS y Linux.

## Un entorno por tarea o proyecto
{: .no_toc }

Imagina esto: estás trabajando en varias cosas a la vez, como añadir una nueva función, arreglar un error urgente o revisar el código de un compañero. Cada una de estas tareas puede necesitar diferentes versiones de librerías o dependencias.

Aquí es donde Conda brilla. Te permite crear un entorno Conda limpio y aislado para cada tarea o proyecto. ¿Necesitas hacer varias cosas a la vez? Sin problema. Puedes tener distintos entornos activos en terminales separadas, lo que te da la flexibilidad de cambiar entre configuraciones sin que haya conflictos. Por ejemplo, mientras trabajas en una función, puedes pasar rápidamente a otro entorno para probar una corrección de un error crítico. Una vez que terminas, simplemente desactivas ese entorno y sigues con tu trabajo principal. Así de fácil es mantener tu espacio de desarrollo organizado y eficiente.

## Ejemplo

Para ver un ejemplo de configuración completo y aprender a usar Conda, puedes consultar la guía de inicio rápido de [Conda](https://docs.conda.io/projects/conda/en/stable/user-guide/getting-started.html){:target="_blank"} y seguir los pasos descritos en la documentación oficial.
