Title: I am using Wyam Static Site Generator

Date: 2/11/2017

Tags:

- Wyam
- Razor

---


Back in the day, we use to create static websites with plain HTML files. Some HTML files included many font and color styles.Template based HTML editors like FrontPage were popular. Fast forward to February 2017, we are are talking about static sites again. Ironic isn’t it?

# Modern Static Sites

The modern static sites are usually generated based on dynamic contents. The thing is they don’t get generated on every request. They are generated whenever the content is updated – maybe once a day or once a month. Among many use cases, blogs, and documentation sites are a perfect fit. Imagine how much processing power – in a way, electricity – is being spent on a Wordpress site that queries a MySQL database to get required contents and dynamically renders contents on each request. In contrast, the content you are reading was generated a few day ago along with the new posts or contents that have been added to the site.

Modern Static Site Generators rely on below techniques, tools and technologies:

- Contents are developed with [Markdown](https://en.wikipedia.org/wiki/Markdown) format.
- While in most cases it is not necessary, [Bootstrap](https://en.wikipedia.org/wiki/Bootstrap_(front-end_framework)) can Augment Markdown in some special cases. A prerequisite for this option is that the Static Site theme must be based on Bootstrap. Most themes are.
- Content developers can collaborate with source control repositories. [GitHub](https://en.wikipedia.org/wiki/GitHub) has been a trend in the past few years. [Visual Studio Team Services](https://www.visualstudio.com/team-services/pricing/) and [Bitbucket](https://bitbucket.org/product/pricing?tab=host-in-the-cloud) are excellent options for free private repositories.
- Many usability and interactivity features are added using browser-side JavaScript libraries such as [Mermaid](https://knsv.github.io/mermaid/) or [anchor.js](https://github.com/bryanbraun/anchorjs).
- Rely on available free 3rd party services for the forms or social feature of a site such as [Disqus](https://disqus.com/) or [Google forms](https://www.google.ca/forms/about/).
- FTP is not a common deployment option anymore. There are many automated site build and deployment options available. I personally ~~like~~ love [Git](https://git-scm.com/).

# Why Wyam Static Site Generator?

Firstly, let me cover the alternate options that I had. before getting to know Wyam, I worked with two Static Site Generators [Jekyll](https://en.wikipedia.org/wiki/Jekyll_(software)) and [Hexo](https://hexo.io/). Here are pros and cons in my opinion:

### Jekyll by GitHub

Pros:
- Very nice and convenient integration with GitHub. With little Markdown knowledge, one can create and host a good looking static site on GitHub. Jekyll works behind the scene, and you don’t have to install Jekyll locally for most basic Scenarios.
- The most popular Static Site Generator

Cons:
- While its description says “Jekyll is a simple, blog-aware, static site generator.”, it lacks some basic features requires for blogs. I guess GitHub created Jekyll so projects can create documentation sites inside GitHub. Features related to Blog sites are not on top of the Jekyll backlog. 
- Not sure why, but, Jekyll persists that it does not officially support Windows: “While Windows is not officially supported, it is possible to get Jekyll running on Windows.”. I am having had time to figure out why Jekyll project made no effort to support Windows users since 2008. We are talking about a popular project from a very profitable company. 

### Hexo
Pros:
- Well built and mature project. 
- Multiplatform. It is built based on Node and javaScript. It installs easy and works well on Windows as well as other platforms. Hexo takes a smart and good advantage of NPM. 
- Hexo is mind-blowingly fast. You have to use it on a large content and behold of its performance. After using Hexo, I learned how fast is Google's V8 JavaScript engine.

Cons:
- Its focus is blog sites only. 
- If you are using Hexo for a blog site, I can’t think of anything here.

Ok, let's talk about Wyam. 

## [Wyam](https://wyam.io)

At the time of this writing, there are not too many Static Site Generator options available based on .NET Framework. A reason could be that before .NET Core, multiplatform support was not a priority for .NET Framework as a product.

Wyam is an elegantly engineered Static Site Generator. In my opinion, it is the most modular, flexible and configurable option available while you can use it “as is” without any customization (only configuration).

Besides other benefits, a big advantage of Wyam for me is its use of [Razor Syntax](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor). The combination of the Razor Stntax and [LINQ](https://msdn.microsoft.com/en-ca/library/bb308959.aspx)  makes Wyam [Theme](https://wyam.io/docs/concepts/themes) development and [Recipe](https://wyam.io/docs/concepts/recipes) configuration considerably efficient and easy. Razor Syntax is easier to read and understand. To elaborate my point, let's compare the source code that generate the Archive page in Wyam and Hexo. Below code two snippets should highlight the readability and simplicity of the Razor and LINQ syntax available in Wyam.

The source codes are from the [CleanBlog Bootstrap Theme](https://startbootstrap.com/template-overviews/clean-blog/) that has been ported to both [Hexo](https://github.com/klugjo/hexo-theme-clean-blog) and [Wyam](https://github.com/Wyamio/Wyam/tree/develop/themes/Blog/CleanBlog).

Wyam CleanBlog Theme Code Snippet. Renders the Archive Page. Uses Razor + LINQ:

[Wyam/themes/Blog/CleanBlog/posts/index.cshtml](https://github.com/Wyamio/Wyam/blob/develop/themes/Blog/CleanBlog/posts/index.cshtml) 

```cs
Title: Archive
---
@{
    Layout = "/_Layout.cshtml";
}

@{
    foreach(IGrouping<int, IDocument> year in Documents[BlogPipelines.Posts]
        .Where(x => x.ContainsKey("Published"))
        .OrderByDescending(x => x.Get<DateTime>("Published"))
        .GroupBy(x => x.Get<DateTime>("Published").Year)
        .OrderByDescending(x => x.Key))
    {
        <h1>@year.Key</h1>
        <hr />
        @Html.Partial("_PostList", year);
    }
}
```
<br>

Hexo CleanBlog Theme Code. Renders the Archive Page. Uses [EJS](http://ejs.co/) templating language:

[hexo-theme-clean-blog/layout/archive.ejs](https://github.com/klugjo/hexo-theme-clean-blog/blob/master/layout/archive.ejs) 


```
<%
    var title = '';
    if (page.category) title = page.category;
    if (page.tag) title = "#" + page.tag;
    if (page.archive) {
        if (page.year) title = page.year + (page.month ? '/' + page.month : '');
        else title = __('archive_a');
    }
%>

<!-- Page Header -->
<!-- Set your background image for this header in the theme's configuration: index_cover -->
<header class="intro-header" style="background-image: url('<%- theme.index_cover %>')">
    <div class="container">
        <div class="row">
            <div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">
                <div class="site-heading">
                    <h1><%- title %></h1>
                </div>
            </div>
        </div>
    </div>
</header>

<!-- Main Content -->
<div class="container">
    <div class="row">
        <div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">
            <% page.posts.each(function(item){ %>
                <%- partial('_partial/article-archive', {item: item}) %>
            <% }); %>
            <div class="archive-before-pagination"></div>
            <%- partial('_partial/pagination') %>
        </div>
    </div>
</div>
```





