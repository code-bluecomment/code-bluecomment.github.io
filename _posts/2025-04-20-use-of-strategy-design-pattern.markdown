---
layout: post
title: "Use of Strategy Design Pattern in C#"
date: 2025-05-20 07:16:00 +0200
comments: true
published: true
categories: ["blog", "archives", "csharp"]
tags: ["Strategy", "Strategy design pattern","Sorting Algorithms","Compression Tools","Authentication Mechanisms","payment processing system"]
permalink: /post/use-of-strategy-design-pattern
---

The Strategy Design Pattern is a behavioral design pattern that allows you to define a family of algorithms, encapsulate them in separate classes, and make them interchangeable. This pattern promotes flexibility by enabling the selection of an algorithm at runtime without altering the code of the context class that uses the algorithm.

The Strategy Pattern shines when multiple solutions exist for the same task and the best option must be chosen dynamically.

## Usage Scenarios

- **Sorting Algorithms**: Different sorting approaches (QuickSort, MergeSort, BubbleSort) implemented as separate strategies.
- **Compression Tools**: Switching between ZIP, RAR, or 7z compression formats.
- **Authentication Mechanisms**: Supporting OAuth, JWT, or basic authentication as interchangeable strategies.

## Example real-world scenario

A real-world scenario for this pattern could be a payment processing system, where multiple payment methods (Credit Card, PayPal, Bitcoin) are available.

### Define the Strategy Interface (The common interface for all algorithms):

```csharp
public interface IPaymentStrategy
{
    void ProcessPayment(decimal amount);
}
```
### Implement Concrete Strategies (Different payment methods):

```csharp
public class CreditCardPayment : IPaymentStrategy
{
    public void ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing {amount} via Credit Card");
    }
}

public class PayPalPayment : IPaymentStrategy
{
    public void ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing {amount} via PayPal");
    }
}

public class BitcoinPayment : IPaymentStrategy
{
    public void ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing {amount} via Bitcoin");
    }
}
```

### Create a Context Class (Delegates the work to a chosen strategy):

```csharp
public class PaymentProcessor
{
    private IPaymentStrategy _paymentStrategy;

    public PaymentProcessor(IPaymentStrategy paymentStrategy)
    {
        _paymentStrategy = paymentStrategy;
    }

    public void ExecutePayment(decimal amount)
    {
        _paymentStrategy.ProcessPayment(amount);
    }
}
```

### Use the Strategy Pattern in Main Method (Selecting a payment method dynamically):

```csharp
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Choose a payment method: 1-CreditCard 2-PayPal 3-Bitcoin");
        int choice = int.Parse(Console.ReadLine());

        IPaymentStrategy strategy = choice switch
        {
            1 => new CreditCardPayment(),
            2 => new PayPalPayment(),
            3 => new BitcoinPayment(),
            _ => throw new Exception("Invalid choice")
        };

        PaymentProcessor processor = new PaymentProcessor(strategy);
        processor.ExecutePayment(100.00m);
    }
}
```

## Pros and Cons of Strategy Design Pattern

### Pros
- **Enhances Flexibility** – Different algorithms can be swapped at runtime without modifying the core logic.
- **Encapsulates Behavior** – Helps maintain clean and modular code.
- **Supports Open/Closed Principle** – New strategies can be added without altering existing code.
- **Simplifies Testing** – Individual strategies can be tested independently.

### Cons:
- **Can Increase Complexity** – Requires multiple classes to implement different strategies.
- **Client Awareness Required** – The client must know about different strategy implementations.
- **Overuse Can Lead to Unnecessary Abstraction** – If not properly justified, using this pattern can lead to unnecessary complexity.

Feel free to explore the [GitHub repository](https://github.com/lijotech/CSharpCodeExamples/tree/main/UseOfStrategyDesignPattern) for more details and examples. Happy coding!
