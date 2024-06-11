# Creating our Blog Post web page and Actions

## _Layout.cshtml

We will create a Black Navbar for all of the pages.

```bash
   <header>
       <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-dark bg-dark border-bottom box-shadow mb-3">
```

In the Navbar we have added ``navbar-dark`` and ``bg-dark``.

We also change the two other menu items to ``text-white`` so that we can see them.

```bash
<li class="nav-item">
    <a class="nav-link text-white" asp-area="" asp-page="/Index">Home</a>
</li>
<li class="nav-item">
    <a class="nav-link text-white" asp-area="" asp-page="/Privacy">Privacy</a>
</li>
```

The Navbar now looks like this.

![Navbar](assets/images/menu-bar.jpg "Navbar")

## Creating a new Page for Blog Posts

This is an Admin page so we will create the folder structure ``Admin-->Blogs`` in the ``Pages`` folder.

Add a new Empty Razor page named ``Add`` which will be used to create a new Blog Post.

These are the two templates that are built for you.

### Add.cshtml

```bash
    @page
    @model Blog.Pages.Admin.Blogs.AddModel
    @{
    }
```

### Add.cshtml.cs

```bash
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace Blog.Pages.Admin.Blogs
    {
        public class AddModel : PageModel
        {
            public void OnGet()
            {
            }
        }
    }
```

We can navigate to this page with.

> <https://localhost:7100/admin/blogs/add>

Go back to the ``_layout.cs`` page and add an Admin section to the menu.

```bash
<li class="nav-item dropdown">
    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown" 
        aria-expanded="false" id="navbarDropdownAdmin">
        Admin
    </a>
    <ul class="dropdown-menu">
        <li>
            <a class="dropdown-item" href="/admin/blogs/add">Add Blog Post</a>
        </li>
    </ul>
</li>
```

This will look like.

![Dropdown menu](assets/images/dropdown-menu.jpg "Dropdown menu")

Make another small change to the ``_layout.cs`` page. Remove the Container class div from around ``main``.

```bash
    </header>
    <main role="main" class="pb-3">
        @RenderBody()
    </main>

    <footer class="border-top footer text-muted">
```

## Create an Add Blog Post Razor form and Bind property

``Add.cshtml``

```bash
@page
@model Blog.Pages.Admin.Blogs.AddModel
@{
}

<div class="bg-secondary bg-opacity-10 py-2 mb-5">
    <div class="container">
        <h2>Add Blog page</h2> 
    </div>
</div>

<div>
    <div class="container">
        <form method="post">
            <div class="mb-3">
                <label for="heading" class="form-label">Heading</label>
                <input type="text" required class="form-control" id="heading" name="heading">
            </div>

            <div class="mb-3">
                <label for="pageTitle" class="form-label">Page Title</label>
                <input type="text" required class="form-control" id="pageTitle" name="pageTitle">
            </div>

            <div class="mb-3">
                <label for="content" class="form-label">Content</label>
                <textarea class="form-control" required id="content" name="content"></textarea>
            </div>

            <div class="mb-3">
                <label for="shortDescription" class="form-label">Short Description</label>
                <input type="text" required class="form-control" id="shortDescription" name="shortDescription">
            </div>

            <div class="mb-3">
                <label for="featuredImageUrl" class="form-label">Featured Image Url</label>
                <input type="text" required class="form-control" id="featuredImageUrl" name="featuredImageUrl">
            </div>

            <div class="mb-3">
                <label for="urlHandle" class="form-label">Url Handle</label>
                <input type="text" required class="form-control" id="urlHandle" name="urlHandle">
            </div>

            <div class="mb-3">
                <label for="publishedDate" class="form-label">Published Date</label>
                <input type="date" required class="form-control" id="publishedDate" name="publishedDate">
            </div>

            <div class="mb-3">
                <label for="author" class="form-label">Author</label>
                <input type="text" required class="form-control" id="author" name="author">
            </div>

            <div class="form-check mb-3">
                <input class="form-check-input" type="checkbox" id="isVisible" name="isVisible">
                <label class="form-check-label" for="isVisible">
                    Is Visible
                </label>
            </div>

            <div class="mb-3">
                <button class="btn btn-primary" type="submit">Submit</button>
            </div>
        </form>
    </div>
</div>
```

