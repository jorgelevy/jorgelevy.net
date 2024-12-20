---
title: Azure Functions con .Net 5 – Ejecución en proceso aislado
date: 2021-07-15 12:00:00 -700
categories: [Azure Functions]
tags: [.net 5, azure, azure functions]     # TAG names should always be lowercase
image: /assets/img/azurefunctions.png
---

El pasado 10 de noviembre del 2020 fue liberado el .Net 5. Sin embargo, un elemento que quedó fuera de este lanzamiento fueron las Azure Functions. No fue sino hasta el 10 de marzo del 2021 que fue liberada una versión de las Azure Functions que soporta el .Net 5.

> Este es un post de una serie de posts relacionado a la ejecución de Azure Functions con .Net 5
>
> - Azure Functions con .Net 5  Ejecución en proceso aislado
> - [Azure Functions con .Net 5  Inyección de dependencias](/posts/azure-functions-con-net-5-inyeccion-de-dependencias)
>

Con este release nos encontramos con un cambio en el modelo de ejecución. Y es aquí donde encontramos con que las Azure Functions ahora son ejecutadas en un proceso aislado.

Pero de qué viene el tema de *ejecución en proceso aislado*?

Desde sus inicios la aplicaciones de funciones de .Net y .Net Core se han ejecutado en el mismo proceso del host, trayendo consigo ventajas únicas como tener un conjunto de enlaces e inyecciones al SDK. Pero por otro lado, la ejecución de funciones para otros lenguajes es ejecutada en un modelo “out-of-process” (fuera del proceso) que permite ejecutar la funcionalidad en un proceso separado. Esto es, bajo la ejecución de un proceso que a su vez es el encargado de la ejecución de las funciones.

Es así como el equipo de desarrollo y soporte de las Azure Functions decide adoptar este modelo para el soporte en .Net 5. Teniendo así que si deseamos trabajar en esta versión, nos encontraremos con una nueva forma de trabajar.

## Program.cs

El primer cambio que vamos a notar es que cuando creamos un nuevo proyecto, digamos en Visual Studio, nos encontraremos con la presencia de la clase *Program.cs*. La cual es el punto de entrada de la ejecución de las funciones, es decir, el proceso encargado de la ejecución.


Anteriormente, como lo comenta [Facu (Facundo La Rocca)](https://twitter.com/TheRockFacu){:target="_blank"} en su blog [acá](/posts/inyeccion-de-dependencias-en-azure-functions), en las Azure Functions para .Net Core 3.1, podíamos crear una clase que heredara de *FunctionsStartup* para que está fuera ejecutada al iniciar la ejecución.

## Paquetes Nuget

Otro cambio con el que nos vamos a encontrar es con que los paquetes Nuget base que vamos a usar serán:

```
Microsoft.Azure.Functions.Worker
Microsoft.Azure.Functions.Worker.Sdk
```

y los paquetes de extensión de enlace serán:

```
Microsoft.Azure.Functions.Worker.Extensions.*
```

En donde * representa el o los paquetes de los trigger que estaremos utilizando en nuestras funciones. Por ejemplo:

```
Microsoft.Azure.Functions.Worker.Extensions.Http
Microsoft.Azure.Functions.Worker.Extensions.Timer
```

## Tabla comparativa .NET 3.1 vs.NET 5

Por acá una tabla con los cambio o diferencias con Azure Functions para .NET 3.1

| Característica/comportamiento   | En proceso (.NET Core 3.1)                                                                   | Fuera de proceso (.NET 5.0)                                                                                                  |
|---------------------------------|----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Versiones de .NET               | LTS (.NET Core 3.1)                                                                          | Actual (.NET 5.0)                                                                                                            |
| Paquetes base                   | Microsoft.NET.Sdk.Functions                                                                  | Microsoft.Azure.Functions.Worker<br>Microsoft.Azure.Functions.Worker.Sdk                                                     |
| Paquetes de extensión de enlace | Microsoft.Azure.WebJobs.Extensions.*                                                         | En Microsoft.Azure.Functions.Worker.Extensions.*                                                                             |
| Registro                        | ILogger se pasa a la función                                                                 | ILogger obtenido de FunctionContext                                                                                          |
| Tokens de cancelación           | Compatible                                                                                   | No compatible                                                                                                                |
| Enlaces de salida               | Parámetros de salida                                                                         | Valores devueltos                                                                                                            |
| Tipos de enlaces de salida      | IAsyncCollector, DocumentClient,<br>BrokeredMessage y otros tipos<br>específicos del cliente | Tipos simples, tipos serializables de JSON y matrices.                                                                       |
| Varios enlaces de salida        | Compatible                                                                                   | Compatible                                                                                                                   |
| Desencadenador HTTP             | HttpRequest/ObjectResult                                                                     | HttpRequestData/HttpResponseData                                                                                             |
| Funciones duraderas             | Compatible                                                                                   | No compatible                                                                                                                |
| Enlaces imperativos             | Compatible                                                                                   | No compatible                                                                                                                |
| Artefacto function.json         | Generado                                                                                     | No se han generado                                                                                                           |
| Configuración                   | host.json                                                                                    | host.json e inicialización personalizada                                                                                     |
| Inserción de dependencias       | Compatible                                                                                   | Compatible                                                                                                                   |
| Software intermedio             | No compatible                                                                                | Compatible                                                                                                                   |
| Tiempo de arranque en frío      | Habitual                                                                                     | Más tiempo, debido al inicio Just-in-Time.<br>Ejecute en Linux en lugar de en Windows para reducir<br>los posibles retrasos. |
| ReadyToRun                      | Compatible                                                                                   | TBD                                                                                                                          |

<small>[https://docs.microsoft.com/es-mx/azure/azure-functions/dotnet-isolated-process-guide#differences-with-net-class-library-functions](https://docs.microsoft.com/es-mx/azure/azure-functions/dotnet-isolated-process-guide#differences-with-net-class-library-functions){:target="_blank"}</small>


En algunos próximos posts, estaré hablando de como hacer la inyección de dependencias en este nuevo modelo de Azure Functions, y de que nos espera para nuevas versiones con la llegada de .Net 6. Estén atentos a esta información.