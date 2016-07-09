# ASP.NET Core Utility

This solution add some useful features for ASP.NET Core projects. All projects in this solution are designed for cross-platform  "netstandard1.5" target framework, which can be used in both Full CLR and CoreCLR systems.

This solution contains the following aspects:

* Tag Helpers
* Middlewares
* Utilities

---

## Current Project List

This section lists all projects in the repo. The list will always update in time.

### External Cookie Services

*Nuget Package Name: `Sakura.AspNetCore.Authentication.ExternalCookie`*

ASP.NET Core Identity Service (`Microsoft.AspNet.Identity` package) already added support for external cookie services, which is required for 3rd application authentication scheme (e.g. Microsoft of Fackbook account). You may use `AddIdentity` and `UseIdentity` method to enable external cookie services. However, in sometimes you may wish to enable external services indivindually without full ASP.NET Identity Service enabled. This project seperates the external cookie services from ASP.NET Identity Services, and may be used in more complex authentication scenes.

The major feature of this project contains:

* Configure ASP.NET Core Application to support ApplicationCookie and ExternalCookie services (provides extension methods can be used in `ConfigureServices` method of ASP.NET Startup class)
* Provide ExternalSignInManager service to simpify external cookie ticket management.


### ASP.NET Core MVC TagHelper Extension Library

*Nuget Package Name: `Sakura.AspNetCore.Mvc.TagHelpers`*

Add various addtional TagHelper classes to simplify strong type model based ASP.NET Core MVC web application development, including:

* `EnumSelectForTagHelper`: You can now use `asp-enum-for` attribute to generate a HTML select list with options for an enum  type, the type is specified by the given model expression. You can apply `DisplayAttribute` on enum items and specify `asp-text-source` to support custom display texts. The `asp-value-source` can be used to specify the format of option values.

* `EnumSelectTypeTagHelper` You can use `asp-enum-type` to specify the enum type manually if you use an select without an model data. Adding the `asp-enum-value` attribute to specify the selected item; otherwise, no item is selected by default.  `asp-text-source` and `asp-value-source` can also be used as the same as `EnumSelectForTagHelper`.

* `SelectValueTagHelper`: You can now use `asp-value` attribute on `select` element to automatically make the matched option selected when rendering its content.

* `SelectOptionLabelTagHelper`: You can now use `asp-option-label` attribute on `select` element to generate an option label with custom text (the value of the option label is set to empty). The `asp-option-label-position` attribute can be used to controller the position of the option label.

* `ConditionalClassTagHelper`: You can now use `asp-conditional-class-<className>="<ConditionExpression>"` to add conditional actived class name to any HTML elements. When the condition expression is evaluated with value `true`, the specified class will be added to this element; otherwise, this class will be ignored. You can apply multiple conditional classes on one element.

* `FlagsEnumInputTagHelper`: You can now use `asp-flag-enum-for` and `asp-flag-enum-value` attributes on checkbox (`<input type="checkbox" />`) element. The `asp-flag-enum-for` attribute controls the model expression of enum flag value (the enum type must be marked with `FlagsAtttibute`), and the `asp-flag-enum-value` indicates the enum flag item for this checkbox. If the actual model expression value contains the flag value (mainly equivalent as `Enum.HasFlag` method), the checkbox will be checked, otherwise, it will be unchecked.

*NOTE: To correctly analysis the generated model data when page posts them to server, you may also need `FlagsEnumModelBinder` class.*

* `FlagsEnumModelBinder`: A helper model binder class often used together with `FlagsEnumInputTagHelper` tag helper, but also can be used indivindually. This model binder can collect all model values associated with one model (or its property) of enum types with `FlagsAttribute` applid, convert each value seperately, and used bitwise or to generate the merged final value.

### ASP.NET TempData Extension Package

*Nuget Package Name: `Sakura.AspNetCore.Mvc.TempDataExtensions`*

This project provides the `EnhancedSessionStateTempDataProvider` service provider, with can replace the original `SessionBasedTempDataProvider`, in order to enhance the type compatibility for session data. The original TempData provider can only work with primitive types, arrays, and one-level plain-objects, objects with complex properties are not supported. The `EnhancedSessionStateTempDataProvider` can successfully work with most data objects with arbitray level structures.

 Internally, it uses certain serializer techinque to convert complex data into string value, and store it together with its type full name. When application loading its content, it will deserialize the string to recover the object data structure. The default implmementation uses `JsonObjectSerializer`, and you may also replace it with your own implementation if necessary.


### ASP.NET Core MVC Messages Package

*Nuget Package Name: `Sakura.AspNetCore.Mvc.Messages`*

This project add the feature of common operation message response in web applications, as well as tag helpers to simplify message presentations. The detailed features includes:

* `OperationMessage` definitions and different `OperationMessageLevel` enum items.
* `IOperationMessageAccessor` service, which can be inject in both views and controllers to access operation message data.
* `MessageTagHelper` helper class, and you may use `asp-message-list` attribute on `div` element to generate message list UI, with various styles and additional layout options can be specified.
* `IOperationMessageHtmlGenerator` service, wich is used internally for generating message list UI, and default bootstrap style generator are built-in implemented.

*NOTE: To use this package, your `ITempDataProvider` implementation must have the ability to store and load `ICollection<OperationMessage>` instance. Defaultly, the ASP.NET5 `SessionStateTempDataProvider` cannot support data operation of complex objects. You may change into another `ITempDataProvider` implementation, or just use `EnhancedSessionStateTempDataProvider` in the `ASP.NET TempData Extension Package` project.

### ASP.NET Core PagedList Packages

*Nuget Package Name: `Sakura.AspNetCore.PagedList`*
*Nuget Package Name: `Sakura.AspNetCore.PagedList.Async`*
*Nuget Package Name: `Sakura.AspNetCore.Mvc.PagedList`*

The `Sakura.AspNetCore.PagedList` package provides the `IPagedList` core interface to represent as a data page for a large data source. Some extension methods are also provided to generate instance from any `IEnumerable<T>` or `IQueryable<T>` data sources.

The `Sakura.AspNetCore.PagedList.Async` package helpes you to generate `IPagedList` using async extension method (`ToArrayAsync`, etc.) defined in `Microsoft.EntityFrameworkCore` package.

The `Sakura.AspNetCore.Mvc.PagedList` allows you to use `<pager>` tag in your MVC view page to generate a full featured pager structure.

*For detailed usage, please visit the [Demo](PagerDemo.md) page. Notice: this package has been updated to version 2 (the recommended version)． For usage of version 1, please visit the [Version 1 Demo](PagerDemov1.md) page.*

### ASP.NET ActionResult Extensions Package

*Nuget Package Name: `Sakura.AspNetCore.Mvc.ActionResultExceptionExtensions`*

In MVC Projects, you need to return a instance of `IActionResult` to finish the action pipeline, this design made it difficult to add common helper functions to make argument or permission checking and then report a specified status code directly to end user. This library allows you to terminate action executing pipeline directly with a specified result through the standard exception handling system. 

In order to enable this feature, all you need is adding an `EnableActionResultException` attribute on a controller or action, and then you can throw an `ActionResultException`instance to terminate a action executing pipeline directly and provide the final action result. If you need to enable this feature globally, you can use `EnableActionResultExceptionFilter` extension method on `MvcOptions` parameter when you add the MVC middleware.

---

## Issues and Important Notes
* ASP.NET Core Localization module has not been a stable state, thus localization support for all projects (including error messages and UI strings) are not proceeded yet.

## Contribution and Discussion

You are welcome to add issues and advices, if you want to contribute to the project, please fell free to make pull requests.