In our CodeBehind page we have an ``OnGet()`` method and we also need an ``OnPost()`` method.

There are a number of ways that we can get the data from the Form to the ``OnPost()`` method.

We have added the ``name`` attribute to each field and will use this to retrieve the data in the CodeBehind page.

```bash
    public void OnPost()
    {
        var heading = Request.Form["heading"];
        var pageTitle = Request.Form["pageTitle"];
        var content = Request.Form["content"];
        var shortDescription = Request.Form["shortDescription"];
        var featuredImageUpload = Request.Form["featuredImageUpload"];
        var featuredImageUrl = Request.Form["featuredImageUrl"];
        var urlHandle = Request.Form["urlHandle"];
        var publishedDate = Request.Form["publishedDate"];
        var author = Request.Form["author"];
        var tags = Request.Form["tags"];
        var isVisible = Request.Form["isVisible"];
    }
```

This is the old way of returning data. it grabs the ``name`` field from the form.

A slightly better way is to add properties to the CodeBehind page. First you need to change all of the ``name`` attributes on the ``Add.cshtml`` page to ``asp-for`` tag helpers. E.g.

```bash
    <input type="text" required class="form-control" id="heading" asp-for="Heading">
```

In the ``Add.cshtml.cs`` CodeBehind.

```bash
    [BindProperty]
    public string Heading { get; set; }
    
    [BindProperty]
    public string PageTitle { get; set; }
```

In the ``OnPost()`` method you can call these properties to get their values once you Post the ``Add`` page.

```bash
    public void OnPost()
    {
        var heading = Heading;
        var pageTitle = PageTitle;
    }
```

This method will clutter up the CodeBehind class. A much better idea is to create a ViewModel. We do this by creating a ``ViewModels`` folder inside the ``Models`` folder.

Now create a class named ``AddBlogPost.cs`` in ``ViewModels``.

```bash
    public class AddBlogPost
    {
        [Required]
        public string Heading { get; set; }

        [Required]
        public string PageTitle { get; set; }

        [Required]
        public string Content { get; set; }

        [Required]
        public string ShortDescription { get; set; }

        [Required]
        public string FeaturedImageUrl { get; set; }

        [Required]
        public string UrlHandle { get; set; }

        [Required]
        public DateTime PublishedDate { get; set; }

        [Required]
        public string Author { get; set; }

        public bool Visible { get; set; }
    }
```

Now we have to change the ``Add.cshtml`` page.

