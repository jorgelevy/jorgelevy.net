---
title: Static Web Apps con API escrita con Azure Functions
date: 2022-08-24 12:00:00 -700
categories: [Azure Functions, Static Web Apps]
tags: [.net 6, azure, azure functions, static web apps]     # TAG names should always be lowercase
image: /assets/img/staticwebapp.png
---

Uno de los servicios relativamente nuevo, o que al menos está ganando tracción en Azure, son las que se conocen como Static Web Apps. Este servicio permite hospedar las aplicaciones llamadas Single-Page Application (SPA) de manera nativa y sencilla. Este tipo de aplicaciones son aquellas que ofrecen la interface de usuario y que usualmente están escritas usando un framework de JavaScript (Angular, React, Vue, etc). Las cuales la mayoría de las veces consumen servicios Back-End usualmente para consulta de datos o alguna otra funcionalidad que ese ejecutada del lado del servidor.

En este blog vamos a revisar como es que podemos implementar estos servicios de Back-End dentro de nuestras Static Web Apps con Azure Functions.

Hace algunas semanas, como celebración de los 100 webinar de @LatinoNetOnline hablamos sobre este tema. Así que si deseas saber mas sobre ellos, acá puedes encontrar el webinar.

## Pre-requisitos

En esta ocasión usaremos primordialmente la linea de comandos, más específicamente el PowerShell junto con las herramientas Command-Line Interface (CLI) de las Static Web Apps y las Azure Functions.

Se pueden encontrar información de como instalarlas en los siguientes enlaces:

- Static Web Apps CLI (SWA CLI) – https://github.com/Azure/static-web-apps-cli ó https://azure.github.io/static-web-apps-cli/
- Azure Functions Core Tools – https://github.com/Azure/azure-functions-core-tools

## Front-End

En esta ocasión crearemos una Single-Page Application (SPA) vanilla, es decir aquella que no tiene un JavaScript framework sino que utiliza solo código JavaScript plano.

Comenzaremos por crear una carpeta o folder donde estaremos trabajando y un sencillo archivo html:

```
mkdir swa-vanilla
cd swa-vanilla
New-Item -Path . -Name "index.html"
```

** Recordemos que estamos trabajando en PowerShell por eso usamos el comando New-Item, pero tú puedes crear el archivo de la forma que mejor te parezca.
Y para el contenido del archivo index.html, le agregaremos el siguiente html

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vanilla JavaScript App</title>
</head>

<body>
    <main>
        <h1>Vanilla JavaScript App</h1>
        <p>Loading content from the API: <b id="apiresponse">...</b></p>
    </main>

    <script>
        (async function () {
            let response = await fetch(`/api/message`);
            let data = await response.text();
            console.log(data);
            document.querySelector('#apiresponse').textContent = data;
        }())
    </script>
</body>

</html>
```

Donde podrás ver que en el código JavaScript se hace una llamada a un servicio api con nombre ‘message’, que incluso tiene una referencia relativa, es decir, se espera que el servicio este dentro de nuestro dominio. Así que abordemos ese paso.

## API con Azure Functions

Para la API que nuestra página web está consumiendo es necesario crearla con Azure Functions. Para este caso primero crearemos el proyecto Azure Functions y posteriormente crearemos un sencillo end point llamado ‘message’. Es aquí donde entra en juego nuestro primer CLI o set de herramientas, el de “Azure Functions Core Tools“.

Dentro de nuestra carpeta de trabajo ejecutaremos los siguientes comandos:

```
func init 'api' --worker-runtime dotnet-isolated --target-framework net6.0
cd api
func new --name 'message' --template "HTTP trigger" --authlevel "anonymous"
```

En el primer comando, creamos el proyecto llamado ‘api‘ con un modelo de ejecución isolated en .NET 6 (tú puedes crear el proyecto con las configuraciones que tu consideres mejor para tu caso de uso)

Una vez creado el proyecto nos movemos a la carpeta recién creada, en donde crearemos la función ‘message‘ que será el End-Point de la api.

``` c#
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Functions.Worker;
using Microsoft.Azure.Functions.Worker.Http;
using Microsoft.Extensions.Logging;

