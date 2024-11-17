---
layout: post
title: "Use of IValidatableObject in C#"
date: 2020-04-28 16:10:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["IValidatableObject", "validation", "ValidationResult", "custom validation" ]
permalink: /post/use-of-ivalidatableobject-in-csharp
---

In this blog post, we'll explore the use of `IValidatableObject` in C# for performing various types of validation within a request object. We'll cover simple validations like email and telephone, validating a list inside a request object, using a database service for validation, and customizing the output response in the `ConfigureServices` method.

## Simple Validation with `IValidatableObject`

The `IValidatableObject` interface provides a way to implement custom validation logic for a class. In our example, we have a `Student` class that implements `IValidatableObject` to validate the email and telephone properties.

```csharp
public class Student : IValidatableObject
{
    public string Email { get; set; }
    public string Telephone { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        var results = new List<ValidationResult>();

        // Validate email
        try
        {
            var addr = new System.Net.Mail.MailAddress(Email);
            if (addr.Address != Email.Trim())
            {
                results.Add(new ValidationResult("Invalid email format.", new[] { nameof(Email) }));
            }
        }
        catch
        {
            results.Add(new ValidationResult("Invalid email format.", new[] { nameof(Email) }));
        }

        // Custom validation for telephone prefix
        if (!Telephone.StartsWith("+1"))
        {
            results.Add(new ValidationResult("Telephone number must start with '+1'.", new[] { nameof(Telephone) }));
        }

        return results;
    }
}
```

In this example, we validate the email format using `System.Net.Mail.MailAddress` and ensure that the telephone number starts with the prefix `+1`.


## Validating a List Inside a Request Object

We can also use `IValidatableObject` to validate a list of objects within a request object. In our example, the `Student` class contains a list of `Document` objects, and we validate each document's `CreatedDate` and `ExpiryDate`.

```csharp
public class Student : IValidatableObject
{
    public List<Document> DocumentList { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        var results = new List<ValidationResult>();

        // Validate DocumentList
        foreach (var document in DocumentList)
        {
            if (document.ExpiryDate <= document.CreatedDate)
            {
                results.Add(new ValidationResult($"The document at index {DocumentList.IndexOf(document)} with name '{document.DocumentName}' has an expiry date that is not greater than the created date.", new[] { "DocumentList" }));
            }

            if (document.CreatedDate > DateTime.Now)
            {
                results.Add(new ValidationResult($"The document at index {DocumentList.IndexOf(document)} with name '{document.DocumentName}' has a created date that is in the future.", new[] { "DocumentList" }));
            }
        }

        return results;
    }
}
```

In this example, we ensure that the `ExpiryDate` is greater than the `CreatedDate` and that the `CreatedDate` is not in the future.

## Validating Using a Database Service or External Service with Dependency

We can also use `IValidatableObject` to perform validation using a database service or an external service. In our example, we use a repository service to check if the email is already present in the database.

```csharp
public class Student : IValidatableObject
{
    public string Email { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        var results = new List<ValidationResult>();

        // Get IRepositoryService from the ValidationContext
        var repositoryService = (IRepositoryService)validationContext.GetService(typeof(IRepositoryService));

        // Check if email is already present in the database
        if (repositoryService.IsEmailPresent(Email))
        {
            results.Add(new ValidationResult("Email is already present in the database.", new[] { nameof(Email) }));
        }

        return results;
    }
}
```

In this example, we retrieve the `IRepositoryService` from the `ValidationContext` and use it to check if the email is already present in the database.

## Customizing the Output Response in `ConfigureServices`

We can customize the output response for validation errors in the `ConfigureServices` method. This allows us to provide a consistent and user-friendly error response.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    services.AddEndpointsApiExplorer();
    services.Configure<ApiBehaviorOptions>(options =>
    {
        options.InvalidModelStateResponseFactory = actionContext =>
        {
            var errors = actionContext.ModelState
                .Where(e => e.Value?.Errors.Count > 0)
                .Select(e => new
                {
                    Name = e.Key,
                    Errors = e.Value?.Errors.Select(er => er.ErrorMessage).ToArray()
                }).ToArray();

            var response = new
            {
                Message = "Validation errors occurred",
                Success = false,
                Errors = errors
            };
            return new BadRequestObjectResult(response);
        };
    });
    services.AddSwaggerGen();
    services.AddScoped<IRepositoryService, RepositoryService>();
}
```

By customizing the output response, we can provide a clear and structured error message to the client, making it easier to understand and fix validation issues.

## Advantages of Customizing the Response

Customizing the validation error response has several advantages:
- **Consistency**: Ensures that all validation errors are returned in a consistent format.
- **User-Friendly**: Provides clear and structured error messages that are easy to understand.
- **Debugging**: Makes it easier to identify and fix validation issues during development.

## Source Code
Check out the example implementation from [github](https://github.com/lijotech/CSharpCodeExamples/tree/main/UseOfIValidatableObjectInCSharp)

In conclusion, `IValidatableObject` is a powerful interface that allows us to implement custom validation logic for our request objects. By leveraging this interface, we can perform simple validations, validate lists, use external services for validation, and customize the output response to provide a better user experience.