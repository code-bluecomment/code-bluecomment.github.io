---
layout: post
title: "Feature Management in C#"
date: 2024-05-20 13:16:00 +0200
comments: true
published: true
categories: ["blog", "archives", "csharp"]
tags: ["Feature management","Microsoft Feature Management","Boolean Feature", "Percentage Feature", "custom feature filter", "Time Window Feature", "Targeting Feature", "Feature Gate"]
permalink: /post/use-of-feature-management-in-csharp
---

Feature management is a powerful technique that allows developers to enable or disable features in an application dynamically. This approach is particularly useful for rolling out new features gradually, conducting A/B testing, or managing feature flags in different environments. In this blog post, we'll explore different ways to use feature management in C#.

## What is Feature Management?
Feature management involves using feature flags (also known as feature toggles) to control the availability of features in an application. This allows developers to deploy new features to production without making them immediately available to all users. Instead, features can be enabled or disabled based on various criteria, such as user roles, environment settings, or specific conditions.

## Setting Up Feature Management
To get started with feature management in C#, you'll need to install the `Microsoft.FeatureManagement` NuGet package. This package provides the necessary tools to define and manage feature flags in your application.

```bash
Install-Package Microsoft.FeatureManagement
```
## Defining Feature Flags

Feature flags are defined in the appsettings.json file. This file contains the configuration settings for your application, including the feature flags.

```json
{
  "FeatureManagement": {
    "FeatureA": true,
    "FeatureB": false
  }
}
```
In this example, FeatureA is enabled, while FeatureB is disabled.

## Configuring Feature Management

In the Startup.cs file, configure the feature management services by adding the following code:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    services.AddFeatureManagement();
}
```

This code registers the feature management services with the dependency injection container.

## Using Feature Flags in Controllers

You can use feature flags in your controllers to conditionally enable or disable features.

```csharp
[ApiController]
[Route("[controller]")]
public class FeatureController : ControllerBase
{
    private readonly IFeatureManager _featureManager;

    public FeatureController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }

    [HttpGet]
    public async Task<IActionResult> Get()
    {
        if (await _featureManager.IsEnabledAsync("FeatureA"))
        {
            return Ok("FeatureA is enabled");
        }
        else
        {
            return Ok("FeatureA is disabled");
        }
    }
}
```

In this example, the `IFeatureManager` service is used to check if FeatureA is enabled. The response is based on the status of the feature flag.

## Advanced Feature Management

Feature filters allow you to enable or disable features based on specific conditions, such as user roles or environment settings.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    services.AddFeatureManagement()
            .AddFeatureFilter<PercentageFilter>();
}
```
In this example, the PercentageFilter is used to enable a feature for a certain percentage of users. You can create custom feature filters by implementing the `IFeatureFilter` interface.

## Let's dive into the details of each feature types

### BooleanFeature

BooleanFeature is a simple feature flag that can be either enabled or disabled. It is defined in the appsettings.json file as follows:

```json
"FeatureManagement": {
  "BooleanFeature": true
}
```
In the controller, you can check if this feature is enabled using the IFeatureManager service:

```csharp
[HttpGet("BooleanFeature")]
public async Task<IActionResult> GetBooleanFeature()
{
    if (await _featureManager.IsEnabledAsync("BooleanFeature"))
    {
        return Ok("Boolean feature is enabled");
    }
    else
    {
        return BadRequest("Boolean feature is disabled");
    }
}
```

### PercentageFeature

PercentageFeature allows you to enable a feature for a certain percentage of users. This is useful for gradual rollouts or A/B testing. It is defined in the appsettings.json file as follows:

```json
"PercentageFeature": {
  "EnabledFor": [
    {
      "Name": "Percentage",
      "Parameters": {
        "Value": 30
      }
    }
  ]
}
```
This configuration enables the feature for 30% of users. In the controller, you can check if this feature is enabled:

```csharp
[HttpGet("PercentageFeature")]
public async Task<IActionResult> GetPercentageFeature()
{
    if (await _featureManager.IsEnabledAsync("PercentageFeature"))
    {
        return Ok("Percentage feature is enabled");
    }
    else
    {
        return BadRequest("Percentage feature is disabled");
    }
}
```

### CustomLanguageFeature

CustomLanguageFeature uses a custom feature filter to enable the feature based on the user's language. The filter checks the `Accept-Language` header in the HTTP request. It is defined in the `appsettings.json` file as follows:

```json
"CustomLanguageFeature": {
  "EnabledFor": [
    {
      "Name": "LanguageFilter",
      "Parameters": {
        "AllowedLanguages": [
          "en-GB",
          "en-US"
        ]
      }
    }
  ]
}
```
The custom filter is implemented as follows:

