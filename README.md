#NHI Styleguide

## Table of Contents

  1. [Terms](#terms)
  
  1. [Naming](#naming-classes-and-methods)
  1. [Comments](#comments)
  
  1. [Application Structure](#application-structure)
  1. [Code Guidelines](#code-guidelines)
  1. [One Solution](#one-solution)
  
  1. [Principles](#principles)
  1. [Patterns](#patterns)
  1. [Snippets](#snippets)
  1. [References](#references)

## Terms

### List of suffixes with short description
    - ...Util, ...Helper: 
        - the unavoidable noun you attach for coordinating classes that contain no state and are generally procedural and static.    
    - ...Handler:
        - DO NOT use this suffix in the generic sense, it is reserved for events and callback methods.    
    - ...Provider, ...Manager, ...Service:
        - All of these suffixes are too vague to give any class a meaningful name.
        - For us, they basically mean the same thing. 
        - We will refrain from using all but one unless it makes sense in the context.    
    - ...Service:
        - The lesser of evils, to serve as a gateway for our business/domain- logic 
        - http://programmers.stackexchange.com/a/218394    
    - ...Factory: 
        - In object-oriented programming (OOP), a factory is an object for creating other objects – formally a factory is simply an object that returns an object from some method call, which is assumed to be "new".    
    - ...Repository:
        - In software development, a repository is a central file storage location.
        - The Repository pattern adds a separation layer between the data and domain layers of an application. It also makes the data access parts of an application better testable. 
        - Entity Framework implements a generic repository pattern out of the box
    - ...Model, ...Dto, ...ViewModel
        - In order of appearance, serves: Domain, Domain to Client transfer object, Client.
        - Always apply these suffixes to your class-names regardless of your fancy self-explanatory namespace.

### Helpers
    'Helper' and 'Manager' names usually imply that you're wrapping up a procedural system and pretending it's object-oriented. There's nothing wrong with the procedural approach but if you want the benefits of object orientation then the objects should perform their own behaviour for the most part.
    
    Keep the use of Helpers to a minimum. We want modular components, and Helpers will work against this by creating external dependencies

## Naming
    Naming conventions help provide a consistent way to find and identify content at a glance. Consistency within the project is vital. Consistency with a team is important. Consistency across a company provides tremendous efficiency.
    
    > Don't get naming paralysis. Yes, names are very important but they're not important enough to waste huge amounts of time on. If you can't think up a good name in 5-10 minutes, move on.

### Simple guidelines to naming your class
    - Classes should do one thing and do it well.
    - If a class is hard to name or explain then it's probably not following the advice in the previous bullet point.
    - A class name should instantly communicate what the class is.
    - Good names drive good designs.
    
    > If your problem is what to name exposed internal classes, maybe you should consolidate them into a larger class.
    
    > If your problem is naming a class that is doing a lot of different stuff, you should consider breaking it into multiple classes.
    
    *Ref: http://stackoverflow.com/a/48019*

### Routes / Namespace
    Routes and namespace will provide additional info to you class name, but this will not always be visible.
    
    Include relevant namespace to your class-names:
    ```
    /* recommended */
    
    NHI.Domain/
        Services/
            CustomerService.cs
            SubscriptionService.cs
    
    ```
    
    Avoid long namespaces where possible, and try to keep the number of post-, pre-fix'es to a minimum 
    ```
    /* avoid */
    
    NHI.Common/
        Models/
            Pages/
                Site/
                    SiteDiseasePageModel.cs
    ```

## Comments
    ```c#
    /// <summary>
    /// Returns a single Favorite matching criteria defined in predicate
    /// </summary>
    /// <param name="predicate">Expression of filters to limit the result</param>
    /// <returns>The single element of Favorite that satisfies the condition in predicate, or default(Favorite) if no such element is found.</returns>
    public Favorite Get(Expression<Func<Favorite, bool>> predicate)
    {
        return _context.Favorites.SingleOrDefault(predicate);
    }
    ```

## Application Structure

### Sample 3 Layer Architecture with Entity Framework and ASP.NET MVC
    ```
    NHI.Data
        Context/
        Configurations/             
        Migrations/                 // Schema changes
        Initializers/               // Seed data
        Repositories/
    NHI.Domain
        Services/                   // Non-feature specific services
        Feature1/
        Feature2/
        Enums.cs                    // Enums avaliable to the whole project namespace
    NHI.Domain.Model
    NHI.Client
        Content/                    // Publicly avaliable assets: styles, images, fonts
            NHI/                    // Custom assets specific for the project resides here
            ...                     // Vendor assets will install here in different folders
        Controllers/
            CustomerController.cs
        Models/                     // Viewmodels
            Customer/
                IndexViewModel.cs
        Views/                      
            Customer/
                Index.cshtml        // List of customers
                Edit.cshtml     
                Details.cshtml      // Create / edit customer
    ```

    ```
        Api/
            v1/
                CustomerApiController.cs                    
    ```

## Code Guidelines
    We will follow the MSDN C# Coding Conventions here: https://msdn.microsoft.com/en-us/library/ff926074.aspx

    Additions and modifications to the conventions will be added to this chapter.

### Constants
    ```c#
    public class MyClass
    {
        /* avoid */
        public const ITEMS_PER_PAGE = 13;               // Do not use SCREAMING_CONSTANTS
        
        /* recommended */
        public const ItemsPerPageConstant = 13;         // Why: This convention is consistent with the .NET Framework and is easy to read.
    }
    ```
    
    *Ref - constants: http://10rem.net/articles/net-naming-conventions-and-programming-standards---best-practices*

### Private instance variables
    ```c#
    /* recommended */
    
    private readonly DbContext _dbContext;          // readonly since it is assigned in contructor only (with DI)
    private readonly IMessageDispatcher _messageDispatcher;
    
    public void MyClass(DbContext dbContext, IMessageDispatcher messageDispatcher)
    {
        _dbContext = dbContext;
        _messageDispatcher = messageDispatcher;
    }
    ```

### When to use result !!!TODO!!!
    ```c#
    Aggregate functino() {
        var result= "";
        
        
        if (something) {
            result += "ues";
        }
        
        return result;
    }
    ```

### var result in action methods !!!TODO!!!
    Avoid using generic words like "result". "return View();" takes a parameter "object model", use this info when naming your result-variable.
    
    In this example "result" is avaliable as a (still not recommended) variable name for some pre-processing before passing all the data to the view.
    ```c#
    public ActionResult Index()
    {
        var model = new IndexViewModel();
        
        var result = someProcessor.Transform()
        
        model.Title = "Foo";
        model.Lols = 
        
        return View(model);
    }
    ```

### Object Initializer
    Avoid calling functions inside an object initializer. 
    ```c#
    /* avoid */
    
    var model = new IndexViewModel
    {
        contents = someProcessor.Transform(myContents)
    }
    ```
    
    This makes debugging much easier
    ```c#
    /* recommended */
    
    var transformedContents = someProcessor.Transform(myContents);
    var model = new IndexViewModel
    {
        contents = transformedContents
    }
    ```
    
    Simple variable assignment is ok to use in object initializers.
    ```c#
    /* recommended */
    var someDocument = _context.Get(p => p.Id == id); 
    var model = new IndexViewModel
    {
        title = someDocument.Title
    }
    ```

## One Solution
    - DLL
    - Nuget
    - Branch
    
## Principles

## Patterns

## Snippets

## References
    - http://stackoverflow.com/questions/1866794/naming-classes-how-to-avoid-calling-everything-a-whatevermanager?lq=1
    