```bash
@page
@model Blog.Pages.Admin.Blogs.AddModel
@{
}

<div class="bg-secondary bg-opacity-10 py-2 mb-5">
    <div class="container">
        <h2>Add Blog page</h2> 
    </div>
</div>

<div>
    <div class="container">
        <form method="post">
            <div class="mb-3">
                <label for="heading" class="form-label">Heading</label>
                <input type="text" class="form-control" id="heading" asp-for="AddBlogPostRequest.Heading">
            </div>

            <div class="mb-3">
                <label for="pageTitle" class="form-label">Page Title</label>
                <input type="text" class="form-control" id="pageTitle" asp-for="AddBlogPostRequest.PageTitle">
            </div>

            <div class="mb-3">
                <label for="content" class="form-label">Content</label>
                <textarea class="form-control" id="content" asp-for="AddBlogPostRequest.Content"></textarea>
            </div>

            <div class="mb-3">
                <label for="shortDescription" class="form-label">Short Description</label>
                <input type="text" class="form-control" id="shortDescription" asp-for="AddBlogPostRequest.ShortDescription">
            </div>

             <div class="mb-3">
                <label for="featuredImageUrl" class="form-label">Featured Image Url</label>
                <input type="text" class="form-control" id="featuredImageUrl" asp-for="AddBlogPostRequest.FeaturedImageUrl">
            </div>

            <div class="mb-3">
                <label for="urlHandle" class="form-label">Url Handle</label>
                <input type="text" class="form-control" id="urlHandle" asp-for="AddBlogPostRequest.UrlHandle">
            </div>

            <div class="mb-3">
                <label for="publishedDate" class="form-label">Published Date</label>
                <input type="date" class="form-control" id="publishedDate" asp-for="AddBlogPostRequest.PublishedDate">
            </div>

            <div class="mb-3">
                <label for="author" class="form-label">Author</label>
                <input type="text" class="form-control" id="author" asp-for="AddBlogPostRequest.Author">
            </div>

            <div class="form-check mb-3">
                <input class="form-check-input" type="checkbox" id="isVisible" asp-for="AddBlogPostRequest.Visible">
                <label class="form-check-label" for="isVisible">
                    Is Visible
                </label>
            </div>

            <div class="mb-3">
                <button class="btn btn-primary" type="submit">Submit</button>
            </div>
        </form>
    </div>
</div>
```

## Save to Database

We can now work on the CodeBehind. The first thing we need to do is create an object to store the Form content that a user enters. We will use the ViewModel class that we created, ``AddBlogPost``.

We need to use the ``[BindProperty]`` to bind the Form data.

```bash
    [BindProperty]
    public AddBlogPost AddBlogPostRequest { get; set; }
```

The next thing we need to do is inject the DbContext into class using the constructor. We created this context in our ``Program.cs`` class. We can use this context in our methods in the class.

```bash
    private readonly BlogDbContext blogDbContext
    
    public AddModel(BlogDbContext blogDbContext)
    {
        this.blogDbContext = blogDbContext;
    }
```

The Form content that is in the ``AddBlogPostRequest`` object needs to be mapped to the Domain model object, ``BlogPost``.

Create a new ``BlogPost`` object in the ``OnPost()`` method and map the fields to it.

```bash
    var blogPost = new BlogPost()
    {
        Heading = AddBlogPostRequest.Heading,
        PageTitle = AddBlogPostRequest.PageTitle,
        Content = AddBlogPostRequest.Content,
        ShortDescription = AddBlogPostRequest.ShortDescription,
        FeaturedImageUrl = AddBlogPostRequest.FeaturedImageUrl,
        UrlHandle = AddBlogPostRequest.UrlHandle,
        PublishedDate = AddBlogPostRequest.PublishedDate,
        Author = AddBlogPostRequest.Author,
        Visible = AddBlogPostRequest.Visible
    };
```

To save the ``blogPost`` object into the database we need to use two steps.

The first step is to add the ``blogPost`` object into the DBSet method, ``BlogPosts``.

```bash
    await blogDbContext.BlogPosts.AddAsync(blogPost);
```

Next we need to use the DbContext to save the object to the database.

```bash
    await blogDbContext.SaveChangesAsync();
```

If you don't do this step it won't save the changes to the database.

### Add.cshtml.cs

```bash
    namespace Blog.Pages.Admin.Blogs
    {
        public class AddModel : PageModel
        {
            private readonly BlogDbContext blogDbContext;

            [BindProperty]
            public AddBlogPost AddBlogPostRequest { get; set; }

            public AddModel(BlogDbContext blogDbContext)
            {
                this.blogDbContext = blogDbContext;
            }

            public void OnGet()
            {
            }

            public async Task OnPost()
            {
                var blogPost = new BlogPost()
                {
                    Heading = AddBlogPostRequest.Heading,
                    PageTitle = AddBlogPostRequest.PageTitle,
                    Content = AddBlogPostRequest.Content,
                    ShortDescription = AddBlogPostRequest.ShortDescription,
                    FeaturedImageUrl = AddBlogPostRequest.FeaturedImageUrl,
                    UrlHandle = AddBlogPostRequest.UrlHandle,
                    PublishedDate = AddBlogPostRequest.PublishedDate,
                    Author = AddBlogPostRequest.Author,
                    Visible = AddBlogPostRequest.Visible
                };

                await blogDbContext.BlogPosts.AddAsync(blogPost);
                await blogDbContext.SaveChangesAsync();
            }
        }
    }
```

