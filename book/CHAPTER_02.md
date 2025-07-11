# Chapter 2: Understanding the Architecture

In [Chapter 1](./CHAPTER_01.md), you built your first GPUI application. Now, it's time to look under the hood. This chapter explores the fundamental architecture of GPUI, giving you a mental model of how the framework operates. Understanding these core concepts is key to building more complex and performant applications.

We'll dissect the roles of the application, its various contexts, and the views that power your UI. We'll also trace the path from your `render` method to the pixels on the screen, and clarify how GPUI manages layout and user interaction.

## Learning Objectives

By the end of this chapter, you will be able to:
- Explain the roles of `App`, `AppContext`, `WindowContext`, and `ViewContext`.
- Describe the GPUI rendering pipeline at a high level.
- Understand the difference between immediate and retained mode UI paradigms.
- Explain how GPUI builds and lays out an element tree.
- Create a GPUI application that manages multiple windows.

## Prerequisites

- Completion of [Chapter 1: Getting Started](./CHAPTER_01.md).
- A solid understanding of the code in the "Hello, world!" project.

## GPUI's Core Concepts

To write effective GPUI applications, it's helpful to understand its architecture. Let's break down the key components.

### App, Contexts, and Views

A GPUI application is structured as a hierarchy of objects that manage state and behavior. At the top is the `Application`, which provides different "contexts" for interacting with the system.

-   **`Application`**: This is the singleton that represents your entire application. It's the entry point and orchestrator, responsible for managing windows, running the main event loop, and providing access to the `AppContext`. You create and run it in your `main` function.

-   **`AppContext`**: This context provides access to application-level functionality. You use it to open windows, manage global state (Models), and perform other tasks that affect the entire application.

-   **`WindowContext`**: Each window in your application has a `WindowContext`. It's your tool for interacting with that specific window, such as accessing its views, handling window-specific events, or drawing directly to it.

-   **`ViewContext`**: Every view is provided with a `ViewContext`. This is the most common context you'll work with. It allows you to manage the view's local state, re-render it, handle events, and interact with its children.

-   **`View`**: A `View` is a struct that implements the `Render` trait. It's the basic building block of your UI, responsible for holding state and defining how it should be drawn on the screen. Our `HelloWorld` struct from Chapter 1 was a `View`.

This hierarchy ensures a clear separation of concerns. A `View` doesn't need to know about the global application state, and the `Application` doesn't need to know about the internal state of a single `View`.

### The Rendering Pipeline

When you call `cx.notify()` in a `ViewContext` or an event occurs, GPUI triggers a re-render. But what happens next?

