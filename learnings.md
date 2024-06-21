# ASP.NET Core

Framework for making web apps, APIs, real-time apps using SignalR.
Offers infrastructure for handling HTTP requests, routing, middleware, etc.

# Routing

The process of mapping URLs to corresponding actions and controllers.
It redirects an incoming request to the appropriate endpoint in the application.

Example:

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();
    
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
    });
}

Here the UseRouting method is added to the middleware pipeline to enable routing capabilities. The MapControllerRoute method defines a route pattern that maps URLs to controller actions.

# Middleware

A component that can handle HTTP requests and responses.
It forms a pipeline that gets invoked on every request.
Allows you to add logic at specific stages of the request processing pipeline.
Common uses: logging, exception handling, security, compression, etc.

Example:

public class RequestLoggerMiddleware
{
    private readonly RequestDelegate _next;

    public RequestLoggerMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Log the request before executing the next middleware
        Console.WriteLine($"Request: {context.Request.Path}");

        // Call the next middleware in the pipeline
        await _next(context);

        // Log the response after executing the next middleware
        Console.WriteLine($"Response: {context.Response.StatusCode}");
    }
}

Here we defined a middleware component that logs the request
path before executing the next middleware, and logs the response
status code after executing the next middleware.

# Aspose

Nothing to do with dotnet. Provides various dotnet APIs for the creation/manipulation/conversion of various file formats.
Word, Excel, PowerPoint, PDF, HTML, images, email formats, etc.

# Model-View-Controller (MVC) pattern

Design pattern used to separate app into 3 interconnected components: Model-View-Controller

How they work together:
The user interacts with the View (e.g. by clicking a button)
The View sends the user's action to the Controller.
The Controller processes the input and interacts with the Model to update/retrieve data.
The Controller then selects a View to display the updated data to the user.

Model [data]
	Represents the data and business logic of the app.
	It has methods to change/retrieve data.
	E.g. in a library management system, a Book model would contain properties like Title, Author, ISBN, and methods to save/retrieve book information from a database.

View [user interface]
	Represents the presentation aspect of the app.
	Displays the data to the user and sends user commands to the controller.
	Renders the user interface / Display data from the model / Capture user input.
	E.g. in the same library management system, a BookDetails view would display the book's details to the user in a webpage.

Controller []
	Intermediate between Model and View.
	Listens to the input from the View, processes it using the Model, returns the output display to the View.
	Handles user input / Interacts with the model to retrieve/update data / Select the view to render that data.
	E.g. in the same library management system, a BooksController would handle requests to view a list of books, view details of specific book, or add new book.

	Handles incoming HTTP requests, processes them, and returns response.
	Intermediate between the View (user interface) and Model (data).

# Model example

namespace MyMvcApp.Models
{
	public class Book
	{
		public int Id ;
		public string Title ;
		public string Author ;
		public string ISBN ;
	}
}

# View example

<!-- Views/Books/Details.cshtml -->
@model MyMvcApp.Models.Book

<!DOCTYPE html>
<html>
<head>
    <title>Book Details</title>
</head>
<body>
    <h1>Book Details</h1>
    <p>Title: @Model.Title</p>
    <p>Author: @Model.Author</p>
    <p>ISBN: @Model.ISBN</p>
</body>
</html>

# Controller example

namespace MyMvcApp.Controllers
{
	public class BooksController : Controller
	{
		private static List<Book> Books = new List<Book>
		{
			new Book { Id = 1, Title = "1984", Author = "George Orwell", ISBN = "123456789" },
			new Book { Id = 2, Title = "To Kill a Mockingbird", Author = "Harper Lee", ISBN = "123456789" },
		};

		public IActionResult Details(int id)
		{
			var book = Books.FirstOrDefault(b => b.Id == id); // ???
			if (book == null)
			{
				return (NotFound()); 
			}
			return (View(book));
		}
	}
}

# Asynchronous Programming (async/await)

Allows code to run without blocking the main thread.
Improves performance and responsiveness of applications.
Useful for I/O-bound operations like network calls, file I/O, database queries.

The await keyword is used to pause execution until the awaited task completes.
The async keyword is used to mark a method that runs asynchronously.

Example:

public async Task<int> GetDataFromServerAsync()
{
    // Simulating a slow network call
    await Task.Delay(5000); 

    return 42;
}

public async Task CallAsyncMethod()
{
    int result = await GetDataFromServerAsync();
    Console.WriteLine($"Result: {result}");
}

In this example, the GetDataFromServerAsync method simulates a
slow network call using Task.Delay. The await keyword is used to
pause execution until the delay is over, allowing the main thread
to remain responsive. The CallAsyncMethod method demonstrates how
to call an async method and await its result.

# Dependency Injection

Technique for achieving Inversion of Control (IoC) between classes
and their dependencies. Classes no longer create their dependencies,
but instead receive them from an external source. Promotes loose
coupling and improves testability and maintainability.

Example:

// Service implementation
public class EmailService : IEmailService
{
    public void SendEmail(string to, string subject, string body)
    {
        // Send email logic...
    }
}

// Controller
public class UserController : Controller
{
    private readonly IEmailService _emailService;

    public UserController(IEmailService emailService)
    {
        _emailService = emailService;
    }

    public IActionResult RegisterUser(string email)
    {
        // Use the injected email service
        _emailService.SendEmail(email, "Registration Confirmation", "Thank you for registering!");

        return Ok();
    }
}

In this example, the UserController depends on the IEmailService interface.
Instead of creating an instance of the EmailService directly, the controller
receives an instance of IEmailService through its constructor, allowing for
easier testing and replacement of the email service implementation.


# Authentication

Authentication is the process of verifying the identity of a user. ASP.NET Core provides several authentication mechanisms, such as cookies, tokens, and external providers like OAuth.

Example:

public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
            .AddCookie(options =>
            {
                options.LoginPath = "/Account/Login";
                options.AccessDeniedPath = "/Account/AccessDenied";
            });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
}

In this example, cookie-based authentication is configured with specified paths for login and access denied actions.

# Authorization

Authorization is the process of determining what an authenticated user is allowed to do. It involves configuring policies and roles to restrict access to certain parts of the application.


Example:

public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy("AdminOnly", policy => policy.RequireRole("Admin"));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthorization();
}


In this example, an authorization policy named "AdminOnly" is configured to require the user to have the "Admin" role.

# Applying Authorization

Authorization can be applied to controllers or actions using attributes.

Example:

[Authorize(Policy = "AdminOnly")]
public class AdminController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}

In this example, the AdminController is restricted to users who meet the "AdminOnly" policy.


# Unit Testing

Unit testing is a software testing technique where individual units or components of a software are tested in isolation. In ASP.NET Core, unit tests can be written using testing frameworks like xUnit, MSTest, or NUnit.
Example
Model Test

public class BookTests
{
    [Fact]
    public void CanChangeBookTitle()
    {
        // Arrange
        var book = new Book { Title = "Original Title" };

        // Act
        book.Title = "New Title";

        // Assert
        Assert.Equal("New Title", book.Title);
    }
}


In this example, a unit test for the Book model ensures that the title can be changed.

# Controller Test

public class BooksControllerTests
{
    [Fact]
    public void Details_ReturnsViewResult_WithBook()
    {
        // Arrange
        var controller = new BooksController();

        // Act
        var result = controller.Details(1);

        // Assert
        var viewResult = Assert.IsType<ViewResult>(result);
        var model = Assert.IsType<Book>(viewResult.ViewData.Model);
        Assert.Equal(1, model.Id);
    }
}

In this example, a unit test for the BooksController ensures that the Details action returns a ViewResult with the correct Book model.
