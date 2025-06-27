# Chapter 1: Getting Started

Welcome to the GPUI Book! This first chapter will guide you through setting up your development environment and creating your very first GPUI application. We'll start with the basics, ensuring you have a solid foundation before we move on to more complex topics.

## Learning Objectives

By the end of this chapter, you will be able to:
- Understand what GPUI is and its core philosophy.
- Set up your development environment for GPUI on macOS or Linux.
- Create and run your first "Hello, GPUI!" application.
- Progressively build a simple UI, understanding each component.
- Understand the basic structure of a GPUI app, including views, elements, and assets.

## Prerequisites

- A basic understanding of Rust, including concepts like ownership, structs, and traits.
- [Rust and Cargo installed](https://www.rust-lang.org/tools/install) on your system.

## What is GPUI?

GPUI is a GPU-accelerated UI framework written in Rust by the creators of the [Zed editor](https://zed.dev). It's designed for building blazingly fast, beautiful, and productive desktop applications.

At its core, GPUI aims to combine the performance of immediate-mode rendering with the developer-friendly ergonomics of a retained-mode API. This hybrid approach allows for a highly reactive and efficient rendering pipeline, making it ideal for applications that require a smooth and responsive user interface, like code editors, design tools, and data visualizations.

### Why Use GPUI?

- **Performance**: By leveraging the GPU, GPUI can render complex interfaces at high frame rates, providing a fluid user experience.
- **Productivity**: The framework is designed with developer experience in mind. Its API is inspired by modern web frameworks and offers a declarative, component-based model that is intuitive to work with.
- **Rust-Powered**: Built on Rust, GPUI gives you memory safety, concurrency, and the power of the Rust ecosystem without sacrificing performance.

## Hands-On Exercise: Your First GPUI Application

Let's dive in and build a classic "Hello, World!" style application. We'll call it "Hello, GPUI!". We will build it step-by-step, starting with the absolute minimum and adding features progressively.

### Project Setup

First, create a new binary Rust project using Cargo:

```sh
cargo new hello_gpui
cd hello_gpui
```

### Add the GPUI Dependency

GPUI is not yet published on `crates.io`. To use it, you need to add the Zed repository as a dependency in your `Cargo.toml` file.

Open `Cargo.toml` and add the following under the `[dependencies]` section:

```toml
[dependencies]
gpui = { git = "https://github.com/zed-industries/zed.git" }
```

### System Dependencies

GPUI has some system dependencies that you need to install.

**macOS**:
Ensure you have the Xcode command-line tools installed. If you don't, you can install them by running:
```sh
xcode-select --install
```

### The Simplest Runnable App

Let's start by creating the most minimal GPUI application possible. This will open a blank window containing an empty view.

Replace the contents of `src/main.rs` with the following:

```rust
use gpui::{
    App, AppContext, Application, Context, IntoElement, Render, Window, WindowOptions, div,
};

// Our view doesn't have any data yet
struct HelloWorld {}

impl Render for HelloWorld {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
    }
}

fn main() {
    // The Application singleton is the entry point to a GPUI app.
    Application::new().run(|cx: &mut App| {
        cx.open_window(
            WindowOptions::default(),
            // The view constructor callback creates an instance of our empty HelloWorld view.
            |_, cx| cx.new(|_| HelloWorld {}),
        )
        .unwrap();
        // Activate the app, making the window visible and focused.
        cx.activate(true);
    });
}
```

Run the application with `cargo run`. You should see a window appear with default dimensions. This is the foundation of every GPUI app.

### Creating a View

In GPUI, `Views` are the core components that hold state and render UI. We have already created a minimal one. Now, let's make it hold some data and render it.

Update `src/main.rs`:
```rust
use gpui::{
    App, AppContext, Application, Context, IntoElement, Render, SharedString, Window,
    WindowOptions, div,
};

// Add a `text` field to our view's struct.
struct HelloWorld {
    text: SharedString,
}

// Implement the `Render` trait for our view. The `render` method is
// called by the framework whenever the UI needs to be redrawn.
impl Render for HelloWorld {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        // Render the text within the div.
        div().child(self.text.clone())
    }
}

fn main() {
    Application::new().run(|cx: &mut App| {
        cx.open_window(
            WindowOptions::default(),
            // In the view constructor, we initialize our view with some text.
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

Run this now. You'll see "Hello, world!" in the top-left corner of the window. Our code is now structured with a `View` that renders our UI.

### Styling and Centering

The text is a bit lonely in the corner. Let's center it and give it some style. GPUI uses a fluent, method-chaining API for styling that is inspired by Tailwind CSS.

Modify the `render` method in `src/main.rs`:
```rust
// ... existing code ...
use gpui::{
    App, AppContext, Application, Context, IntoElement, ParentElement, Render,
    SharedString, Styled, Window, WindowOptions, div, rgb,
};

// Add a `text` field to our view's struct.
struct HelloWorld {
    text: SharedString,
}

// Implement the `Render` trait for our view. The `render` method is
// called by the framework whenever the UI needs to be redrawn.
impl Render for HelloWorld {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        div()
            // Use flexbox to control layout.
            .flex()
            // Fill the available space.
            .w_full()
            .h_full()
            // Center children horizontally and vertically.
            .justify_center()
            .items_center()
            // Apply text styling.
            .text_xl()
            .text_color(rgb(0xffffff))
            // Add the text as a child element.
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
Now when you run the app, the text will be perfectly centered, larger, and colored white, standing out against the default dark background.

### Run the Final Application

You're all set! The final code in `src/main.rs` should look like this:

```rust
use gpui::{
    App, AppContext, Application, Context, IntoElement, ParentElement, Render,
    SharedString, Styled, Window, WindowOptions, div, rgb,
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
            .text_xl()
            .text_color(rgb(0xffffff))
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

Compile and run your application from the project root:

```sh
cargo run
```

Congratulations! You've successfully built and run your first GPUI application, starting from a blank window and progressively adding components and styling.

## Summary

In this chapter, you learned how to:
- Set up a new Rust project with the GPUI dependency.
- Install the necessary system libraries for your operating system.
- Create a windowed application using `gpui::Application`.
- Define and render a `View` with state.
- Use the `div` element and styling methods to create a layout.
- Progressively build a UI, from a blank window to a styled view.

You now have a working GPUI development environment. You've taken the first and most important step on your journey to mastering GPUI.

## What's Next?

In **Chapter 2: Understanding the Architecture**, we will take a closer look at the core concepts of GPUI, such as the rendering pipeline, contexts, and views, to build a deeper understanding of how the framework operates.
