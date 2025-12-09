---
title: Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)
date: 2022-08-12 12:00:00 -700
categories: [Azure Functions]
tags: [.net 7, azure, azure functions, preview]     # TAG names should always be lowercase
image: /assets/img/azurefunctions.png
---

Este 9 de agosto fue liberada la versión 17.3.0 de Visual Studio, y con ello llegó la emoción de muchos al ver que por primera vez se entregan las herramientas para desarrollar aplicaciones multi plataforma (MAUI por sus siglas en ingles). Y para nosotros los aventureros early adopters, como suele ser costumbre, con la liberación de una versión oficial también viene la liberación de una nueva version preview para el IDE. En esta ocasión con el nombre de [Visual Studio 2022 version 17.4 Preview 1](https://docs.microsoft.com/es-mx/visualstudio/releases/2022/release-notes-preview).

Lo interesante de esta versión preview es que vienen incluido el template de desarrollo para **Azure Functions con soporte a .NET 7 preview**. Ya habíamos hablado de la liberación de las herramientas (Azure Functions Core Tools) con este soporte en [este post](/azure-functions-con-net-7-preview-primera-version/), sin embargo nos habíamos quedado solo a nivel de consola para poder jugar con esta versión. El día de hoy, ya podemos jugar en Visual Studio Preview 17.4.

## Template de Azure Functions con .NET 7 en Visual Studio

Comenzaremos con la tradicional pantalla de creación de proyecto de Visual Studio

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-1.png)


Para posteriormente seleccionar el template de Azure Functions

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-2.png)

Seleccionamos el nombre de nuestra solución y proyecto, así como el folder

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-3.png)

Y es en el siguiente paso donde seleccionamos precisamente la versión .NET 7.0 Isolated

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-4.png)

Y terminamos seleccionando el tipo de trigger función que deseamos. Así como su configuración especifica.

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-5.png)

Así pues terminamos con un proyecto para Azure Functions en versión .NET 7.

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-6.png)

## La nube de Azure, preparada para ejecutar Azure Function con .NET 7

Otra de las grandes sorpresas que me llevé, fue encontrarme que cuando desplegamos estas Azure Functions a Azure, estas ya están soportadas. Y si bien, en la configuración del recurso todavía no se muestra la opción.

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-7.png)

Si usamos la funcionalidad de publicar desde nuestro Visual Studio, vamos a corroborar la funcionalidad se puede ejecutar.

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-8.png)

Y nuestro recurso en Azure

![Azure Functions con .NET 7 (preview) – Visual Studio 2022 (preview)](/assets/img/2022-08-12-azure-functions-con-net-7-preview-visual-studio-2022-preview/image-9.gif)

## Conclusiones

Estoy muy contento de ver que el proyecto de Azure Functions retoma el camino para estar preparados para otorgar soporte para la version .NET 7 desde el día en que será liberado formalmente como se había prometido.

Sigo a la espera de nuevas funcionalidades para la ejecución en modo aislado, como las durable functions, pero me quedo con un buen sabor de boca por ahora.