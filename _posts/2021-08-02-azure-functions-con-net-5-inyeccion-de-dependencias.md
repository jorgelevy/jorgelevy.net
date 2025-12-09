---
title: Azure Functions con .Net 5 – Inyección de dependencias
date: 2021-08-02 12:00:00 -700
categories: [Azure Functions]
tags: [.net 5, azure, azure functions]     # TAG names should always be lowercase
image: /assets/img/azurefunctions.png
---

En su post [Inyección de dependencias en Azure Functions](posts/inyeccion-de-dependencias-en-azure-functions), de mi amigo [Facu](https://twitter.com/TheRockFacu){:target="_blank"}, él nos habla de como configurar la Inyección de Dependencias para Azure Functions para .Net Core 3.1. En este post veremos como es que lo podemos hacer para .Net 5.

> Este es un post de una serie de posts relacionado a la ejecución de Azure Functions con .Net 5
>
> - [Azure Functions con .Net 5  Ejecución en proceso aislado](/posts/azure-functions-con-net-5-ejecucion-en-proceso-aislado)
> - Azure Functions con .Net 5  Inyección de dependencias
>

En mi [post anterior](), platicaba sobre el nuevo modelo de ejecución en proceso aislado de las Azure Functions para .Net 5. En el abordábamos el tema sobre la presencia de la clase '*Program.cs*' que será el proceso encargado de la ejecución de la funcionalidad.

Es en esta clase en la que también podemos observar la presencia del método '*Main()*' que es el punto de entrada de la aplicación. Y es aquí donde nos encontramos con la inicialización de una variable tipo '*HostBuilder*' la cual una vez inicializada es ejecutada con el método '*Run()*'.

Dentro de los métodos de extensión que el namespace *Microsoft.Extensions.Hosting* nos ofrece nos encontramos el método *ConfigureServices* que es donde podremos hacer la inyección de dependencias. Este método espera recibir un [delegado de tipo Action](https://docs.microsoft.com/es-mx/dotnet/api/system.action-1?view=net-5.0){:target="_blank"} para una colección de servicios (*IServiceCollection*) que nos permite configurar los servicios que deseamos inyectar.

Así pues es aquí donde podremos por medio de una expresión lambda pasarle como parámetros los diferentes servicios que esperamos inyectar en nuestras funciones.

``` c#
.ConfigureServices(services => {
    services.AddHttpClient("Swapi", configuration => {
        configuration.BaseAddress = new Uri(Environment.GetEnvironmentVariable("SwapiUrl"));
    });
    services.AddDbContext<DemoDatabaseContext>(options => {
        options.UseSqlServer(Environment.GetEnvironmentVariable("DbConnectionString"));
    });
    services.AddTransient<IStoreBll, StoreBll>();
})
```

Quedándonos una clase Main como:

``` c#
using System;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Demo.LatinoNet.AzFunNet5.Business;
using Demo.LatinoNet.AzFunNet5.Models;
namespace Demo.LatinoNet.AzFunNet5
{
    public class Program
    {
        public static void Main()
        {
            var host = new HostBuilder()
                .ConfigureFunctionsWorkerDefaults()
                .ConfigureServices(services => {
                    services.AddHttpClient("Swapi", configuration => {
                        configuration.BaseAddress = new Uri(Environment.GetEnvironmentVariable("SwapiUrl"));
                    });
                    services.AddDbContext<DemoDatabaseContext>(options => {
                        options.UseSqlServer(Environment.GetEnvironmentVariable("DbConnectionString"));
                    });
                    services.AddTransient<IStoreBll, StoreBll>();
                })
                .Build();
            host.Run();
        }
    }
}
```

Una vez configurados los servicios para nuestras funciones, es en ellas donde ahora podemos tener la inyección de la dependencias como usualmente se hace, en los constructores de nuestras clases:

``` c#
public class Function1
{
    private readonly IHttpClientFactory _httpClientFactory;
    private readonly DemoDatabaseContext _context;
    private readonly IStoreBll _storeBll;
    public Function1(IHttpClientFactory httpClientFactory,
        DemoDatabaseContext context,
        IStoreBll storeBll)
    {
        _httpClientFactory = httpClientFactory;
        _context = context;
        _storeBll = storeBll;
    }

    ...
```

En un siguiente post, estaré hablando un poco de como es que podemos configurar nuestras Azure Functions para ejecutarse usando el .Net 5 en nuestros portales Azure.