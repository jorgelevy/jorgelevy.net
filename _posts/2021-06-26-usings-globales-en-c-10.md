---
title: ‘Usings’ globales en C# 10!!! 😱
date: 2021-06-26 12:00:00 -700
categories: [C#]
tags: [c# 10, code]     # TAG names should always be lowercase
---

Microsoft Build 2021 se ha terminado, y si bien fueron muchas las sesiones, me parece que las opciones para desarrolladores se ven reducidas. en mi opinión personal, a traves de los años este evento se ha convertido más es un acto de promoción más allá que un apoyo para nosotros los programadores.

Pero este post no es sobre mi opinión sobre el Build. Sin embargo si fue necesario para hablar desde la perspectiva de lo poco con lo que me quedo sobre el evento. Y es aquí donde me gustaría hablar del anuncio que más me ha llamado la atención. Estoy hablando del llamado “**global using**”.

Esta nueva característica que tendremos presente a partir de C# 10, podremos anteponer la palabra reservada '*global*' antes de definir un '*using*' para permitir que dicho namespace sea accesible en cualquier clase sin necesidad de volver a escribir el '*using*'.

``` c#
global using System.Linq;
```

Y de qué va esto? La premisa es sencilla, si en cualquier archivo .cs que pertenezca a tu proyecto está definido un using de manera global, ya no será necesario volver a escribir ese *using* en otro archivo .cs.

Para explorar esta nueva característica del lenguaje tendremos que configurar nuestro proyecto para que utilice la version de lenguaje preview.

``` xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
    <LangVersion>preview</LangVersion>
  </PropertyGroup>

</Project>
```

Y una vez configurado el proyecto podemos, a manera de sugerencia, crear un archivo *GlobalUsings.cs* en donde estaremos poniendo los using globales para nuestro proyecto

``` c#
global using System;
global using System.Linq; 
global using System.Threading.Tasks;
```

Con esto configurado, el mismo Visual Studio nos avisará cuando un using está “globalizado”.

Qué te pareció esta nueva configuración? Será que comenzaremos a utilizarla una vez liberado la versión del lenguaje?