```csharp
[FilterAlias(nameof(LanguageFilter))]
public class LanguageFilter : IFeatureFilter
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public LanguageFilter(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public Task<bool> EvaluateAsync(FeatureFilterEvaluationContext context)
    {
        var userLanguage = _httpContextAccessor?.HttpContext?.Request.Headers.AcceptLanguage.ToString();
        var settings = context.Parameters.Get<LanguageFilterSettings>();
        return Task.FromResult(settings.AllowedLanguages.Any(a => userLanguage.Contains(a)));
    }
}

public class LanguageFilterSettings
{
    public string[] AllowedLanguages { get; set; }
}
```
In the controller, you can check if this feature is enabled:

```csharp
[HttpGet("CustomLanguageFeature")]
public async Task<IActionResult> GetCustomFeature()
{
    if (await _featureManager.IsEnabledAsync("CustomLanguageFeature"))
    {
        return Ok("Custom Language feature enabled");
    }
    else
    {
        return BadRequest("Custom Language feature is disabled");
    }
}
```

### TimeWindowFeature

TimeWindowFeature enables a feature for a specific time window. This is useful for features that should only be available during certain periods. It is defined in the appsettings.json file as follows:

```json
"TimeWindowFeature": {
  "EnabledFor": [
    {
      "Name": "TimeWindow",
      "Parameters": {
        "Start": "2024-05-01T10:00:00+00:00",
        "End": "2024-05-30T11:00:00+00:00"
      }
    }
  ]
}
```
In the controller, you can check if this feature is enabled:

```csharp
[HttpGet("TimeWindowFeature")]
public async Task<IActionResult> GetTimeWindowFilter()
{
    if (await _featureManager.IsEnabledAsync("TimeWindowFeature"))
    {
        return Ok("Time Window feature enabled");
    }
    else
    {
        return BadRequest("Time Window feature is disabled");
    }
}
```

### TargetingFeature
TargetingFeature enables a feature for specific users or groups. This is useful for targeting features to specific audiences. It is defined in the appsettings.json file as follows:

```json
"TargetingFeature": {
  "EnabledFor": [
    {
      "Name": "Targeting",
      "Parameters": {
        "Audience": {
          "Users": [ "User1", "User2" ],
          "Groups": [
            {
              "Name": "BetaTesters",
              "RolloutPercentage": 50
            }
          ],
          "DefaultRolloutPercentage": 0
        }
      }
    }
  ]
}
```
The CustomTargetingContextAccessor is implemented as follows:

```csharp
 public class CustomTargetingContextAccessor : ITargetingContextAccessor
    {
        private readonly IHttpContextAccessor _httpContextAccessor;

        public CustomTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
        {
            _httpContextAccessor = httpContextAccessor;
        }

        public ValueTask<TargetingContext> GetContextAsync()
        {
            var context = new TargetingContext
            {
                UserId = _httpContextAccessor.HttpContext.User.Identity.Name,
                Groups = GetUserGroups(_httpContextAccessor.HttpContext.User)
            };
            return new ValueTask<TargetingContext>(context);
        }

        private List<string> GetUserGroups(ClaimsPrincipal user)
        {
            // TODO: Implement this method based on your application's logic
            return new List<string> { "BetaTesters" };
        }
    }
```


In the controller, you can check if this feature is enabled:

```csharp
[HttpGet("TargetingFeature")]
public async Task<IActionResult> GetTargetingFeature()
{
    if (await _featureManager.IsEnabledAsync("TargetingFeature"))
    {
        return Ok("Targeting feature is enabled");
    }
    else
    {
        return BadRequest("Targeting feature is disabled");
    }
}
```

### FeatureGate
FeatureGate is an attribute that can be used to conditionally enable or disable entire actions or controllers based on feature flags. This is useful for controlling access to specific endpoints. Here’s how you can use it:

```csharp
[HttpGet("BooleanFeatureUsingFeatureGate")]
[FeatureGate("BooleanFeature")]
public async Task<IActionResult> GetBooleanFeatureUsingFeatureGate()
{
    return Ok("Boolean feature is enabled");
}
```
In this example, the FeatureGate attribute ensures that the action is only executed if the BooleanFeature is enabled.

## Conclusion
Feature management in C# provides a flexible and powerful way to control the availability of features in your application. By using different types of feature flags and filters, you can enable or disable features based on various criteria, such as user roles, percentages, languages, time windows, and targeting. This approach allows for controlled rollouts, A/B testing, and better management of features across different environments.

Feel free to explore the [GitHub repository](https://github.com/lijotech/CSharpCodeExamples/tree/main/UseOfFeatureManagement) for more details and examples. Happy coding!