namespace api
{
    public class message
    {
        private readonly ILogger _logger;

        public message(ILoggerFactory loggerFactory)
        {
            _logger = loggerFactory.CreateLogger<message>();
        }

        [Function("message")]
        public HttpResponseData Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestData req)
        {
            _logger.LogInformation("C# HTTP trigger function processed a request.");

            var response = req.CreateResponse(HttpStatusCode.OK);
            response.Headers.Add("Content-Type", "text/plain; charset=utf-8");

            response.WriteString("Welcome to Azure Functions!");

            return response;
        }
    }
}
```

Este código ya fue generado por las herramientas de Azure Functions. Solo se muestra como referencia.

# Static Web App (local)

Una vez que la estructura de archivos está completa podemos proceder a configurar la Static Web App para su ejecución de manera local

![Static Web Apps con API escrita con Azure Functions](/assets/img/2022-08-24-static-web-apps-con-api-escrita-con-azure-functions/image-1.png)

Una vez posicionados en nuestra carpeta de trabajo (recordemos estábamos en la carpeta de la api), utilizaremos el “Static Web Apps CLI (SWA CLI)“

```
cd ..
swa init
```

El primer parámetro que necesitaremos configurar será el nombre de la configuración, usualmente se usa el nombre de la aplicación o del folder en este caso.

![Static Web Apps con API escrita con Azure Functions](/assets/img/2022-08-24-static-web-apps-con-api-escrita-con-azure-functions/image-2.png)

Posteriormente el CLI hará un análisis de nuestro código y nos hará sugerencias para los parámetros de ejecución de nuestras aplicaciones (Single-Page App y las Azure Functions). En nuestro caso detectará que es un Static Html con una API escrita en .NET

![Static Web Apps con API escrita con Azure Functions](/assets/img/2022-08-24-static-web-apps-con-api-escrita-con-azure-functions/image-3.png)

Analicemos estas configuraciones:

- Framework(s): los framework o lenguajes con que nuestras aplicaciones están escritas
- App location: el directorio donde está ubicado el código Front-End
- Output location: el directorio donde estará el resultado de compilar el código Front-End
- Api location: el directorio donde está ubicado el código Back-End
- App build command: el comando de compilación del código Front-End
- API build command: el comando de compilación del código Back-End
- App dev server command: el comando de ejecución del código Front-End
- App dev server url: el url de ejecución del código Front-End

Por ejemplo para una aplicación Angular y una aplicación React, tendremos configuraciones como:

![Static Web Apps con API escrita con Azure Functions](/assets/img/2022-08-24-static-web-apps-con-api-escrita-con-azure-functions/image-4.png)

Una vez aceptadas la configuraciones, se ejecutaran una serie de pasos para terminar mostrándonos las opciones que podemos realizar.

![Static Web Apps con API escrita con Azure Functions](/assets/img/2022-08-24-static-web-apps-con-api-escrita-con-azure-functions/image-5.png)

Compilaremos nuestras aplicaciones para posteriormente iniciar nuestros servicios

```
swa build
swa start
```

Así pues veremos que nuestra aplicación esta siendo ejecutada en el url http://localhost:4280/

![Static Web Apps con API escrita con Azure Functions](/assets/img/2022-08-24-static-web-apps-con-api-escrita-con-azure-functions/image-6.png)

Con resultado del lado de la Single-Page Application como

![Static Web Apps con API escrita con Azure Functions](/assets/img/2022-08-24-static-web-apps-con-api-escrita-con-azure-functions/image-7.png)

Y con la api siendo servida en http://localhost:4280/api/message

![Static Web Apps con API escrita con Azure Functions](/assets/img/2022-08-24-static-web-apps-con-api-escrita-con-azure-functions/image-8.png)

## Conclusiones

Este blog intento ofrecer apenas una introducción a las Static Web App. En posteriores post estaré escribiendo en como podemos trabajar con Visual Studio Code, como configurar este servicio en Azure, y como hacer un CI/CD desde Azure DevOps.
