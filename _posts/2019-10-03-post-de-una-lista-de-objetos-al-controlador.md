---
title: Post de una lista de objetos al controlador
date: 2019-10-03 12:00:00 -700
categories: [.NET MVC]
tags: [.net core, asp mvc, enlace de datos]     # TAG names should always be lowercase
---

Cuando necesitamos hacer un post de una lista de objetos desde la vista a un controlador, .Net nos ayuda haciendo el *enlace de modelos ([Model Binding](https://docs.microsoft.com/es-mx/aspnet/core/mvc/models/model-binding){:target="_blank"})* desde nuestra forma html a un lista de objectos.

``` c#
[HttpPost]
public IActionResult Save(List<Person> model)
{
    //Manipular datos
    return View(model);
}
```

Sin embargo, del lado de la vista el trabajo no es tan sencillo como lo pudiéramos adivinar. Si bien, la vista recibe una *lista de objectos* como modelo, nuestro primer pensamiento es utilizar un ciclo *for-each*. Pero en este caso, para lograr enviar estos datos de manera que el *enlace de modelo* lo pueda interpretar correctamente debemos de usar un ciclo *for* en su lugar. Esto por la forma en que los elemento son nombrados e identificados en la forma html.

Por ejemplo, si queremos mostrar la lista de un modelo persona en una tabla html y posteriormente enviar los valores correspondientes a nuestro controlador, tendriamos un código similar a este:

``` html
@model List<ModelListBinding.Models.Person>

<form asp-action="Save" method="post" role="form">
    <table>
        <thead>
            <tr>
                <th><label asp-for="@Model[0].FirstName" class="control-label"></label></th>
                <th><label asp-for="@Model[0].LastName" class="control-label"></label></th>
                <th><label asp-for="@Model[0].Birthday" class="control-label"></label></th>
                <th><label asp-for="@Model[0].IsActive" class="control-label"></label></th>
            </tr>
        </thead>
        @for (int i = 0; i < Model.Count; i++)
        {
            <tr>
                <td>
                    <input asp-for="@Model[i].id" type="hidden" />
                    <input asp-for="@Model[i].FirstName" class="form-control" />
                </td>
                <td>
                    <input asp-for="@Model[i].LastName" class="form-control" />
                </td>
                <td>
                    <input asp-for="@Model[i].Birthday" class="form-control" />
                </td>
                <td>
                    <label class="form-check-label">
                        <input class="form-check-input" asp-for="@Model[i].IsActive" />
                    </label>
                </td>
            </tr>
        }
    </table>
    <input type="submit" value="Save" class="btn btn-default" />
</form>
```

Con esto permitiremos los datos sean nombrados correctamente en el código html final para posteriormente sean transportados al controlador en el submit de la forma.

Como referencia, aquí el *modelo* utilizado en el ejemplo:

``` c#
public class Person
{
    public int id { get; set; }

    [Display(Name = "First Name")]
    public string FirstName { get; set; }

    [Display(Name = "Last Name")]
    public string LastName { get; set; }

    [Display(Name = "Birthday")]
    public DateTime Birthday { get; set; }

    [Display(Name = "Is Active")]
    public bool IsActive { get; set; }
}
```

Adicionalmente, para Asp .Net MVC se pueden utilizar los métodos extendidos usuales de la forma:

```
@Html.TextBoxFor(m => m[i].LastName, new { @class="form-control" })
```