---
layout: post
title: "Understanding the Builder Design Pattern in C#"
date: 2024-10-06 08:16:00 +0200
comments: true
published: true
categories: ["blog", "archives", "csharp"]
tags: ["Builder", "Builder design pattern","Document Generation","Database Queries","Game Object Creation","Test Data Builders","HTTP Request Construction"]
permalink: /post/use-of-builder-design-pattern
---

The Builder Design Pattern is a creational pattern that helps in constructing complex objects step by step. It separates the construction of a complex object from its representation, allowing the same construction process to create different representations.

## When to Use the Builder Design Pattern

- **Complex Object Creation**: When the creation process involves multiple steps or configurations.
- **Immutability**: When you want to create immutable objects with many optional parameters.
- **Readability**: When you want to improve the readability of the code by avoiding complex constructors.

## Why Use Builder Design Pattern?

- **Improves Readability**: Code is more intuitive with fluent calls.
- **Ensures Immutability**: Helps in creating immutable objects when needed.
- **Enhances Maintainability**: Easy to add new steps without modifying existing logic.
- **Avoids Constructor Overloading**: Prevents confusion in object creation when multiple parameters exist.

## Example Scenario
Lets discuss a real world scenario of Pizza making.

### Step 1: Defining the Product

Every builder pattern starts with a product class. In this case, it’s `Pizza.cs`.

```csharp
public class Pizza
{
    public string Size { get; set; }
    public string CrustType { get; set; }
    public List<string> Toppings { get; set; }
    public bool HasExtraCheese { get; set; }
    public void Display()
    {
        Console.WriteLine($"Size: {Size}");
        Console.WriteLine($"Crust Type: {CrustType}");
        Console.WriteLine("Toppings:");
        foreach (var topping in Toppings)
        {
            Console.WriteLine($"- {topping}");
        }
        Console.WriteLine($"Extra Cheese: {(HasExtraCheese ? "Yes" : "No")}");
    }
}
```

### Step 2: Creating the Builder Interface

A builder interface defines the methods for stepwise construction. We will define interface as `IPizzaConcreteBuilder`. We will also discuss a fluent builder shortly.

```csharp
public interface IPizzaConcreteBuilder
{
    void SetSize(string size);
    void SetCrustType(string crustType);
    void AddToppings(List<string> toppings);
    void SetExtraCheese(bool hasExtraCheese);
    Pizza GetPizza();
}
```

### Step 3: Implementing the Concrete Builder
A concrete builder implements the interface and provides specific implementations.

```csharp
public class PizzaConcreteBuilder : IPizzaConcreteBuilder
{
    private Pizza _pizza;
    public PizzaConcreteBuilder()
    {
        _pizza = new Pizza();
    }
    public void SetSize(string size) => _pizza.Size = size;
    public void SetCrustType(string crustType) => _pizza.CrustType = crustType;
    public void AddToppings(List<string> toppings) => _pizza.Toppings = toppings;
    public void SetExtraCheese(bool hasExtraCheese) => _pizza.HasExtraCheese = hasExtraCheese;
    public Pizza GetPizza() => _pizza;
}
```

### Step 4: Using the Director for Construction
A director guides the construction process.

```csharp
public class PizzaDirector
{
    private readonly IPizzaConcreteBuilder _pizzaBuilder;
    public PizzaDirector(IPizzaConcreteBuilder pizzaBuilder)
    {
        _pizzaBuilder = pizzaBuilder;
    }
    public void MakePizza()
    {
        _pizzaBuilder.SetSize("Large");
        _pizzaBuilder.SetCrustType("Thin Crust");
        _pizzaBuilder.AddToppings(new List<string> { "Pepperoni", "Mushrooms", "Onions" });
        _pizzaBuilder.SetExtraCheese(true);
    }
}
```
### Step 5:Using the Concrete Builder

```csharp
static void Main(string[] args)
{
    Console.Write("===Concrete Builder===");
    Console.WriteLine();

    IPizzaConcreteBuilder pizzaBuilder = new PizzaConcreteBuilder();
    PizzaDirector director = new PizzaDirector(pizzaBuilder);
    director.MakePizza();
    Pizza concretePizza = pizzaBuilder.GetPizza();
    concretePizza.Display();
}
```

## Extending the Builder Pattern with Fluent Interface
To make the Builder Pattern more intuitive, we can use a fluent interface. For that we can create a `IPizzaFluentBuilder`interface and `PizzaFluentBuilder` class.

