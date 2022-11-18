---
title: Tutorial 3 - Arquitectura Hexagonal
layout: default
parent: Semana 2
nav_order: 1
---

# Tutorial 3 - Arquitectura Hexagonal

## Introducción
{: .no_toc }

Como ya vimos en las lecciones del curso, la definición del lenguaje ubicuo es una tarea primordial para todo proyecto que desee utilizar Diseño Guiado por el Dominio (DDD). En este tutorial exploraremos diferentes métodos para diseñar y ejecutar lenguajes ubicuos de una forma coordinada y eficiente.

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Bottom Up

Los métodos Bottom-up son aquellos en donde partiendo desde la infraestructura o modelo de datos comenzamos a abstraer los términos, conceptos y reglas para modelar nuestro dominio. 

### Diccionario de datos

El primer método es crear un diccionario de datos usando nuestras tablas. Algo importante es 
