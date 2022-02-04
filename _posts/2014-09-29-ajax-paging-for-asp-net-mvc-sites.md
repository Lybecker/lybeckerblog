---
id: 1204
title: AJAX paging for ASP.NET MVC sites
date: 2014-09-29T11:33:12+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=1204
permalink: /2014/09/29/ajax-paging-for-asp-net-mvc-sites/
dsq_thread_id:
  - "3456420517"
  - "3456420517"
categories:
  - ASP.Net
tags:
  - AJAX
  - ASP.Net MVC
  - PagedList.Mvc
  - Paging
---
When working with lists of data paging is a necessity, but trivial to implement in an ASP.NET MVC. The article [Sorting, Filtering, and Paging with the Entity Framework in an ASP.NET MVC Application](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application "Article on asp.net website") on [www.asp.net](http://www.asp.net/) makes use of the [X.PackedList.MVC](https://www.nuget.org/packages/X.PagedList.Mvc// "X.PackedList.MVC</ NuGet") NuGet package and each step is described in detail. The implementation requires full-page refresh and does not make use of AJAX capabilities.

Let us extend the implementation and make full use of the AJAX capabilities.

Wrap the entire table in a div-tag and give it an id of _content_ – it will enable us to replace the table without refreshing the entire webpage.

<pre class="brush: xml; highlight: [1,9]; title: ; notranslate" title="">&lt;div id="content"&gt;
    &lt;table&gt;
        ... removed for brevity
    &lt;/table&gt;

    &lt;div id="contentPager"&gt;
        @Html.PagedListPager(Model, page =&gt; Url.Action("Index", new { page }))
    &lt;/div&gt;
&lt;/div&gt;
</pre>

Also wrap the `@Html.PagedListPager` in a div-tag and set the id to _contentPager_ – it will let us alter the behavior of the click-event.

The below JQuery code attaches the anonymous function to every a-tag within the _contentPager_ element and the function replaces the html within the _content_ element with whatever is returned from the AJAX call.

<pre class="brush: jscript; title: ; notranslate" title="">$(document).on("click", "#contentPager a", function () {
    $.ajax({
        url: $(this).attr("href"),
        type: 'GET',
        cache: false,
        success: function (result) {
            $('#content').html(result);
        }
    });
    return false;
});
</pre>

Move everything within the _content_ element to a new view – let us call the new view _List_.

<pre class="brush: xml; highlight: [10]; title: ; notranslate" title="">@model PagedList.IPagedList&lt;ContosoUniversity.Models.Student&gt;;
@using PagedList.Mvc;

&lt;div id="content"&gt;
    &lt;table class="table"&gt;
      ... removed for brevity
    &lt;/table&gt;

    &lt;div id="contentPager"&gt;
        @Html.PagedListPager(Model, page =&gt; Url.Action("List", new { page }))
    &lt;/div&gt;
&lt;/div&gt;
</pre>

Notice in above highlighted code, that the Action URL is changed to _List_, which is the name of the Action we need to add to the `StudentController`.

<pre class="brush: csharp; title: ; notranslate" title="">public ActionResult List(int? page)
{
    var students = from s in db.Students
                    orderby s.LastName
                    select s;

    int pageSize = 3;
    int pageNumber = (page ?? 1);
    return View(students.ToPagedList(pageNumber, pageSize));
}
</pre>

The functionality of the new _List_ Action is the same as in the existing _Index_ Action. Just move all the code from the _Index_ Action, so it just returns the default view as below.

<pre class="brush: csharp; title: ; notranslate" title="">public ViewResult Index()
{
    return View();
}
</pre>

To wrap it up, the _Index_ view needs to call the _List_ Action to render the table in the _Index_ view.

So the _Index_ view ends up looking like this.

<pre class="brush: xml; highlight: [7]; title: ; notranslate" title="">&lt;link href="~/Content/PagedList.css" rel="stylesheet" type="text/css" /&gt;
@{
    ViewBag.Title = "Students";
}
&lt;h2&gt;Students&lt;h2&gt;

@Html.Action("List")

@section scripts
{
    &lt;script language="javascript" type="text/javascript"&gt;
        $(document).ready(function () {
           $(document).on("click", "#contentPager a[href]", function () {
                $.ajax({
                    url: $(this).attr("href"),
                    type: 'GET',
                    cache: false,
                    success: function (result) {
                        $('#content').html(result);
                    }
                });
                return false;
            });
        });
    &lt;/script&gt;
}
</pre>

That is it.

The solution is inspired by [this StackOverflow question](http://stackoverflow.com/questions/18822352/using-paging-in-partial-view-asp-net-mvc).

<a href="/blog/wp-content/uploads/AspNetMvcAjaxPaging.zip" rel="attachment wp-att-1242">Download the complete solution</a>, build and open the Student page. If running the solution in Visual Studio 2015+, then change the data source connection string in web.config to (localdb)\_MSSQLLocalDB_ as the default SQL Server LocalDB instance name has changed.

**Update** May 15th 2016: The [PagedList.Mvc](https://www.nuget.org/packages/PagedList.Mvc) NuGet package is no longer maintained, but a fork of the project is available and maintained called [X.PagedList.MVC](https://www.nuget.org/packages/X.PagedList.Mvc/). I have updated the post and the source to use this new package.

Juanster and others pointed out a bug in the sample, but it was actually in the PagedList.MVC. I created a [pull request](https://github.com/kpi-ua/X.PagedList/pull/14) for X.PagedList.MVC, which is now part of the NuGet package.