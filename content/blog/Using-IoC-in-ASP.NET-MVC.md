---
title: Using IoC in ASP.NET MVC
date: "2019-04-22T23:46:37.121Z"
description: The reasons to use IoC are numerous, but specifically for ASP.NET it will allow you to keep code isolated into functional components, and allow you to migrate those functional components to newer/more appropriate technologies.
---

## Why use IoC

The reasons to use IoC are numerous, but specifically for ASP.NET it will allow you to keep code isolated into functional components, and allow you to migrate those functional components to newer/more appropriate technologies.

Another major use for IoC is in testing.  Where you will use full database repositories for production, using static data is often more than enough for unit tests.



## Which IoC Container

Since you are currently using ASP.Net MVC , the best option currently is Unity (no not the game engine :) ).  Not that .NET Core has it's own IoC tooling, but most, if not all, of the options here will be easily migrated when the time comes.



## Sample Project

To illustrate the concepts in this doc, I've created the worlds simplest project to go through these examples, that project can be found [here](https://github.com/barranger/IoCTutorial).

The sample project will simply display a quote received form a quote service, that implements the `IQuoteService.cs` Interface



## Register Services

Once adding the `Unity.MVC` library via NuGet, you'll find both `UnityConfig.cs` and `UnityMvcActivator.cs` in your `App_Start` folder.  The `UnityConfig.cs` file is where you register the services, like so:

```csharp
public static void RegisterTypes(IUnityContainer container)
{
	// TODO: Register your type's mappings here.
    container.RegisterType<IQuoteService, StaticQuoteService>();
}
```

Where `IQuoteService` is the interface that we are registering, and `StaticQuoteService` is the concrete implementation of the Interface that we will be exposing to the system.



## Using Services

Once the services are setup, we'll want to use them, in the case of Controllers, this is extremely easy as Unity will auto add them to our application if we declare the interfaces as parameter to our constructors like so:

```csharp
private IQuoteService quoteService;

public HomeController(IQuoteService quoteService)
{
	this.quoteService = quoteService;
}
```

we can then use the service anywhere in the controller, like the sample does for the `About` route:

```csharp
public ActionResult About()
{
	ViewBag.Message = this.quoteService.GetRandomQuote();
	return View();
}
```

Sometimes you will need to access the services outside of a controller, which we can also do by using `UnityConfig.Container` like so:

```csharp
 public static string GetRandomQuote()
 {
 	var service = UnityConfig.Container.Resolve(typeof(IQuoteService), null) as IQuoteService;
 	
    return $"From the RandomUtil: {service.GetRandomQuote()}";
 }
```



