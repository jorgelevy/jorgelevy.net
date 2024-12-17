---
title: Static Web Apps.- Desplegados
date: 2022-09-21 12:00:00 -700
categories: [Static Web Apps]
tags: [static web apps, azure, azure functions]     # TAG names should always be lowercase
image: /assets/img/staticwebapp.png
---

En mi post anterior revisábamos como configurar el backend para nuestras Single Page Applications (SPAs) y como utilizar el CLI de Static Web App para configurar su compilación y ejecución en nuestras maquina locales (certificación works on my machine).

En este post abordaremos como configurar nuestras Static Web Apps a partir de nuestro código ya almacenado en nuestro repositorio GitHub o Azure DevOps.

## Repositorio de código en GitHub o Azure DevOps

Para desplegar una Azure Static Web App el medio más común o más utilizado es por medio de pipelines de desplegados ya sea en en GutHub Actions o en Azure DevOps. Aunque existen diferentes maneras como el mismo CLI (del cual no he podido completar la tarea por errores que me arroja).

Pero en este post nos enfocaremos usando tengo repositorio como pipelines en Azure DevOps.

Recordemos hemos venido usando una aplicación JavaScript vanilla, pero los mismo aplica para cualquier otro framework como Angular, React, Vue, etc.

![Repositorio Azure DevOps](/assets/img/2022-09-21-swa-deployment/image-7-1024x560.png)
_Repositorio Azure DevOps_

## Configurar Static Web App en Azure

El primero paso en la configuración de nuestra Static Web App será crear el recurso en Azure.

![Crear Static Web App](/assets/img/2022-09-21-swa-deployment/image-3-1024x876.png)
_Crear Static Web App_

Una vez configurados nuestros parámetros usuales, como la subscripción, el grupo de recursos y nombre; nos encontramos con nuestra primera decisión propia de las Static Web Apps, el cual será elegir el tipo de plan. Aquí, contamos a una tabla comparativa para ayudarnos en nuestra decisión.

![Comparaciones de planes](/assets/img/2022-09-21-swa-deployment/image-4-1024x763.png)
_Comparaciones de planes_

Otro punto importante es conocer que el servicio está disponible solo en una lista de regiones limitada

![img-description](/assets/img/2022-09-21-swa-deployment/image-5.png)

Siguiente paso sera configurar el repositorio de nuestro código. Como lo había mencionado anteriormente, en esta ocasión estaremos usando Azure DevOps

![img-description](/assets/img/2022-09-21-swa-deployment/image-6.png)

Y una vez configurado nuestro repositorio, nos mostrará una nueva sección donde configuraremos los parámetros de compilación, esos que veíamos en el post anterior usábamos para configurar con el CLI.

![img-description](/assets/img/2022-09-21-swa-deployment/image-8.png)

Esta acción generará un pipeline en nuestro Azure DevOps así como su respectivo archivo yml.

![img-description](/assets/img/2022-09-21-swa-deployment/image-10-1024x560.png)

## Pipeline manual

Si eres como yo, y prefieres crear tu propio pipeline de desplegado, este archivo yml te puede ayudar con ello. Si te fijas es más sencillo y entendible, y al final del día realizan la misma acción.

```yaml
trigger: none

pool:
  vmImage: ubuntu-latest

steps:
- task: AzureStaticWebApp@0
  inputs:
    app_location: '.'
    output_location: '.'
    api_location: 'api'
    azure_static_web_apps_api_token: '$(deployment_token)'
```

Solo recordar que cuando estemos configurando nuestro servicio tendremos que seleccionar como fuente de nuestro código la opción de ‘custom’

![img-description](/assets/img/2022-09-21-swa-deployment/image-14.png)

Además, una vez configurado, será necesario obtener el token de desplegado (deployment token) el cual puede obtener en tu recurso de Azure en el encabezado de la página. El cual será utilizado como una variable en el pipeline.

![img-description](/assets/img/2022-09-21-swa-deployment/image-11-1024x560.png)

![img-description](/assets/img/2022-09-21-swa-deployment/image-13-1024x560.png)

## Conclusiones

Como lo podrás notar, el desplegado es muy sencillo ya hasta pudiéramos decir que automatizado casi al 100%.

En el siguiente post, hablaremos de cómo usar una aplicación ASP.NET Web API como nuestro backend siendo una alternativa a las Azure Functions que veíamos en este último par de posts.