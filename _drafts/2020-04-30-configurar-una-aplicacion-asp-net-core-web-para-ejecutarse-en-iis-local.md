---
title: Configurar una Aplicación ASP.NET Core Web para ejecutarse en IIS local
date: 2020-04-30 12:00:00 -700
categories: [ASP.NET Core]
tags: [iis, visual studio]     # TAG names should always be lowercase
---

Si en alguna ocasión te encuentras con la necesidad de configurar una aplicación web de .Net Core en tu servidor local IIS. Estos los pasos para lograrlo:

## 1.- Habilitar IIS en la computadora.
Esto se logra habilitando el IIS con la opción que se encuentra dentro de “Activar o desactivar las características de Windows” en la sección de “Programas y caracteristicas”.
\* En donde usualmente se desinstalan programas.

![Program and Features](/assets/img/2020-04-30-configurar-una-aplicacion-asp-net-core-web-para-ejecutarse-en-iis-local/image-1.png)

<small>Activar o desactivar características de Windows</small>

Y seleccionando la opción de Internet Information Services


<small>Instalar IIS</small>

Nota: Para habilitar soporte para aplicaciones .Net típicas, se puede seleccionar los diferentes características deseadas en la sección de “Características de desarrollo de aplicaciones”

## 2.- Habilitar “Soporte de IIS en tiempo de desarrollo” en Visual Studio

En el Instalador de Visual Studio se deberá de incluir la opción de “Soporte de IIS en tiempo de desarrollo”. Esta opción se encuentra deshabilitada por default en las opciones de “ASP.NET Y Desarrollo Web” y deberá ser habilitada.


## 3.- Configurar la aplicación web

Finalmente, *ejecutando Visual Studio como administrador*, se deberá de configurar un perfil de publicación donde se configure la ejecución desde IIS, en el sección de “Depuración” en las propiedades del proyecto.

Con el botón de crear nuevo perfil, el cual podrá recibir cualquier nombre, configurando que la aplicación sea ejecutada utilizando IIS y seleccionando el URL de la aplicación.

## 4.- Ejecutar la aplicación

Una vez configurada la aplicación web, el perfil creado estará disponible en la opciones de depuración.