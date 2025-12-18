---
title: Aspire.- Aspire más allá de .NET
date: 2025-12-18 12:00:00 -700
categories: [Aspire]
tags: [aspire, nodejs, react, angular]     # TAG names should always be lowercase
image: /assets/img/aspire.webp
---

> Este artículo es parte del proyecto "Calendario de adviento 2025" creado por [Ricardo Josue Pérez Altamirano](https://www.linkedin.com/in/ricardojosue/){:target="_blank"}.

.NET Aspire evoluciona y se desprende de la marca para hacerlo más poliglota. Comenzando con retirarle del nombre la parte de .NET y dejándolo simplemente en ***Aspire***. Además, hace brinco de número de versión pasando de la versión 9 a la versión 13. Entre alguno otros cambios que iremos mencionando.

# Lanzamiento

Aspire 13 marca un cambio de ciclo importante: Se presentó en noviembre de 2025 durante .NET Conf como la mayor actualización hasta la fecha, con mejoras para aplicaciones distribuidas y soporte más amplio para entornos cloud-native.[1]

[1]: <https://visualstudiomagazine.com/articles/2025/11/12/microsoft-releases-aspire-13.aspx> "Visual Studio Magazine"

Desde sus inicio el proyecto ha sido open source y con esta nueva versión continua con la misma filosofía en su objetivo de proveer una herramienta para la orquestación de nuestros proyectos. Ahora con la encomienda de abrirse al mundo y ser un referente no solo para proyectos .NET.

# Aspire ahora es poliglota (multi-lenguaje)

Con un enfoque IA y con la idea de 'Aspireify Anything' (Asperificar cualquier cosa), esta nueva versión se pone como objetivo expandir su capacidades y da la bienvenida al ecosistema a cualquier lenguaje de programación o framework. Desde proyectos .NET hasta Node.js o Javascript como Angular, React, Vue, etc.

Este cambio refleja la visión de Microsoft de convertir Aspire en una plataforma de aplicaciones distribuidas y cloud-native para cualquier lenguaje, no solo para .NET. Así, Aspire se convierte en un entorno poliglota de orquestación y despliegue, capaz de integrar equipos y proyectos con diferentes stacks tecnológicos.

Y es en este tema en el que me enfocaré en este artículo. Dejando fuera las otras características que han sido incluidas en este lanzamiento.

# Galería de integraciones

Podemos mencionar rápidamente que también, junto con el lanzamiento de la versión 13, el proyecto toma las maletas y no solo muda su página web sino que la organiza con un nuevo diseño en su nuevo hogar [https://aspire.dev/](https://aspire.dev/){:target="_blank"}.

Y es aquí donde podemos encontrar la documentación respecto a la integración que el proyecto ofrece en el apartado de [descripción general de las integraciones](https://aspire.dev/integrations/overview/){:target="_blank"} donde se explica la integraciones de hospedaje, clientes, versionamientos, etc.

Podemos encontrar integraciones desde servicios como Redis, Base de Datos, etc. que sirven como soporte a nuestros proyectos principales de Back-End y Front-End. Toda ellas abordadas en el [la galería de integraciones](https://aspire.dev/integrations/gallery){:target="_blank"}.

Entonces viene lo interesante para el autor de este artículo. Es bien conocido que un área de fortaleza del entorno .NET es en el Back-End, mientras que existen áreas de mejora en el Front-End permitiendo encontrar que mucho de ellos están escritos en frameworks como Angular, React, Vue, etc. Así que echaremos un vistazo rápido a como **Aspire 13** logra cumplir con el cometido.

# Integrando aplicaciones Javascript

En esta ocasión nos apoyaremos en un repositorio de GitHub que nos permitirá observar cómo es que podemos referenciar proyectos escrito en Angular, React, Vue y Vite. Puedes clonar o hacer un fork en esta url: [https://github.com/dotnet/aspire-samples](https://github.com/dotnet/aspire-samples){:target="_blank"}

Y si bien podemos una larga serie de proyectos, nos enfocaremos en el que podemos encontrar en la carpeta *samples/aspire-with-javascript*, donde encontraremos un archivo de solución que podemos abrir con nuestro Visual Studio, o Visual Studio Code con su extensión *C# Dev Kit*.

Dentro de la simplicidad que Aspire ofrece, sabemos que necesitamos ir al archivo *AppHost.cs* que podemos encontrar en el proyecto *AspireJavaScript.AppHost* en donde encontraremos el siguiente código:

```c#
var builder = DistributedApplication.CreateBuilder(args);

var weatherApi = builder.AddProject<Projects.AspireJavaScript_MinimalApi>("weatherapi")
    .WithExternalHttpEndpoints();

builder.AddJavaScriptApp("angular", "../AspireJavaScript.Angular", runScriptName: "start")
    .WithReference(weatherApi)
    .WaitFor(weatherApi)
    .WithHttpEndpoint(env: "PORT")
    .WithExternalHttpEndpoints()
    .PublishAsDockerFile();

builder.AddJavaScriptApp("react", "../AspireJavaScript.React", runScriptName: "start")
    .WithReference(weatherApi)
    .WaitFor(weatherApi)
    .WithEnvironment("BROWSER", "none") // Disable opening browser on npm start
    .WithHttpEndpoint(env: "PORT")
    .WithExternalHttpEndpoints()
    .PublishAsDockerFile();

builder.AddJavaScriptApp("vue", "../AspireJavaScript.Vue")
    .WithRunScript("start")
    .WithNpm(installCommand: "ci")
    .WithReference(weatherApi)
    .WaitFor(weatherApi)
    .WithHttpEndpoint(env: "PORT")
    .WithExternalHttpEndpoints()
    .PublishAsDockerFile();

var reactVite = builder.AddViteApp("reactvite", "../AspireJavaScript.Vite")
    .WithReference(weatherApi)
    .WithEnvironment("BROWSER", "none");

// Bundle the output of the Vite app into the wwwroot of the weather API
weatherApi.PublishWithContainerFiles(reactVite, "./wwwroot");

builder.Build().Run();
```

No entraremos en detalle de analizar línea por línea, pero es en este código donde podemos observar cómo se integran los diferentes proyectos de Front-End con el proyecto de Back-End con una serie de sencillas configuraciones que permiten a la plataforma realizar la orquestación. Desde la configuración de puertos y endpoints hasta la configuración de los contenedores Docker.

# Conclusiones

Es con este sencillo, pero a la vez poderoso ejemplo, que nos otorga la oportunidad de ver el potencial de Aspire en la integración de diferentes plataformas. Cómo lo he venido mencionando en las diferentes intervenciones que he tenido en el último mes. Con esto, Aspire me da ahora si las herramientas para promover su uso en mi actual trabajo, y espero que a ti de la oportunidad de al menos comenzar a jugar con él.
