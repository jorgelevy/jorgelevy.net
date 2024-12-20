---
title: ‘Dev tunnels’ en Visual Studio para Azure Functions
date: 2022-11-16 12:00:00 -700
categories: [Azure Functions, Dev Tunnels]
tags: [azure functions, dev tunnels]     # TAG names should always be lowercase
image: /assets/img/azurefunctions.png
---

El día de hoy me encontré con esta noticia que me pareció muy interesante. Este artículo (en inglés) habla de cómo crear túneles para poder acceder nuestros proyectos de ASP.NET que están siendo ejecutados en nuestros entornos locales desde otras computadoras o incluso desde dispositivos con acceso a internet.

Para quienes requerimos está opción en el pasado, teníamos la opción de usar la herramienta ngrok, la cual realiza la tarea perfectamente. Con esta nueva característica en Visual Studio ya no será necesario obtener ni ejecutar ngrok por separado ya que con ‘Dev tunnels’ estará integrado nativamente.

El artículo habla de aplicaciones ASP.NET, y lo primero que se nos viene a la mente son aplicaciones MVC, Web Api o Blazor. Pero leyendo todo el artículo, en esta sección, me di cuenta de que las Azure Functions también están soportadas. Y aquí encontraras detalles de cómo lograr este objetivo.

Disclaimer: Hasta el día de hoy, noviembre 16 del 2022, ‘Dev tunnel’ se encuentra en versión preview. La información contenida en este artículo puede nos ser actual en futuras fechas.

## Configuración de ‘Dev tunnels’ para Azure Functions

En el artículo podemos encontrar las instrucciones para configurar Visual Studio así como modificar el archivo launchSettings.json para lograr el objetivo en aplicaciones ASP.NET. Cuando hablamos de Azure Function, encontramos que también podemos crear REST APIs con el desencadenador http (HttpTrigger), y es en este escenario donde podemos aplicar ‘Dev tunnels’. Acá la sección donde habla del soporte.

Si seguimos los pasos de configuración nos encontraremos con que el archivo launchSettings.json es un tanto diferente, por lo que acá te dejo como debería de quedar el correspondiente a un proyecto Azure Functions:

```json
{
  "profiles": {
    "DevTunnel": {
      "commandName": "Project",
      "commandLineArgs": "--port 7173",
      "launchBrowser": false,
      "devTunnelEnabled": true,
      "devTunnelAccess": "public"
    }
  }
}
```

Ahora, si ejecutamos Azure Functions localmente podemos ver los endpoint expuestos, y estos son configurados usando localhost y el puerto donde están siendo servidos


Para conocer el url generado, bastará con ir a la ventana de output y seleccionar el output the ‘Dev Tunnels’.

Y entonces, cuando ejecutamos el url se nos mostrará el mensaje de advertencia

Y una vez aceptado este mensaje, presionando el botón ‘Continue’ podemos acceder a nuestras Azure Function HttpTrigger desde cualquier parte del internet usando el dominio configurado.

## Caso de uso

Y nos preguntaremos, dónde podemos utilizar esta característica? Acá te dejo un webinar que tuvimos con Luis donde nos mostró el uso de ngrok con una Azure Function como webhook para Twilio. Perfecto escenario para aplicar Dev Tunnels en nuestros proyectos.