Once we fill in the form data and submit we are sent back to the Add form. This isn't ideal and we will change this behavior when we create a Blog post list next.

## Show all Blog Posts

We will create a List view named ``List.cshtml`` in the ``Admin->Blogs`` folder.

In the CodeBehind class inject the DbContext into the Constructor.

To create a list of ``BlogPosts`` you have to create a public List of ``BlogPost``. By doing this the list can be available to the Razor page. We do this with.

```bash
    public List<BlogPost> BlogPosts { get; set; }
```

To create the list of ``BlogPosts`` use the ``OnGetAsync()`` method with this statement.

```bash
    public async Task OnGet()
    {
        BlogPosts = await blogDbContext.BlogPosts.ToListAsync();
    }
```

Once again we are using the DbContext to bring back a list of DBSet ``BlogPosts`` and then creating a list with ``ToListAsync()``.

### List.cshtml.cs

```bash
    public class ListModel : PageModel
    {
        public List<BlogPost> BlogPosts { get; set; }

        private readonly BlogDbContext blogDbContext;
        
        public ListModel(BlogDbContext blogDbContext)
        {
            this.blogDbContext = blogDbContext;
        }

        public async Task OnGet()
        {
            BlogPosts = await blogDbContext.BlogPosts.ToListAsync();
        }
    }
```

**Note:** because the list of ``BlogPosts`` in ``OnGetAsync()`` is public they are available to List.cshtml.

### List.cshtml

```bash
@page
@model Blog.Pages.Admin.Blogs.ListModel
@{
}

<div class="bg-secondary bg-opacity-10 py-2 mb-5">
    <div class="container">
        <h2>Blog Posts</h2>
    </div>
</div>

<!-- <partial name="_Notification"> -->
@if (Model.BlogPosts != null && Model.BlogPosts.Any())
{
    <div class="container">
        <table class="table">
            <thead>
                <tr>
                    <td>Id</td>
                    <td>Heading</td>
                    <td> </td>
                </tr>
            </thead>
            <tbody>
                @foreach (var blogPost in Model.BlogPosts)
                {
                    <tr>
                        <td>@blogPost.Id</td>
                        <td>@blogPost.Heading</td>
                        <td><a href="/Admin/Blogs/Edit/@blogPost.Id">View</a></td>
                    </tr>
                }
            </tbody>
        </table>
    </div>
}
else
{
    <div class="container">
        <p>No blog posts were found!</p>
    </div>
}
```

Once again we can add an ``Admin-->Blogs-->List`` link into ``_Layout.cs``.

## Edit or Update page

On our list page we have an Edit button for each row. Inside the link we have the ``@blogPost.Id`` and we will use this to get the Blog Post from the database.

At the top of the ``Edit.cshtml`` page we need to add the following.

```bash
    @page "{id:int}"
```

This is the ``id`` route parameter and we make it type safe by adding the type (``int``) next to it.

In the ``Edit.cshtml.cs`` CodeBehind class we will add the route parameter to the ``OnGet()`` method.

We inject our DbContext and create a public property to hold the ``BlogPost`` object.

**Note:** I wasn't aware that by making a public property you make it available to the Razor page. This is important to remember.

We then use the DbContext to return the Post that is identified by the route parameter.

```bash
    public async Task OnGet(int id)
    {
        BlogPost = await blogDbContext.BlogPosts.FindAsync(id);
    }
```

### Edit.cshtml.cs

