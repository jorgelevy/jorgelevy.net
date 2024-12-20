---
title: Cobertura de código para pruebas unitarias en C# con Coverlet
date: 2020-06-17 12:00:00 -700
categories: [Unit Test]
tags: [unit test, cobertura de código, code voverage, coverlet]     # TAG names should always be lowercase
---

Ahora que he estado utilizando la versión Community de Visual Studio, una de las características que considero hacen la diferencia respecto a la versión Enterprise es la falta de *Cobertura de Código* para las *Pruebas Unitarias* (Code Coverage en inglés) en la versión community. Esta característica permite medir en porcentaje y visualizar con marcado de líneas toda aquellas líneas de código que son tocadas, o no, con nuestras pruebas.

Es aquí donde [Coverlet](https://github.com/coverlet-coverage/coverlet){:target="_blank"} y [ReportGenerator](https://github.com/danielpalme/ReportGenerator){:target="_blank"} entran en juego permitiendo por medio de paquetes Nugets y otras herramientas del dotnet, realizar dicha tarea. Así pues, en esta entrada, veremos como generar los reportes de cobertura utilizando Coverlet. Y posteriormente se mostrara el uso de la herramienta proveída por ReportGenerator para obtener el reporte visual en formato html.

## Configurar el proyecto de pruebas

Lo primero que se necesita realizar es agregar al proyecto de pruebas (puede ser tanto MSUnit como Xunit) el paquete nuget:

```
Install-Package coverlet.msbuild
```

\* Verificar que el proyecto de pruebas también incluya el paquete coverlet.collector

Una vez configurado el proyecto, los siguientes pasos serán realizados en una *Consola de Windows* (command prompt en inglés). Recomendando utilizar la terminar proveída por Visual Studio.

## Preparación del ambiente

Como preparación del ambiente, será necesario instalar la herramienta *dotnet-reportgenerator-globaltool*. Aún y cuando esta herramienta es provista por medio de los paquetes nugets, esta debe de ser instalada usando la Consola de Windows.

```
dotnet tool install -g dotnet-reportgenerator-globaltool
```

\* Con este comando, la herramienta se instalará globalmente. Si el desarrollador así lo desea, puede ser instalado localmente en el directorio de la solución.

## Ejecutar las pruebas con recolección de cobertura de código

Una vez posicionados en el directorio de la solución, el primer paso a realizar, será llamar el commando para la ejecución de la pruebas. A este comando se le deberá añadir los parámetros necesarios para la recolección de la cobertura y el directorio de salida donde poner estos resultados.

```
dotnet test /p:CollectCoverage=true
            /p:CoverletOutputFormat=cobertura
            /p:CoverletOutput="./../TestResults/"
```
## Generar el reporte html

El siguiente paso consistirá en utilizar la herramienta de generación del reporte que instalamos para el dotnet seleccionando el archivo xml generado en el paso anterior y seleccionando que el reporte sea generado en html.

```
reportgenerator.exe "-reports:TestResults\coverage.cobertura.xml"
                    "-targetdir:TestResults\html"
                    "-reporttypes:HTML;"
```
Una vez realizado esto, se podrá acceder al reporte html, ejecutando el navegador sobre el archivos generado.

## Visualizar el reporte

```
.\TestResults\html\index.htm
```

Obteniendo un reporte con las clases que las pruebas unitarias están ejecutando, marcando en verde las líneas de código que si son ejecutadas, mientras las que no se ejecutan marcadas de color rojo.