#NHI Styleguide

## Table of Contents

  1. [Terms](#terms)
  1. [Naming classes and methods](#naming-classes-and-methods)
  1. [Comments](#comments)
  1. [Application Structure](#application-structure)
  1. [Principles](#principles)
  1. [Patterns](#patterns)
  1. [Snippets](#snippets)
  1. [References](#references)

## Terms

>Don't get naming paralysis. Yes, names are very important but they're not important enough to waste huge amounts of time on. If you can't think up a good name in 5 minutes, move on.

### List of suffixes with short description

- manager, util, helper: the unavoidable noun you attach for coordinating classes that contain no state and are generally procedural and static.

- handler
- service
- provider

- element
- node
- item

- model, viewmodel, dto

- attribute
- type
- collection
- converter
- info
- exception
- option
- factory
- context
- designer
- base
- editor


  1. ~~TODO: group up and give the suffixes a one-liner explanation~~

### Helpers
'Helper' and 'Manager' names usually imply that you're wrapping up a procedural system and pretending it's object-oriented. There's nothing wrong with the procedural approach but if you want the benefits of object orientation then the objects should perform their own behaviour for the most part.

Keep the use of Helpers to a minimum. We want modular components, abd Helpers will work against this by creating external dependencies

## Naming classes and methods
- Classes should do one thing and do it well.
- If a class is hard to name or explain then it's probably not following the advice in the previous bullet point.
- A class name should instantly communicate what the class is.
- Good names drive good designs.

If your problem is what to name exposed internal classes, maybe you should consolidate them into a larger class.

If your problem is naming a class that is doing a lot of different stuff, you should consider breaking it into multiple classes.

If that's good advice for a public API then it can't hurt for any other class.
  1. ~~TODO: http://stackoverflow.com/a/48019~~
  1. ~~TODO: http://stackoverflow.com/questions/421965/anyone-else-find-naming-classes-and-methods-one-of-the-most-difficult-part-in-pr?rq=1~~

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

        Api/
            v1/
                CustomerApiController.cs                    


## Principles

## Patterns

## Snippets

## References
- http://stackoverflow.com/questions/1866794/naming-classes-how-to-avoid-calling-everything-a-whatevermanager?lq=1

[Read more words!](more_word.md)