1.  **Render Method**: GPUI calls the `render` method on your `View`.
2.  **Element Tree**: Your `render` method returns a tree of `Element`s (like `div()`, `text()`, etc.). This tree describes the structure and appearance of your UI for this frame. It's lightweight and rebuilt on every frame, which is characteristic of an immediate-mode style.
3.  **Layout Calculation**: GPUI uses a layout engine called [Taffy](https://github.com/DioxusLabs/taffy) to compute the size and position of each element. It uses a flexbox-based algorithm, similar to modern web development.
4.  **Painting**: Once the layout is determined, GPUI "paints" the elements into a `Scene`. This is a display list of drawing commands.
5.  **GPU Rendering**: Finally, the `Scene` is sent to the GPU, which efficiently renders the pixels to the screen.

This pipeline is highly optimized. GPUI is smart about only re-rendering what has changed, making it incredibly fast.

### Immediate vs. Retained Mode

UI frameworks are often categorized as "immediate mode" or "retained mode."

-   **Retained Mode**: The framework maintains a persistent tree of UI objects (e.g., a DOM). You create the UI once and then mutate it over time with commands like `button.set_text("New Text")`. Traditional desktop frameworks often use this model.
-   **Immediate Mode**: The UI is completely rebuilt from scratch in code on every frame. There is no persistent UI object to modify. You describe what the UI should look like *right now*.

GPUI provides the best of both worlds. You write your `render` code as if it's an immediate-mode UI, which is simpler to reason about. However, behind the scenes, GPUI retains state and performs optimizations, giving you the performance benefits of a retained-mode architecture.

### Element Tree and Layout

As mentioned, your `render` method builds an element tree. This is a nested structure of components.

```rust
div() // Root element
    .flex()
    .child(
        div().child("Header") // First child
    )
    .child(
        div().child("Body") // Second child
    )
```

GPUI's layout system is based on flexbox. You use styling methods like `.flex()`, `.flex_row()`, `.justify_center()`, and `.items_center()` to control the position and size of elements. This declarative approach makes it easy to create responsive and complex layouts.

## Hands-On Exercise: Multiple Windows

To see the `AppContext` in action, let's create an application with two windows. We'll modify our "Hello, World!" project to open a second, distinct window.

### Project Setup

Create a new cargo project.

```sh
cargo new chapter_02
cd chapter_02
```

Install GPUI as a dependency.

```
cargo add gpui --git https://github.com/zed-industries/zed.git
```

### Open Two Windows

We will modify the `main` function to call `cx.open_window` twice. Each window will be created with its own instance of the `HelloWorld` view, but we'll pass different text to each one.

Let's start with the final code from Chapter 1. This will give us a single, styled window.

```rust
use gpui::{
    div, rgb, App, AppContext, Application, Context, IntoElement, ParentElement, Render,
    SharedString, Styled, Window, WindowOptions,
};

struct HelloWorld {
    text: SharedString,
}

impl Render for HelloWorld {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .flex()
            .w_full()
            .h_full()
            .justify_center()
            .items_center()
            .bg(rgb(0x2a2a2a))
            .text_3xl()
            .text_color(rgb(0xffff00))
            .child(self.text.clone())
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(
            WindowOptions::default(),
            |_, cx| {
                cx.new(|_| HelloWorld {
                    text: "Hello, world!".into(),
                })
            },
        )
        .unwrap();
        cx.activate(true);
    });
}
```

Next, we'll customize the window's appearance. We'll add a helper function to create a `WindowOptions` struct, allowing us to set the window's title and position. This step prepares our code for managing multiple windows with distinct properties.

```rust
use gpui::{
    bounds, div, point, px, rgb, size, App, AppContext, Application, Context, IntoElement,
    ParentElement, Pixels, Point, Render, SharedString, Size, Styled, TitlebarOptions, Window,
    WindowBounds, WindowOptions,
};

struct HelloWorld {
    text: SharedString,
}

impl Render for HelloWorld {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .flex()
            .w_full()
            .h_full()
            .justify_center()
            .items_center()
            .bg(rgb(0x2a2a2a))
            .text_3xl()
            .text_color(rgb(0xffff00))
            .child(self.text.clone())
    }
}

fn get_window_options(origin: Point<Pixels>, size: Size<Pixels>, title: String) -> WindowOptions {
    WindowOptions {
        titlebar: Some(TitlebarOptions {
            title: Some(title.into()),
            ..Default::default()
        }),
        window_bounds: Some(WindowBounds::Windowed(bounds(origin, size))),
        ..Default::default()
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        let window_size = size(px(400.0), px(400.0));

        let first_window_options = get_window_options(
            point(px(0.0), px(0.0)),
            window_size,
            String::from("First Window"),
        );

        cx.open_window(first_window_options, |_, cx| {
            cx.new(|_| HelloWorld {
                text: "Hello, from window 1!".into(),
            })
        })
        .unwrap();

        cx.activate(true);
    });
}
```

Finally, let's add a second window. We'll call our `get_window_options` helper again with a different position and title, and then use `cx.open_window` a second time. This demonstrates how to manage multiple windows in a single GPUI application.

```rust
use gpui::{
    bounds, div, point, px, rgb, size, App, AppContext, Application, Context, IntoElement,
    ParentElement, Pixels, Point, Render, SharedString, Size, Styled, TitlebarOptions, Window,
    WindowBounds, WindowOptions,
};

struct HelloWorld {
    text: SharedString,
}

impl Render for HelloWorld {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .flex()
            .w_full()
            .h_full()
            .justify_center()
            .items_center()
            .bg(rgb(0x2a2a2a))
            .text_3xl()
            .text_color(rgb(0xffff00))
            .child(self.text.clone())
    }
}

fn get_window_options(origin: Point<Pixels>, size: Size<Pixels>, title: String) -> WindowOptions {
    WindowOptions {
        titlebar: Some(TitlebarOptions {
            title: Some(title.into()),
            ..Default::default()
        }),
        window_bounds: Some(WindowBounds::Windowed(bounds(origin, size))),
        ..Default::default()
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        let window_size = size(px(400.0), px(400.0));

        // --- Window 1 ---
        let first_window_options = get_window_options(
            point(px(0.0), px(0.0)),
            window_size,
            String::from("First Window"),
        );

        cx.open_window(first_window_options, |_, cx| {
            cx.new(|_| HelloWorld {
                text: "Hello, from window 1!".into(),
            })
        })
        .unwrap();

        // --- Window 2 ---
        let second_window_options = get_window_options(
            point(px(410.0), px(0.0)),
            window_size,
            String::from("Second Window"),
        );

        cx.open_window(second_window_options, |_, cx| {
            cx.new(|_| HelloWorld {
                text: "Greetings, from window 2!".into(),
            })
        })
        .unwrap();

        cx.activate(true);
    });
}
```

Run the application with `cargo run`. You should now see two windows appear, each with its own text. This demonstrates how the `AppContext` can be used to manage multiple windows, each with its own independent view hierarchy.

## Summary

In this chapter, you dove into the core architecture of GPUI. You learned about:
- The roles of `Application`, `AppContext`, `WindowContext`, and `ViewContext`.
- The high-level steps in the GPUI rendering pipeline, from `render` to the GPU.
- How GPUI blends the simplicity of immediate-mode APIs with the performance of a retained-mode backend.
- The flexbox-based layout system for positioning elements.
- How to use `AppContext` to create and manage multiple windows.

With this foundational knowledge, you are now better equipped to understand how to structure your own GPUI applications and solve more complex UI problems.

## What's Next?

In **Chapter 3: Basic Elements and Styling**, we will take a deeper dive into the most common UI elements like `div`, `text`, `img`, and `svg`. You'll learn how to use GPUI's powerful styling system to create beautiful and responsive user interfaces.
