---
title: Inyección de dependencias en Azure Functions
date: 2022-07-15 12:00:00 -700
categories: [Azure Functions]
tags: [.net, azure, azure functions]     # TAG names should always be lowercase
image: /assets/img/azurefunctions.png
---

— Este es un respaldo del post realizado el 29/06/2021 por [Facundo La Rocca](https://twitter.com/TheRockFacu){:target="_blank"} —

A estas alturas, inyección de dependencias es uno de esos patrones de diseño que se ha convertido casi obligatorio, no sólo porque nos ayuda a escribir código mas testeable, sino también a escribir componentes desacoplados. No importa que tan compleja o simple sea una clase, si tiene alguna dependencia la inyectaremos mediante el constructor.

En este post veremos los pasos necesarios para poder comenzar a utilizar inyección de dependencias de la misma forma que lo hacemos en cualquier aplicación web.

## Contexto

Cuando iniciamos un proyecto nuevo de Azure Functions, a diferencia de como sucede con proyectos de ASP.Net, no disponemos de ningún mecanismo listo para usar para inyectar dependencias.

A modo de ejemplo, pensemos una Azure Function llamada “Greeting” del tipo HttpTrigger que en base a un nombre devuelve un saludo.

Como parte de nuestra solución, implementamos un servicio muy simple de forma que nuestro código sea testeable.

``` c#
public class GreetingService : IGreetingService
{
    public string GetGreeting(string name) =>
        string.IsNullOrEmpty(name)
            ? "Hi there!"
            : $"Hi {name}!";
}
```

Luego, implementamos el código de la función de forma tal que lea el nombre recibido como query string y obtenga el saludo correspondiente utilizando el servicio anterior.

``` c#
public static class Greeting
{
    [FunctionName("Greeting")]
    public static IActionResult Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequest req)
    {
        var name = req.Query["name"];
        var greetingService = new GreetingService();

        var greeting = greetingService.GetGreeting(name);

        return new OkObjectResult(greeting);
    }
}
```

En la línea 8 podemos apreciar el problema: para poder utilizar el servicio necesitamos crear una instancia del mismo.

## Requerimiento

Necesitamos implementar Inyección de Dependencias, no sólo porque las buenas prácticas así lo recomiendan, sino también porque queremos escribir Test Unitarios, y sabemos que de la forma que está escrito nuestro código es imposible.

Parte del requerimiento consiste en utilizar todas las herramientas que le framework nos provea, de forma que no tengamos que implementar nuestro propio set de clases o Contenedor para inyección de dependencias.

## Implementación

Para poder completar nuestro requerimiento necesitamos dos cosas:

Registrar y configurar nuestros servicios (generalmente cuando se inicia la aplicación)
Inyectar las dependencias en forma automática, como lo hacemos en aplicaciones Web o APIs con ASP.Net.

Por suerte, el framework de Azure Functions nos provee de ambos mecanismos, solo tenemos que realizar las configuraciones necesarias.

## Librerías

Comenzaremos instalando la librería Microsoft.Azure.Functions.Extensions, la cuál nos va facilitar los componentes que necesitamos.

```
dotnet add package Microsoft.Azure.Functions.Extensions
```

## Startup

Azure Functions dispone de una clase abstracta diseñada para ser ejecutada al iniciar la aplicación. Esta clase se llama FunctionsStartup y nos va a permitir realizar la registración de servicios que necesitamos.

Comenzaremos agregando una nueva clase al proyecto llamada Startup y haremos que herede de la clase FunctionsStartup.

``` c#
using Demo;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(Startup))]

namespace Demo
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
        }
    }
}
```

El atributo de ensamblado (línea 4) es fundamental para que el framework pueda detectar los startups que deben ser invocados.

## Configure

Al heredar de la clase abstracta FunctionsStartup, tendremos que sobrescribir el método Configure. Aquí haremos la registración de los servicios necesarios, igual que lo hacemos en cualquier aplicación ASP.Net. Siguiendo con el ejemplo, registraremos la clase GreetingService.

``` c#
public override void Configure(IFunctionsHostBuilder builder)
{
    builder.Services.AddSingleton<IGreetingService, GreetingService>();
}
```

## Function

El último paso que nos queda es modificar la función para poder inyectar los servicios necesarios como lo haríamos con cualquier otra clase. Las modificaciones son:

Modificar la clase y el método Run para que ya no sean static.
Agregar un constructor parametrizado con los servicios que queremos recibir.
Guardar los servicios en variables de instancia.
Modificar el metodo en cuestion para utilizar las variables de instancia

``` c#
public class Greeting
{
    private readonly IGreetingService _greetingService;

    public Greeting(IGreetingService greetingService) =>
        _greetingService = greetingService;

    [FunctionName("Greeting")]
    public IActionResult Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequest req)
    {
        var name = req.Query["name"];
        var greeting = _greetingService.GetGreeting(name);

        return new OkObjectResult(greeting);
    }
}
```

## Conclusión

En este breve post vimos cómo podemos configurar nuestra Azure Function para utilizar Inyección de Dependencias utilizando todas las herramientas que le framework nos provee.

Si querés acceder al código completo, te dejo a continuación el repositorio en Github.