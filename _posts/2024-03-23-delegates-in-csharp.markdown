---
layout: post
title: "Different Types of Delegates in C#"
date: 2024-03-23 13:22:00 +0200
comments: true
published: true
categories: ["blog", "archives","csharp"]
tags: ["delegates in C#", "delegates in Csharp", "named delegate", "action delegate","func delegate", "multicast delegate", "predicate delegate", "event delegate"]
permalink: /post/delegates-in-csharp
---

Delegates are a powerful feature that allows you to pass methods as arguments to other methods. They are essential for designing extensible and flexible applications. In this post, we'll explore various types of delegates with examples.

## Named Delegate

A named delegate is a type-safe function pointer. It allows you to encapsulate a method with a specific signature. Here's an example:

```csharp
// Named delegate
delegate void MyDelegate(string msg);

// Method that matches the signature of the named delegate
static void MyMethod(string message)
{
    Console.WriteLine("Named Delegate: " + message);
}

static void Main(string[] args)
{
    // Named delegate example
    MyDelegate del = MyMethod;
    del("Hello, World!");
}
```

In this example, `MyDelegate` is a named delegate that points to the `MyMethod` method.

## Action Delegate

The Action delegate is a predefined delegate type in C#. It represents a method that takes one or more input parameters and does not return a value. Here's an example:

```csharp
// Action delegate example
Action<string> myAction = (message) =>
{
    Console.WriteLine("Action Delegate: " + message);
};
myAction("Hello, World!");

```

In this example, `myAction` is an `Action` delegate that takes a `string` parameter and prints it.

## Func Delegate

The `Func` delegate is another predefined delegate type in C#. It represents a method that takes one or more input parameters and returns a value. Here's an example:

```csharp
// Func delegate example
Func<int, int, int> add = (a, b) =>
{
    return a + b;
};
int result = add(1, 2);
Console.WriteLine("Func Delegate: " + result);
```
In this example, add is a `Func` delegate that takes two `int` parameters and returns their sum.

## Multicast Delegate

A multicast delegate is a delegate that holds references to more than one method. When invoked, it calls all the methods in its invocation list. Here's an example:

```csharp
// Multicast delegate example
MyDelegate del1 = MyMethod;
MyDelegate del2 = MyMethod;
MyDelegate del3 = del1 + del2;
del3("From Multicast, Hello, World!");
```
In this example, `del3` is a multicast delegate that calls `MyMethod` twice.

## Predicate Delegate

The Predicate delegate is a predefined delegate type in C#. It represents a method that takes one input parameter and returns a boolean value. Here's an example:

```csharp
// Predicate delegate example
Predicate<string> isUpperCase = IsUpperCase;
bool isUpper = isUpperCase("Hello World");
Console.WriteLine("Predicate Delegate: " + isUpper);

static bool IsUpperCase(string str)
{
    return str.Equals(str.ToUpper());
}
```
In this example, `isUpperCase` is a Predicate delegate that checks if a string is in uppercase.

## Event Delegate

Event delegates are used to define events in C#. They allow you to subscribe methods to an event and invoke those methods when the event is raised. Here's an example:

```csharp
// Event delegate
public delegate void MyEventDelegate();

public class MyClass
{
    public event MyEventDelegate MyEvent;

    public void RaiseEvent()
    {
        MyEvent?.Invoke();
    }
}

static void Main(string[] args)
{
    // Event delegate example
    MyClass obj = new MyClass();
    obj.MyEvent += () => Console.WriteLine("Event Delegate: Event Raised");
    obj.RaiseEvent();
}
```

In this example, `MyEventDelegate` is an event delegate, and `MyEvent` is an event that can be raised by the `MyClass` class.

**Conclusion**

Delegates are a versatile feature in C# that can help you write more flexible and reusable code. Understanding the different types of delegates and how to use them can significantly improve your coding skills.