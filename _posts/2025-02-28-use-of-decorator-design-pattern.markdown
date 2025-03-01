---
layout: post
title: "Use of Decorator Design Pattern in C#"
date: 2025-02-28 09:22:00 +0200
comments: true
published: true
categories: ["blog", "archives","csharp","dotnet"]
tags: ["Decorator Design Pattern", "Decorator Design" ]
permalink: /post/use-of-decorator-design-pattern
---

The Decorator Pattern is a structural design pattern that allows behavior to be added to individual objects, dynamically, without affecting the behavior of other objects from the same class. This pattern is particularly useful in scenarios where functionalities such as logging, caching, or circuit breaking need to be added to individual services in a flexible and reusable manner.

In this blog post, we’ll explore how the Decorator Pattern can be used to implement logging and circuit breaking in C#.

## Understanding the Decorator Pattern

In essence, the decorator pattern involves creating a set of decorator classes that are used to wrap concrete components. These decorators add new behaviors to the objects they wrap, making it easy to extend functionality without modifying the original code.

Here’s a brief outline of the pattern:

- **Component**: The interface or abstract class defining the basic functionalities.

- **ConcreteComponent**: The class implementing the Component interface.

- **Decorator**: The abstract class implementing the Component interface and containing a reference to a Component object.

- **ConcreteDecorator**: The class extending the Decorator to add additional functionalities.

## Example Scenario: Implementing Logging and Circuit Breaking

Let's dive into an example and see how the Decorator Pattern is used to add logging and circuit breaking functionalities to services.

### Step 1: Defining the Component Interface
First, we define the `IDataService` and `IExternalDataService` interfaces, which represent the core functionalities of our services.


```csharp 
public interface IDataService
{
    List<int> GetData();
}

public interface IExternalDataService
{
    Task<List<int>> GetExternalDataAsync();
}
```

### Step 2: Implementing the Concrete Components

Next, we create the concrete implementations of the interfaces.

```csharp
public class DataService : IDataService
{
    public List<int> GetData()
    {
        var data = new List<int>();
        for (var i = 0; i < 10; i++)
        {
            data.Add(i);
            Thread.Sleep(500);
        }
        return data;
    }
}

public class ExternalDataService : IExternalDataService
{
    public async Task<List<int>> GetExternalDataAsync()
    {
        // Simulating data retrieval with an artificial delay and exception
        await Task.Delay(500);
        throw new NotImplementedException();
    }
}
```

### Step 3: Creating Decorators
Now, we create decorators for logging and circuit breaking functionalities.

Logging Decorator
The LoggingDataService class adds logging functionality to the IDataService.

```csharp
public class LoggingDataService : IDataService
{
    private readonly IDataService _dataService;
    private readonly ILogger<LoggingDataService> _logger;

    public LoggingDataService(IDataService dataService, ILogger<LoggingDataService> logger)
    {
        _dataService = dataService;
        _logger = logger;
    }

    public List<int> GetData()
    {
        _logger.LogInformation("Starting to get data");
        var stopwatch = Stopwatch.StartNew();
        var data = _dataService.GetData();
        stopwatch.Stop();
        var elapsedTime = stopwatch.ElapsedMilliseconds;
        _logger.LogInformation("Finished getting data in {elapsedTime} milliseconds", elapsedTime);
        return data;
    }
}
```

Circuit Breaker Decorator
The CircuitBreakerDataService class adds circuit-breaking functionality to the IExternalDataService.

```csharp
public class CircuitBreakerDataService : IExternalDataService
{
    private readonly IExternalDataService _dataService;
    private readonly ICircuitBreaker _circuitBreaker;

    public CircuitBreakerDataService(IExternalDataService dataService, ICircuitBreaker circuitBreaker)
    {
        _dataService = dataService;
        _circuitBreaker = circuitBreaker;
    }
    
    public async Task<List<int>> GetExternalDataAsync()
    {          
        try
        {
            if (_circuitBreaker.IsOpen)
            {
                throw new CircuitBreakerException("Circuit breaker is open");
            }
            var forecast = await _dataService.GetExternalDataAsync();
            _circuitBreaker.Reset();
            return forecast;
        }
        catch (Exception)
        {
            _circuitBreaker.Trip();
            throw;
        }
    }
}
```

