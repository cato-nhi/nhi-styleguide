#NHI Styleguide

## Table of Contents

  1. [Terms](#terms)  
  1. [Naming](#naming-classes-and-methods)
  1. [Comments](#comments)  
  1. [Application Structure](#application-structure)
  1. [Design Patterns](#design-patterns)
  1. [Code Guidelines](#code-guidelines)
  1. [One Solution](#one-solution)  
  1. [Principles](#principles)
  1. [Patterns](#patterns)
  1. [Snippets](#snippets)

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
*Ref: http://stackoverflow.com/questions/1866794/naming-classes-how-to-avoid-calling-everything-a-whatevermanager?lq=1*

### Booleans
Try to prefix boolean variable-names with: Is, Has, Can 
```
    /* avoid */
    bool IsMarriedOrNot;
    bool ShouldImplement;
    
    /* recommended */
    bool IsMarried;
    bool HasChildren;
```

Functions that return boolean value should be named as a yes-no question.
```
    IsRetrievable();
    ContainsSomething();
```

## Comments
Always try to leave helpful comments, use 3 x / + [Tab] to insert templated comment-structure
```csharp
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

### Sample 3 Layer Structure with Entity Framework and ASP.NET MVC
```
NHI.Data
    Context/
    Configurations/             
    Migrations/                     // Schema changes
    Initializers/                   // Seed data
    Repositories/
NHI.Domain
    Services/                       // Non-feature specific services
        CustomerService.cs          // CRUD-service to CustomerModel 
                                    // and domain-logic for CustomerContext
        CustomerCommentService.cs   // CRUD-service to CustomerCommentModel
    NHILogger/                      // Feature with custom name
    MessageDispatcher/              // Another example feature with relevant custom name
    Enums.cs                        // Enums avaliable to the whole project namespace
NHI.Domain.Model/
    CustomerContext/
        CustomerModel.cs
        CustomerCommentModel.cs
        CustomerProfessionModel.cs
    MessageContext/
        MessageModel.cs
        MessageDeliveryTypeModel.cs
        MessageTemplateModel.cs
        
NHI.Client
    Content/                        // Publicly avaliable assets: styles, images, fonts
        NHI/                        // Custom assets specific for the project resides here
        ...                         // Vendor assets will install here in different folders
    Controllers/
        Api/
            v1/
                CustomerApiController.cs                    
        CustomerController.cs
    Models/                         // Viewmodels
        Customer/
            IndexViewModel.cs
    Views/                      
        Customer/
            Index.cshtml            // List of customers
            Edit.cshtml     
            Details.cshtml          // Create / edit customer
```

## Design Patterns

### Simplified code-slice from previous sample 3 layer structure
```csharp
class UserController : Controller 
{
    private UserService _userService;

    public UserController(UserService userService)
    {
        _userService = userService;
    } 

    public ActionResult MakeHimPay(int userId, int amount) 
    {
        _userService.MakeHimPay(userId, amount);
        return RedirectToAction("ShowUserOverview");
    }

    public ActionResult ShowUserOverview() 
    {
        return View();
    }
}

class UserService 
{
    private IUserRepository _userRepository;

    public UserService(IUserRepository userRepository) 
    {
        _userRepository = userRepository;
    }

    public void MakeHimPay(userId, amount) 
    {
        var user = _userRepository.Get(p => p.userId);        
        user.Debt -= amount;        
        _userRepository.Update(user);
    }
}

class UserRepository 
{
    private DbContext _dbContext;
        
    public UserRepository(DbContext dbContext)
    {
        _dbContext = dbContext;
    }    
    
    public UserModel Get(Expression<Func<User, bool>> predicate)
    {
        // Get appropriate user from database
    }
}

class User 
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Debt { get; set; }
}
```

http://programmers.stackexchange.com/questions/218011/how-accurate-is-business-logic-should-be-in-a-service-not-in-a-model/218394#218394

## Code Guidelines
We will follow the MSDN C# Coding Conventions here: https://msdn.microsoft.com/en-us/library/ff926074.aspx

Additions and modifications to the conventions will be added to this chapter.

### Constants
Do not use SCREAMING_CONSTANTS

*Why?:* Recommended convention is consistent with the .NET Framework and is easy to read.
```csharp
public class MyClass
{
    /* avoid */
    public const ITEMS_PER_PAGE = 13;
    
    /* recommended */
    public const ItemsPerPageConstant = 13;
}
```

*Ref - constants: http://10rem.net/articles/net-naming-conventions-and-programming-standards---best-practices*

### Private instance variables
```csharp
private readonly DbContext _dbContext;          // readonly since it is assigned in contructor only (with DI)
private readonly IMessageDispatcher _messageDispatcher;

public void MyClass(DbContext dbContext, IMessageDispatcher messageDispatcher)
{
    _dbContext = dbContext;
    _messageDispatcher = messageDispatcher;
}
```

### When to use variable name "result"
Variable name "result" should imply a variable that will change during the method / class, an aggregate of multiple operations.
```csharp
public string GetFullname(UserModel user) 
{
    string result;
        
    if (!string.IsNullOrWhitespace(user.FirstName)) 
    {
        result += user.FirstName;
    }
    
    if (!string.IsNullOrWhitespace(user.LastName)) 
    {
        result += user.LastName;
    }
    
    return result;
}
```

### Multiple return statements in functions
Ideally we only want one exit point in a function, but there are scenarios that warrant multiple. Keep number of exit points to a minimum, preferably one. 
```csharp
/* avoid */
public string GetFullname(UserModel user) 
{
    if (string.IsNullOrWhitespace(user.LastName)) 
    {
        return user.FirstName;
    }
    
    return string.Format("{0} {1}", user.FirstName, user.LastName);
}
```

### When not to use variable name "result"
Avoid using generic words like "result" as much as possible. 

"return View();" takes a parameter "object model", use this info when naming your variable.

In this example "result" is avaliable as a (still not recommended) variable name for some pre-processing before passing all the data to the view.
```csharp
/* avoid */
public ActionResult BreadCrumb(PageData currentPage)
{
    var contentLoader = ServiceLocator.Current.GetInstance<IContentLoader>();
    var sitePageService = new SitePageService(contentLoader);

    var result = sitePageService.GetNavigationPath(currentPage);
    
    return PartialView("BreadCrumb", result);
}
```

Declare "result"-variable with a useful name, preferably early in the method with a default value.
```csharp
/* recommended */
public ActionResult BreadCrumb(PageData currentPage)
{
    var breadCrumbPages = new List<SitePageBaseViewModel>;
    
    var contentLoader = ServiceLocator.Current.GetInstance<IContentLoader>();
    var sitePageService = new SitePageService(contentLoader);

    breadCrumbPages = sitePageService.GetNavigationPath(currentPage);
    
    return PartialView("BreadCrumb", breadCrumbPages);
}```

### Object Initializer
Avoid calling functions inside an object initializer. 
```csharp
/* avoid */
var model = new IndexViewModel
{
    contents = someProcessor.Transform(myContents)
}
```

This makes debugging much easier
```csharp
/* recommended */
var transformedContents = someProcessor.Transform(myContents);
var model = new IndexViewModel
{
    contents = transformedContents
}
```

Simple variable assignment is ok to use in object initializers.
```csharp
/* recommended */
var someDocument = _context.Get(p => p.Id == id); 
var model = new IndexViewModel
{
    title = someDocument.Title
}
```

## One Solution  !!!TODO!!!
- DLL
- Nuget
- Branch / Copy code

## The Big List of Smells (TBLS)  !!!TODO!!!
- Too many comments

## SOLID Principles
- Single responsibility principle
    - a class should have only a single responsibility (i.e. only one potential change in the software's specification should be able to affect the specification of the class)
- Open/closed principle
    - “software entities … should be open for extension, but closed for modification.”
- Liskov substitution principle
    - “objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program.” See also design by contract.
- Interface segregation principle
    - “many client-specific interfaces are better than one general-purpose interface.”[8]
- Dependency inversion principle
    - one should “Depend upon Abstractions. Do not depend upon concretions.”[8]

## Patterns  !!!TODO!!!

## Snippets

### NHIs collection of handsy snippets !!!TODO!!!

### Creating custom snippets  !!!TODO!!!

### Already Built in Snippets
- ctor: Creates a constructor for the containing class.
- try: Creates a try-catch block.
- prop: Creates an auto-implemented property declaration.
- for: Creates a for loop.

Check out the reference for the full list of snippets included in Visual Studio.

*Ref - Visual C# Code Snippets: https://msdn.microsoft.com/en-us/library/z41h7fat.aspx* 
