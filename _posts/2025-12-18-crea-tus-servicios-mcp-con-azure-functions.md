---
title: Azure Function.- Crea tus servicios MCP con Azure Functions
date: 2025-12-18 12:00:00 -700
categories: [Azure Functions, IA, Agentes, C#]
tags: [azure functions, ia, agentes, c#, .net 10]     # TAG names should always be lowercase
image: /assets/img/azurefunctions.png
---

Este artículo es parte del proyecto "[Diciembre de Agentes (2025)](https://dev.to/icebeam7/diciembre-de-agentes-2025-4oob){:target="_blank"}" creado por [Luis Beltran](https://www.linkedin.com/in/luisantoniobeltran/){:target="_blank"}. 

# Introducción

Con el creciente número de frameworks y herramientas para construir Servidores MCP, especialmente en el ecosistema de .NET, Azure Functions surge como una nueva opción. Y como es costumbre con esta tecnología, la implementación es simple permitiéndonos como desarrolladores enfocarnos más en la funcionalidad de nuestros servicios que en la tecnología que lo soporta. En este post exploraremos como podemos lograr este objetivo.

Lo primero que necesitamos recordar y tener en mente es el concepto de los *desencadenadores* (o **triggers** en ingles). Es aquí nos referimos a la descripción oficial:

> Los desencadenadores provocan la ejecución de una función. Un desencadenador define cómo se invoca una función y una función debe tener exactamente un desencadenador. Los desencadenadores también pueden pasar datos a la función, como lo haría con las llamadas al método.

Y es aquí donde justamente sucede la magia cuando queremos implementar nuestro Servidor MCP. Con el lanzamiento, por el equipo de desarrollo de Azure Functions, del empaquetado [Microsoft.Azure.Functions.Worker.Extensions.MCP](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Extensions.Mcp){:target="_blank"}, en el que podemos encontrar el trigger ***McpToolTrigger***, que también viene acompañado por la propiedad ***McpToolProperty***.

# Creación del proyecto usando Visual Studio 2026

Estaremos usando el recién estrenado Visual Studio 2026 con .NET 10, sin embargo, los conocimientos adquiridos acá de podrán implementar en versiones anteriores.

El primer paso será seleccionar la plantilla de Azure Functions.

![Crear proyecto](/assets/img/2025-12-18-crea-tus-servicios-mcp-con-azure-functions/01-create-new-project.webp)

Seguido por la selección de configuraciones a utilizar

![Crear proyecto](/assets/img/2025-12-18-crea-tus-servicios-mcp-con-azure-functions/02-configure-new-project.webp)

Hasta aquí nada nuevo en la creación de nuestra Azure Function, sin embargo, es justo en el siguiente paso donde seleccionaremos la información adicional que nos permitirá implementar nuestro *Servidor MCP*.

En este paso seleccionaremos las opciones:
- Function worker: **.NET 10 Isolated (Long Term Support)**
- Function: **MCP Tool Trigger**
- y habilitaremos '**User Azure for runtime storage account (AzureWebJobStorage)**'

![Información adicional](/assets/img/2025-12-18-crea-tus-servicios-mcp-con-azure-functions/03-additional-information-new-project.webp)

Nota 1a: En el apartado de *Function* es donde elegimos que queremos implementar un trigger de MCP Server.

Nota 1b: Probablemente para versiones anteriores de Visual Studio, la opción de MCP Tool Trigger no esté disponible. En ese caso puedes seleccionar la opción de no implementar función y hacerlo posteriormente de manera manual siguiente el código de ejemplo que más adelante estaremos revisando.

Nota 2: Para ejecutar localmente, será necesario seleccionar forzosamente la opción de *User Azure for runtime storage account (AzureWebJobStorage)*

# El código

Una vez terminado nuestro asistente de creación terminaremos con un código como este:

```c#
using Microsoft.Azure.Functions.Worker;
using Microsoft.Azure.Functions.Worker.Extensions.Mcp;
using Microsoft.Extensions.Logging;

namespace AFMcpServer;

public class Function1
{
    private ILogger<Function1> _logger;

    public Function1(ILogger<Function1> logger)
    {
        _logger = logger;
    }

    [Function(nameof(Function1))]
    public string Run(
        [McpToolTrigger(nameof(Function1), "Responds to the user with a hello message.")] ToolInvocationContext context,
        [McpToolProperty(nameof(name), "The name of the person to greet.")] string? name
    )
    {
        _logger.LogInformation("C# MCP tool trigger function processed a request.");
        return $"Hello, {name ?? "world"}! This is an MCP Tool!";
    }
}
```

En este código nos encontramos con el mentado MCPToolTrigger, que recibe dos parámetros. El primero de ellos que es el ***nombre*** de la herramienta expuesta. Seguido por una ***descripción*** de la función. Es de aquí de donde los Clientes MCP, como Copilot, usan esta información para entender el propósito de nuestro Servidor MCP y su funcionalidad.

Adicionalmente, observamos que se puede agregar una propiedad MCPToolProperty que es de donde la función recibe la información de entrada.

Y todo esto ocurre con tan solo dos líneas de código, en donde toda la plomería de nuestra Azure Function maneja todo el protocolo MCP.

Finalmente, podemos observar que la función regresa un string. Sin embargo, como veremos más adelante, lo que en realidad se regresa es un texto json que los clientes MCP usan como respuesta.

Y si bien regresamos un string, este string puede ser complejo como un json donde podemos usar la poderosa y confiable funcionalidad del *JsonSerializer.Serialize*. 

Por ejemplo, podemos implementar funciones como:

```c#
_logger.LogInformation($"Looking for people related with {people}");

IRepository<Person> personRepository = new Repository<Person>();
var entities = personRepository.GetEntities();
var searchResult = entities.Where(p => p.Name.Contains(people, StringComparison.OrdinalIgnoreCase)).ToList();

return JsonSerializer.Serialize(searchResult);
```

Como lo podemos ver, con todos esto, es donde el programador puede concentrarse en codificar la funcionalidad y despreocuparse por la configuración.

# Ejecución y consumo del MCP Server (localmente)

Cuando ejecutamos el servicio localmente, podemos ver en la ventana de salida dos endpoint. El primero será el *MCP server endpoint* y el segundo el *MCP server legacy SSE endpoint*. 

![Endpoints locales](/assets/img/2025-12-18-crea-tus-servicios-mcp-con-azure-functions/04-endpoints-mcp-server.webp)

# Consumiendo el servicio

Son estos endpoint lo que podemos usar de acuerdo con nuestra propia configuración de nuestro cliente MCP. Por ejemplo, usando Postman o GitHub Copilot para Visual Studio y Visual Studio Code.

Aquí el ejemplo para Postman (el cual cubriremos muy por encima ya que este no es el objetivo del artículo)

![Endpoints locales](/assets/img/2025-12-18-crea-tus-servicios-mcp-con-azure-functions/05-postman-mcp-client.webp)

Finalmente, como lo habíamos mencionado antes, en la respuesta de la llamada al servicio podemos observa que si bien estamos regresando un string en nuestra función, lo que realmente se regresa es un texto json que cumple con el protocolo MCP.

![Endpoints locales](/assets/img/2025-12-18-crea-tus-servicios-mcp-con-azure-functions/06-reponse-mcp-server.webp)

# Conclusiones

En este artículo solo se muestra la creación del servicio MCP Server. Sin embargo, hay mucho más que decir sobre este primer acercamiento al tema. También hay información sobre cómo se aloja esta funcionalidad en Azure y cómo utilizarla, pero ese es un tema para otro artículo. El objetivo es proporcionar un punto de partida para que experimente con esta opción, que es solo una de las muchas maneras de crear servidores MCP. La opción que elija dependerá de su conocimiento de las herramientas disponibles.
