---
title: Azure Static Web Apps ya soporta repositorios de Azure DevOps
date: 2021-04-06 12:00:00 -700
categories: [Azure DevOps, Static Web Apps]
tags: [azure devops, azure static web apps]     # TAG names should always be lowercase
image: /assets/img/azuredevops.png
---

El día de hoy nos encontramos con una super sorpresa que nos regresa la esperanza a lo que nos sentimos muy cómodos con Azure DevOps.

Muchos fuimos felices cuando hace algún tiempo, Microsoft anunció el lanzamiento de Azure Static Web Apps que si bien hasta el día de hoy continua en estado de preview su estabilidad es considerable. Esta característica de Azure nos permitía configurar páginas estáticas o las famosas ‘aplicaciones single-page’ con su muchos sabores de frameworks para el front-end.

Sin embargo, la decepción no tardó mucho en llegar, al enterarnos que solamente eran soportados repositorios GitHub. Dónde quedaba la integración de Azure con Azure? (ok, de Azure con Azure DevOps)

Y bueno, como ya lo había mencionado, hoy nos volvemos a emocionar pues se ha anunciado que a partir de esta fecha las Azure Static Web Pages soporta repositorios en Azure DevOps.

Y cómo va la cosa? Pues el truco ahora está en que cuando se configura un pipeline de compilación tipo yaml, podemos ahora utilizar token de desplegado (Deployment Token) el cual obtenemos utilizando la opción correspondiente en nuestro recurso de Azure.

![Token para el desplegado](/assets/img/2021-04-06-azure-static-web-apps-ya-soporta-repositorios-de-azure-devops/image-1.png)

Para finalmente utilizarlo en el código yaml de la definición del pipeline

``` yaml
trigger:​
  - main​
​
pool:​
  vmImage: ubuntu-latest​
​
steps:​
  - task: AzureStaticWebApp@0​
    inputs:​
      app_location: "/" ​
      api_location: "api​"
      output_location: ""
    env:​
      azure_static_web_apps_api_token: $(deployment_token)
```

En donde debemos definir una variable llamada deployment_token con el valor obtenido de nuestro Portal Azure

![Token para el desplegado](/assets/img/2021-04-06-azure-static-web-apps-ya-soporta-repositorios-de-azure-devops/image-2.png)

\* Podemos encontrar información más detallada en este [link](https://learn.microsoft.com/es-mx/azure/static-web-apps/get-started-portal?pivots=azure-devops&tabs=vanilla-javascript){:target="_blank"}

Si bien, la tarea no es trivial y requiere de configuraciones especiales para cada tipo de framework del front-end. Esta información nos da pie a comenzar a jugar con este nuevo juguete. Por lo pronto, para mi me sirve de buena excusa para ahora si animarme a adentrarme en Blazor. Así que estén atentos pues de esa exploración pueden salir algunas entradas en este su blog.