```bash
    public class EditModel : PageModel
    {
        [BindProperty]
        public BlogPost BlogPost { get; set; }

        private readonly BlogDbContext blogDbContext;

        public EditModel(BlogDbContext blogDbContext)
        {
            this.blogDbContext = blogDbContext;
        }

        public async Task OnGet(int id)
        {
            BlogPost = await blogDbContext.BlogPosts.FindAsync(id);
        }
    }
```

**Note:** before I build the Razor page I can test that the DbContext is returning the Post by adding a breakpoint on the DbContext statement in the ``OnGet()`` method. When I run this I can see my data in ``BlogPost``.

### Edit.cshtml

```bash
@page "{id:int}"
@model Blog.Pages.Admin.Blogs.EditModel
@{
}

<div class="bg-secondary bg-opacity-10 py-2 mb-5">
    <div class="container">
        <h1>Edit Blog Post</h1>
    </div>
</div>

@if (Model.BlogPost != null)
{
    <div>
        <div class="container">
            <form method="post">
                <div class="mb-3">
                    <label for="heading" class="form-label">Id</label>
                    <input type="text" class="form-control" id="id" asp-for="BlogPost.Id" readonly>
                    <span class="text-danger" asp-validation-for="BlogPost.Id"></span>
                </div>

                <div class="mb-3">
                    <label for="heading" class="form-label">Heading</label>
                    <input type="text" class="form-control" id="heading" asp-for="BlogPost.Heading">
                </div>

                <div class="mb-3">
                    <label for="pageTitle" class="form-label">Page Title</label>
                    <input type="text" class="form-control" id="pageTitle" asp-for="BlogPost.PageTitle">
                </div>

                <div class="mb-3">
                    <label for="content" class="form-label">Content</label>
                    <textarea class="form-control" id="content" asp-for="BlogPost.Content"></textarea>
                </div>

                <div class="mb-3">
                    <label for="shortDescription" class="form-label">Short Description</label>
                    <input type="text" class="form-control" id="shortDescription" asp-for="BlogPost.ShortDescription">
                </div>

                <div class="mb-3">
                    <label for="featuredImageUrl" class="form-label">Featured Image Url</label>
                    <input type="text" class="form-control" id="featuredImageUrl" asp-for="BlogPost.FeaturedImageUrl">
                    <span class="text-danger" asp-validation-for="BlogPost.FeaturedImageUrl"></span>
                </div>

                <div class="mb-3">
                    <label for="urlHandle" class="form-label">Url Handle</label>
                    <input type="text" class="form-control" id="urlHandle" asp-for="BlogPost.UrlHandle">
                </div>

                <div class="mb-3">
                    <label for="publishedDate" class="form-label">Published Date</label>
                    <input type="date" class="form-control" id="publishedDate" asp-for="BlogPost.PublishedDate">
                </div>

                <div class="mb-3">
                    <label for="author" class="form-label">Author</label>
                    <input type="text" class="form-control" id="author" asp-for="BlogPost.Author">
                </div>

                <div class="form-check mb-3">
                    <input class="form-check-input" type="checkbox" id="isVisible" asp-for="BlogPost.Visible">
                    <label class="form-check-label" for="isVisible">
                        Is Visible
                    </label>
                </div>

                <div class="mb-3 d-flex justify-content-between">
                    <button class="btn btn-primary" type="submit" asp-page-handler="Edit">Submit</button>

                    <button class="btn btn-danger" type="submit" asp-page-handler="Delete">Delete</button>
                </div>

            </form>
        </div>
    </div>
}
else
{
    <div class="container">
        <p>Blog Post Not Found!</p>
    </div>
}
```

When I run this it returns the Edit Form. We are now ready to write the ``OnPost()`` method.

**Note:** We need to add ``[BindProperty]`` to our ``BlogPost`` property. This will make the property have 2-way binding and this is necessary for both the ``OnGet()`` and ``OnPost()`` methods.