```csharp
public interface IPizzaFluentBuilder
{
    IPizzaFluentBuilder SetSize(string size);
    IPizzaFluentBuilder SetCrustType(string crustType);
    IPizzaFluentBuilder AddTopping(string topping);
    IPizzaFluentBuilder SetExtraCheese(bool hasExtraCheese);
    Pizza Build();
}

public class PizzaFluentBuilder : IPizzaFluentBuilder
{
    private Pizza _pizza;
    public PizzaFluentBuilder()
    {
        _pizza = new Pizza();
        _pizza.Toppings = new();
    }
    public IPizzaFluentBuilder SetSize(string size)
    {
        _pizza.Size = size;
        return this;
    }
    public IPizzaFluentBuilder SetCrustType(string crustType)
    {
        _pizza.CrustType = crustType;
        return this;
    }
    public IPizzaFluentBuilder AddTopping(string topping)
    {
        _pizza.Toppings.Add(topping);
        return this;
    }
    public IPizzaFluentBuilder SetExtraCheese(bool hasExtraCheese)
    {
        _pizza.HasExtraCheese = hasExtraCheese;
        return this;
    }
    public Pizza Build()
    {
        return _pizza;
    }

    public PizzaFluentBuilder FromSpecification(PizzaSpecification spec)
    {
        _pizza.Size = spec.Size;
        _pizza.CrustType = spec.CrustType;
        _pizza.Toppings = new List<string>(spec.Toppings);
        _pizza.HasExtraCheese = spec.HasExtraCheese;
        return this;
    }
}
```

We can also create another class for the Specification, the purpose of that will explain shortly.

```csharp
 public class PizzaSpecification
 {
     public string Size { get; set; }
     public string CrustType { get; set; }
     public List<string> Toppings { get; set; } = new ();
     public bool HasExtraCheese { get; set; }
 }
```
### Using the Fluent Builder

```csharp
static void Main(string[] args)
{
    Console.Write("===Fluent Builder===");
    Console.WriteLine();
    Pizza fluentPizza = new PizzaFluentBuilder()
        .SetSize("Large")
        .SetCrustType("Thin Crust")
        .AddTopping("Pepperoni")
        .AddTopping("Hammoos")
        .AddTopping("Onions")
        .SetExtraCheese(false)
        .Build();
    fluentPizza.Display();
}
```
As you can see the the Fluent Builder makes object creation more readable and intuitive. At the same time the Concrete Builder approach provides structured flexibility.

Finally we could improve this scenario using a parameter object in Fluent Builder. We use the `PizzaSpecification` object for this.

```csharp
static void Main(string[] args)
{
    Console.Write("===Fluent Builder with parameter Object===");
    Console.WriteLine();
    var pizzaSpec = new PizzaSpecification
    {
        Size = "Medium",
        CrustType = "Thick Crust",
        Toppings = new List<string> { "Salt", "Mushrooms", "Onions" },
        HasExtraCheese = true
    };
    Pizza fluentParameterPizza = new PizzaFluentBuilder()
    .FromSpecification(pizzaSpec)
    .Build();
    fluentParameterPizza.Display();
}
```
In summary we see two ways (Concrete, Fluent) of implementint Builder pattern.

## Real-world applications of this pattern

- Document Generation (Reports & PDFs)

  Applications that generate documents step by step—like invoices, reports, or PDFs—often leverage the Builder pattern to add headers, footers, formatting, and embedded media.
  Example: PDF generation usinf `iText`, Report Builders: JasperReports, Crystal Reports
- Database Queries (SQL Query Builders)

  Many database access layers use a Builder approach to construct complex SQL queries dynamically.
  Example: Entity Framework (C#), Hibernate Criteria API (Java)
- Complex Game Object Creation

  In gaming applications, characters, weapons, or levels often require multiple parameters to be set sequentially. Builders help create these objects in a modular way.
  Example: Unity (C#), Minecraft Modding API
- HTTP Request Construction (REST Clients)

  When interacting with APIs, constructing requests step by step ensures flexibility. 
  Example: OkHttp (Java), RestSharp (C#)
- Test Data Builders (Unit Testing)

  In test frameworks, using a Test Data Builder helps create objects with predefined states for better testing. 
  Example: (JUnit (Java), Mockito & FluentAssertions (C#)) 

## Drawbacks 

- Increased Complexity: Adds more classes to the codebase.
- Overhead: May introduce unnecessary overhead if the object construction is simple.

## Conclusion

The Builder Design Pattern is a powerful tool for constructing complex objects in a readable, maintainable, and scalable way. It provides a structured approach to object creation, ensuring flexibility and clarity while avoiding the pitfalls of telescoping constructors.

If your project involves configuring objects with multiple parameters, consider leveraging the Builder Pattern—it might just be the missing piece for making your code cleaner and more efficient! 

Feel free to explore the [GitHub repository](https://github.com/lijotech/CSharpCodeExamples/tree/main/UseOfBuilderDesignPattern) for more details and examples. Happy coding!