### Step 4: Defining the Circuit Breaker
We also need to define the ICircuitBreaker interface and its implementation.

```csharp
public interface ICircuitBreaker
{
    bool IsOpen { get; }
    void Trip();
    void Reset();
}

public class CircuitBreaker : ICircuitBreaker
{
    private bool _isOpen = false;
    private DateTime _lastFailedAttempt = DateTime.MinValue;
    private TimeSpan _timeout = TimeSpan.FromSeconds(10);

    public bool IsOpen
    {
        get
        {
            if (_isOpen && DateTime.Now > _lastFailedAttempt + _timeout)
            {
                Reset();
            }
            return _isOpen;
        }
    }

    public void Trip()
    {
        _isOpen = true;
        _lastFailedAttempt = DateTime.Now;
    }

    public void Reset()
    {
        _isOpen = false;
    }
}
```

### Step 5: Configuring the Services
Finally, we configure the services in the Startup.cs or equivalent configuration file. By leveraging dependency injection, we can chain multiple decorators together to create a flexible and extensible system.

```csharp
services.AddSingleton<ICircuitBreaker, CircuitBreaker>();

services.AddScoped(serviceProvider =>
{
    var logger = serviceProvider.GetService<ILogger<LoggingDataService>>();
    var memoryCache = serviceProvider.GetService<IMemoryCache>();

    IDataService concreteService = new DataService();
    IDataService cachingDecorator = new CachingDataService(concreteService, memoryCache);
    IDataService loggingDecorator = new LoggingDataService(cachingDecorator, logger);

    return loggingDecorator;
});

services.AddScoped(serviceProvider =>
{
    var circuitBreaker = serviceProvider.GetService<ICircuitBreaker>();

    IExternalDataService concreteService = new ExternalDataService();
    IExternalDataService circuitBreakerDecorator = new CircuitBreakerDataService(concreteService, circuitBreaker);

    return circuitBreakerDecorator;
});

```

## Use Cases

- Logging: You can use the decorator pattern to add logging to services in your application. For example, you could create a LoggingService that logs each method call and then passes the call onto the decorated service.

- Performance Metrics: Similarly, you can create a MetricsService that records the time it takes to execute a method and then passes the call onto the decorated service. This can be useful for identifying performance bottlenecks in your application.

- Authentication and Authorization: In an ASP.NET Core application, you can use the decorator pattern to add authentication and authorization to your services. For example, you could create an AuthorizedService that checks if the current user has the necessary permissions before passing the call onto the decorated service.

- Circuit Breaker Pattern: In a microservices architecture, you might use the decorator pattern to implement the circuit breaker pattern. This pattern can prevent an application from trying to invoke a service that’s failing. A CircuitBreakerService could track the number of failed requests and open the circuit (i.e., stop forwarding requests) if a certain failure threshold is reached.

- Validation: You can use the decorator pattern to add validation to your services. A ValidationService could validate the parameters of a method call and throw an exception if the parameters are invalid, before passing the call onto the decorated service.

## Drawbacks
 
While the decorator pattern offers flexibility and reusability, it also comes with some drawbacks:

- Complexity: The use of multiple decorators can lead to increased complexity in understanding and maintaining the codebase.

- Performance Overhead: Each additional decorator adds a layer of abstraction, which might impact performance.

- Dependency Management: Managing dependencies between decorators can become challenging, especially in large applications.


Remember, the decorator pattern is all about adding behavior to an object without modifying the object itself. Instead, you create a new class that wraps the original object and adds the new behavior. This allows for a high degree of flexibility and customization in your code. It’s a powerful tool in the toolbox of any ASP.NET Core developer.