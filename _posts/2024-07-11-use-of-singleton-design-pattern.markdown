---
layout: post
title: "Use of Singleton Design Pattern in C#"
date: 2024-07-11 08:16:00 +0200
comments: true
published: true
categories: ["blog", "archives", "csharp"]
tags: ["Singleton", "Singleton design pattern"]
permalink: /post/use-of-singleton-design-pattern
---

Today, I want to talk about a design pattern that always seems to spark debate in the developer community: the **Singleton Pattern**. We'll also dive into how it differs from a seemingly similar concept—the **Static Class**.

Let's break it down together!

---

## 🦄 What is the Singleton Pattern?

Have you ever encountered a situation where you needed exactly *one* instance of a class to coordinate actions across your entire application? Think of things like a configuration manager, a logging service, or a connection pool. 

The **Singleton Design Pattern** is built for this exact scenario. It ensures a class has only one instance and provides a global point of access to it.

Here is why developers use it:
- **Single Instance:** Only one instance is created throughout the application's lifecycle.
- **Global Access:** It provides a central, easy-to-reach access point to that instance.
- **Lazy Initialization:** The instance isn't created until it's actually needed, saving resources on startup.
- **State Management:** Unlike simpler structures, Singletons can maintain and manage complex states across different parts of your application.
- **Flexibility:** Because a Singleton is a real object, it can implement interfaces, inherit from other classes, and be injected using Dependency Injection (DI).

---

## 🏛️ Enter the Static Class

Now, you might be thinking: *"Wait a minute. Can't I just use a static class to get a single, global point of access?"*

Yes, you can! A **Static Class** in C# is a class containing only static members. It cannot be instantiated at all. You just call its methods or properties directly using the class name (like `Math.PI` or `Console.WriteLine()`).

### The Differences

While they might seem to do the same job at first glance, they are fundamentally different tools in our developer toolkit:

| Feature | Singleton Pattern | Static Class |
| :--- | :--- | :--- |
| **Instantiation** | Yes (but only once) | No (cannot use `new`) |
| **Interfaces/Inheritance**| Yes. Can implement interfaces and inherit. | No. Cannot inherit or implement interfaces. |
| **Dependency Injection** | Perfect fit. Easy to inject and mock for testing. | Hard to inject or mock. Tightly couples your code. |
| **State** | Great for managing complex data/state. | Best for stateless utilities. |
| **Memory** | Resides on the Heap. | Loaded into a special memory area (High-Frequency Heap) and stays there. |

### When to Use Which?

> **Rule of Thumb:**
> - Use a **Static Class** when you have a collection of utility or helper functions that do not rely on an internal state (e.g., math calculations, string extensions).
> - Use a **Singleton** when your object needs to maintain a state, implement an interface, or participate in Dependency Injection.

---

## 🛠️ Let's Look at Our Example Project

In this [repository](https://github.com/lijotech/CSharpCodeExamples/tree/main/UseOfSingletonPattern), I built a small ASP.NET Core Web API to demonstrate the differences in action!

### What's Inside?

1. **The Singleton Service (`SingletonService.cs`)**
   I created a service that implements an interface (`ISingletonService`). Every time it's created (which is only once!), it generates a unique ID (`Guid`). It also maintains a simple counter.

   ```csharp
   public interface ISingletonService
    {
        Guid GetServiceId();
        int GetCounter();
        void IncrementCounter();
    }

    public class SingletonService : ISingletonService
    {
        private readonly Guid _serviceId;
        private int _counter;

        public SingletonService()
        {
            _serviceId = Guid.NewGuid();
            _counter = 0;
        }

        public Guid GetServiceId()
        {
            return _serviceId;
        }

        public int GetCounter()
        {
            return _counter;
        }

        public void IncrementCounter()
        {
            _counter++;
        }
    }
   ```

2. **The Static Class (`StaticService.cs`)**
   I created a static class that also generates a unique ID when it's first accessed and holds its own counter.

   ```csharp
   public static class StaticService
    {
        // Static state is initialized once when the class is first accessed.
        public static readonly Guid ServiceId = Guid.NewGuid();
        private static int _counter = 0;

        public static int GetCounter()
        {
            return _counter;
        }

        public static void IncrementCounter()
        {
            _counter++;
        }
    }
   ```

3. **Dependency Injection (`Startup.cs`)**
   The Singleton is officially registered with the ASP.NET Core DI container:  
   `services.AddSingleton<ISingletonService, SingletonService>();`

4. **The Controller (`DemoController.cs`)**
   When you hit the `/api/demo/getstate` endpoint, the controller asks both the injected Singleton and the Static class to increment their counters and return their current states.

### Try it Out!

1. Clone the repository.
2. Run the application (`dotnet run`).
3. Navigate to `/api/demo/getstate`.

Refresh the page a few times! You will notice that both the Singleton and the Static class maintain their exact same `ServiceId` and successfully increment their counters. 

The magic, however, is that the **Singleton was instantiated as an object and injected into the controller**, proving its superiority for modern, testable, dependency-injected applications!

Feel free to explore the [GitHub repository](https://github.com/lijotech/CSharpCodeExamples/tree/main/UseOfSingletonPattern) for more details and examples. Happy coding!