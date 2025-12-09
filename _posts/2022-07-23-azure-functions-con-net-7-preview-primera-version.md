---
title: Azure Functions con .Net 7 (preview) – Primera versión
date: 2022-07-23 12:00:00 -700
categories: [Azure Functions]
tags: [.net 7, azure, azure functions, preview]     # TAG names should always be lowercase
image: /assets/img/azurefunctions.png
---

Esta semana llegó esa noticia que tanto había estado esperando. Estamos ya en la version [Preview 6 de .NET 7](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-6/){:target="_blank"} y no habíamos escuchado ninguna noticia por parte del equipo de Azure Functions sobre el soporte en esta versión. Sin embargo, eso ha cambiado esta semana.

El anuncio estuvo a cargo de Mélony Qin ([@MelonyQ](https://twitter.com/MelonyQ){:target="_blank"}) Program Manager Azure Functions. En este post, Mélony nos explica en que consiste estos primeros pasos en el soporte de la tecnología serverless por parte de Azure. Y más allá de la información mostrada, me gustaría abordar algunos puntos que considero importantes a considerar.

## Ejecución en modo aislado (Isolated Mode)

He estado hablando continuamente sobre este nuevo modelo de ejecución de las Azure Functions, [en este post]() o en las varias presentaciones que he hecho últimamente. Y como ya había sido anticipado en el Road Map, para la Versión 4 de Azure Function para .Net 7 nos encontramos con que las Azure Functions puede ser ejecutados solamente en lo que se conoce como [Modelo de Ejecución Aislado (Isolated Process)](https://docs.microsoft.com/es-mx/azure/azure-functions/dotnet-isolated-process-guide){:target="_blank"}.

![Azure Functions con .Net 7 (preview) – Primera versión](/assets/img/2022-07-23-azure-functions-con-net-7-preview-primera-version/image-1.png)

Y de la descripción podemos encontrar que "*la ejecución fuera de proceso le permite desacoplar el código de función de tiempo de ejecución de Azure Functions*". Esto es, que a diferencia del modelo de ejecución en proceso (In-Process) donde las funciones son ejecutadas "*como una biblioteca de clases en el mismo proceso que el host*", es decir, por Azure. Ahora, las Azure Functions son ejecutadas por un proceso independiente del host, esto desde "*aplicación de consola de .NET que tiene como destino un runtime .NET compatible*".

## Azure Functions Core Tools

Para esta primera aproximación en las versiones preview de .NET 7 y hasta el día de hoy 23 de julio del 2022, **solo** podemos trabajar con las “[Azure Functions Core Tools](https://docs.microsoft.com/es-mx/azure/azure-functions/functions-core-tools-reference){:target="_blank"}” para crear proyectos en esta versión preview de .Net 7.

Es importante mencionar que es necesario tener instalado al menos la [versión 4.0.4653](https://github.com/Azure/azure-functions-core-tools/releases/tag/4.0.4653){:target="_blank"}. Así que si tienes una versión más antigua, es hora de actualizar.

Una vez instaladas estas herramientas, el comando que necesitamos ejecutar es el siguientes:

```
func init --worker-runtime dotnet-isolated --target-framework net7.0
```

De donde podemos observar los dos parámetros claves para tener un proyecto que posteriormente podemos añadir funciones. Estos son:

```
–worker-runtime dotnet-isolated, que indica que se debe de ejecutar en un proceso aislado
–target-framework net7.0, donde le indicamos que el framework a utilizar será .NET 7.0
```

Adicionalmente, vamos a poder agregar nuestras funciones con el comando:

```
func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
```

Y ejecutarlas localmente con:

```
func start
```

## Estructura del proyecto

Lo primero que podemos observar una vez que ejecutamos el comando de ‘func init..’ es que la estructura del proyecto donde nos encontramos con 4 archivos principales:

- *\<nombre_del_proyecto\>*.csproj, que es donde se define la version de .NET a ejecutar.
- *host.json*, el archivo de metadatos que contiene las opciones de configuración global que afectan las funciones
- *local.settings.json*, donde se definen almacenan la configuración de la aplicación y la configuración que usan las herramientas locales de desarrollo
- *Program.cs*, que es la clase que ejecuta las Azure Functions y en donde se configura el HostBuilder de la aplicación

![Azure Functions con .Net 7 (preview) – Primera versión](/assets/img/2022-07-23-azure-functions-con-net-7-preview-primera-version/image-2.png)

Y es en este último archivo donde podemos extender configurar opciones como la inyección de dependencias, la cual explico [en este post]().

## Conclusiones personales

Lo primero con lo que me encuentro es que trabajar con Azure Functions para .NET 7 el día de hoy tiene soporte un tanto menos que el básico. No es posible ejecutarlas, ni siquiera crear nuevas todavía, ni con Visual Studio Code y mucho menos con Visual Studio.

Encontrarse con que la experiencia es exactamente la misma que crear una Azure Function para .NET 6. No encuentro ninguna ventaja propiamente de las Azure Function sobre la version anterior.

Y por último, bajo esta idea de no encontrar diferencias entre las versiones del .NET, me encuentro que las “Durable Functions” no son soportadas, todavía. Siendo esta característica las más esperada para mí.

Sin embargo, conocer que el equipo de desarrollo en Microsoft a cargo de las Azure Function se encuentra trabajando para ofrecer el soporte este próximo noviembre en la liberación oficial de .NET 7 me deja con un buen sabor de boca y con mucha expectativa de seguir explorando estas nuevas versiones.