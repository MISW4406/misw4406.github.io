---
title: Tutorial 3 - Arquitectura Hexagonal
layout: default
parent: Semana 2
nav_order: 1
---

# Tutorial 3 - Arquitectura Hexagonal

## Introducción
{: .no_toc }

A lo largo de esta semana exploramos diferentes conceptos, principios y patrones para el [diseño táctico](https://learning.oreilly.com/library/view/implementing-domain-driven-design/9780133039900/pref06lev2sec3.html#pref06lev2sec3){:target="_blank"} en DDD. Al final, fuimos capaces de organizar todos estos mismos en una arquitectura de capas usando el principio de inversion de responsabilidades, lo que nos resultó en una [arquitectura hexagonal o también llamada arquitectura cebolla](https://learning.oreilly.com/library/view/implementing-domain-driven-design/9780133039900/ch04lev1sec3.html#ch04lev1sec3){:target="_blank"}. En este tutorial, entraremos aún más en los detalles de implementación usando Python. Veremos ejemplos de módulos, interfaces, adaptadores, agregaciones, entidades, etc. 

{: .note }
> Antes de comenzar con el desarrollo, comience viendo el video tutorial donde se explican y elaboran algunos de los métodos a tratar aquí.


## Tabla de contenidos
{: .no_toc .text-delta }

1. TOC
{:toc}


## Preparación

Haga un fork del template de [AeroAlpes](https://github.com/MISW4406/tutorial-3-arquitectura-hexagonal){:target="_blank"} en su repositorio de preferencia. Si necesita ayuda en cómo realizar este paso, use alguno de los siguientes links:

1. [Fork Github a Github](https://docs.github.com/en/get-started/quickstart/fork-a-repo){:target="_blank"}
2. [Fork de Github a Gitlab](https://stackoverflow.com/questions/50973048/forking-git-repository-from-github-to-gitlab){:target="_blank"}
3. [Fork de Github a Bitbucket](https://stackoverflow.com/questions/8137997/forking-from-github-to-bitbucket){:target="_blank"}

Si está usando Github Codespaces puede ejecutar su código de forma inmediata. De lo contrario, instale las dependencias en su máquina o ambiente de desarollo, usando el archivo de `requirements.txt`. La versión de Python que se usa es 3.10 pero el requerimiento mínimo es de 3.7. Para crear ambientes de desarollo virtuales en su máquina local, es sugerible usar [Conda](https://docs.conda.io/en/latest/){:target="_blank"}, en los recursos adicionales del sitio puede encontrar un [documento](/docs/recursos_adicionales/conda){:target="_blank"} sobre su configuración.

{: .note }
> Github Codespaces puede tardar un poco en instalar las dependencias la primera vez que crea el workspace. Usted puede observar el progreso de la instalación por medio de la vista de terminal en la parte inferior del IDE. Así mismo, en la esquina inferior derecha puede ver los pasos en ejecución con su correspondiente output.

## Manos a la obra

Comience por entender la estructura del proyecto. Este ya contiene un README con una breve explicación de la arquitectura de alto nivel. Revisa la implementación de los diferentes conceptos en el módulo de `vuelos` y use esta como ejemplo para los siguientes pasos. En este tutorial no enfocaremos en en **extender el módulo cliente**.

Específicamente queremos ser capaces de:

1. Registrar un nuevo usuario (Cliente natural).
2. Agregar, modificar y eliminar un método de pago.

### 1. Modificación de entidades y objetos

Entre en el archivo `objetos_valor.py` y modifique las clase `MetodosPago` con los atributos necesarios para que podamos agregarlo a nuestro sistema. Recuerde que un método de pago puede tener un tipo, tales como tarjeta de crédito, débito, transferencia banacaria, entre otros. 

Así mismo, queremos que el método de pago tenga un nombre, el cual un usuario puede cambiar en cualquier momento. Por motivos de seguridad, no queremos persistir información sensible acerca del método. Es decir, datos como número de la tarjeta, cvv, etc deben estar totalmente ofuscados. Sin embargo, para poder hacer uso de las mismas contaremos con un Token persistente de seguridad único, el cual nos permite retribuir los datos pagos posteriores. ¿Qué otras entidades u objetos deben ser agregados o modificados?

### 2. Definición de la agregación

Una vez definidas todas las entidades y objetos valor, defina la raíz de la agregación ¿Cual cree que debe ser la entidad raíz? ¿Qué objetos deben ser parte de la agregación? Recuerde que puede utilizar los componentes definidos en el módulo de `seedwork`.

### 3. Definición de reglas

Defina reglas para el registro de un nuevo usuario. En nuestro caso debemos verificar:

1. El nombre y apellido no son nulos o con caracteres especiales.
2. El correo electrónico sigue el [RFC822](http://www.ex-parrot.com/~pdw/Mail-RFC822-Address.html){:target="_blank"}.
3. La cédula sigue el formato numérico [dictado por la ley](https://es.wikipedia.org/wiki/C%C3%A9dula_de_ciudadan%C3%ADa_(Colombia)){:target="_blank"}. Recuerde que AeroAlpes es una compańia internacional y este puede cambiar de acuerdo al país.
4. Los métodos de pago no pueden tener más de 80 caracteres y menos de 5. Así mismo, ninguno debe ser un caracter especial.

### 4. Creación del repositorio

Cree el repositorio para la agregación. Recuerde que la interfaz debe encontrarse en el módulo de `dominio`, pero la implementación debe encontrarse en el módulo de `infrastructura`. No olvide usar las clases definidas en el `seedwork`.

En el proyecto se está usando [SQLite](https://www.sqlite.org/index.html){:target="_blank"} para la persistencia de los datos. Es probable que deba crear mapeaderores para los objetos de dominio y los DTOs.

### 5. Creación del servicio de aplicación

Cree un servicio en el módulo de `aplicacion`. Este servicio puede ser creado usando un patrón de servicios clásico o usando un patrón [CQRS](https://learning.oreilly.com/library/view/designing-event-driven-systems/9781492038252/ch07.html#event_sourcing_comma_command_sourcing){:target="_blank"}, en donde podemos definir comandos para: `registrar_usuario`, `agregar_metodo_pago`, etc. Esta lectura le puede ayudar a la [implementación](https://www.cosmicpython.com/book/chapter_12_cqrs.html){:target="_blank"} usando dicho patrón.

### 6. Agregue los Endpoints al API

Modifique el módulo API para agregar los nuevos Endpoints y que sean capaces de enrutar a los servicios de aplicación.

### 7. Prueba final

{: .task}
> Una vez finalizada la implementación corra el servidor, registre un usuario, agregue un método de pago a éste, modifíquelo y despues eliminelo. No olvide validar caminos de excepción tales como: usuario con mismo email, método de pago con mismos datos, eliminación de método de pago que no existe, etc.
