---
layout: post
title: "Use of Command Design Pattern in C#"
date: 2025-04-13 07:16:00 +0200
comments: true
published: true
categories: ["blog", "archives", "csharp"]
tags: ["Command", "Command design pattern","Undo/Redo functionality","Queuing","Fire-and-Forget Scenario","Transaction Management"]
permalink: /post/use-of-command-design-pattern
---

The Command Design Pattern is a behavioral pattern that encapsulates a request as an object, thereby allowing for parameterization of clients with different requests, queuing of requests, and logging of requests. It is especially useful in scenarios where the sender of the request should be decoupled from the object that handles the request.

## Usage Scenarios
The Command Design Pattern is ideal for:
- **Undo/Redo functionality**: In applications like text editors or drawing tools.
- **Queuing commands**: For batch processing or scheduling.
- **Parameterizing objects**: With different operations, decoupling the requester and executor.
- **Fire-and-Forget Scenario**: Useful for scenarios, where a request is sent to be processed later without waiting for an immediate response.
- **Macro Commands**: Group a set of commands into one. For example, in a video game, a complex attack sequence can be encapsulated as a single command that triggers multiple actions (jump, swing sword, cast spell).
- **Transaction Management**: Implement rollback or commit functionality. If a set of operations need to be completed together (like a database transaction), each operation can be a command, and the undo method can roll back changes if any operation fails.
- **Remote-Control Systems**: Similar to the smart home example, commands can represent actions for controlling devices like drones, robots, or machinery.
- **Workflow Systems**: Implement a workflow engine where each step in the workflow is a command that executes some logic and has undo capabilities. 

## Real-World Example: Remote Control for a Smart Home

Imagine a smart home system where a remote control can turn on/off lights, adjust the thermostat, and close/open blinds. Each operation represents a command.

### Command Interface: 

`ICommand` defines `Execute` and `Undo` methods.

```csharp
public interface ICommand
{
    void Execute();
    void Undo();
}
```

### Concrete Commands: 

`LightCommand` implement the `ICommand` interface and contain the logic for turning on and off the light.

```csharp
public class LightCommand : ICommand
{
    public readonly Light _light;
    public readonly Mode _mode;

    public LightCommand(Light light, Mode mode)
    {
        _light = light ?? throw new ArgumentNullException(nameof(light));
        _mode = mode;
    }

    public void Execute()
    {
        Console.WriteLine($"Executing action {_mode}");
        if (_mode.Equals(Mode.ON))
        {
            _light.TurnOn();
        }
        else
        {
            _light.TurnOff();
        }
    }

    public void Undo()
    {
        Console.WriteLine($"Undoing action {_mode}");
        if (_mode.Equals(Mode.ON))
        {
            _light.TurnOff();
        }
        else
        {
            _light.TurnOn();
        }
    }
}

public enum Mode
{
    ON,
    OFF
}
```

### Receiver: 

`Light` is the object that performs the actual operations.

```csharp
public class Light
{
    public void TurnOn()
    {
        Console.WriteLine("The light is ON.");
    }

    public void TurnOff()
    {
        Console.WriteLine("The light is OFF.");
    }
}
```

### Invoker: 

`RemoteControl` invokes commands and maintains a history of commands for undo functionality.

```csharp
public class RemoteControl
{
    private readonly Stack<ICommand> _commandHistory = new Stack<ICommand>();

    public void Invoke(ICommand command)
    {
        command.Execute();
        _commandHistory.Push(command);
    }

    public void Undo()
    {
        if (_commandHistory.Count > 0)
        {
            ICommand lastCommand = _commandHistory.Pop();
            lastCommand.Undo();
        }
        else
        {
            Console.WriteLine("No commands to undo.");
        }
    }
}
```

### Client: 

The `Program` class creates commands, receivers, and invokes commands through the remote control.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        Light livingRoomLight = new Light();

        RemoteControl remoteControl = new RemoteControl();

        // Execute commands
        remoteControl.Invoke(new LightCommand(livingRoomLight, Mode.ON));
        remoteControl.Invoke(new LightCommand(livingRoomLight, Mode.OFF));

        // Undo commands
        remoteControl.Undo();
        remoteControl.Undo();
        //show command history is empty
        remoteControl.Undo();
    }
}
```

## Merits
- **Decoupling**: It decouples the sender from the receiver.
- **Flexibility**: New commands can be added easily without changing existing code.
- **Undo/Redo**: Enables easy implementation of undo/redo mechanisms.
- **Macro Commands**: Multiple commands can be grouped into one.

## Demerits
- **Complexity**: The number of classes can grow as each command requires a concrete implementation.
- **Overhead**: May introduce unnecessary abstraction and overhead for simple operations.