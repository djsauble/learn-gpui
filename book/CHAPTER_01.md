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
gpui = { git = "https://github.com/zed-industries/zed.git", features = ["macros"] }
```

The `macros` feature enables helpful macros that simplify the development process.

### System Dependencies

GPUI has some system dependencies that you need to install.

**macOS**:
Ensure you have the Xcode command-line tools installed. If you don't, you can install them by running:
```sh
xcode-select --install
```

### The Simplest Runnable App

Let's start by creating the most minimal GPUI application possible. Replace the contents of `src/main.rs` with the following:

```rust
use gpui::*;

// The `main` function is the entry point of our application.
fn main() {
    // `App::new()` creates a new application instance.
    App::new().run(|cx: &mut AppContext| {
        // `cx.open_window` opens a new window. We provide default options
        // and a closure that builds the view for the window.
        // For now, the view is empty.
        cx.open_window(WindowOptions::default(), |_| {});
    });
}
```

Run the application with `cargo run`. You should see a blank window appear. This is the foundation of every GPUI app.

### Creating a View

In GPUI, `Views` are the core components that hold state and render UI. Let's create one.

Update `src/main.rs`:
```rust
use gpui::*;

// Define a struct for our view. It doesn't need any data yet.
struct HelloWorld;

// Implement the `Render` trait for our view. The `render` method is
// called by the framework whenever the UI needs to be redrawn.
impl Render for HelloWorld {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        // The `div` element is a basic container, similar to `<div>` in HTML.
        // For now, it's empty.
        div()
    }
}

fn main() {
    App::new().run(|cx: &mut AppContext| {
        cx.open_window(WindowOptions::default(), |cx| {
            // `cx.new_view` creates an instance of our `HelloWorld` view
            // and places it in the window.
            cx.new_view(|_cx| HelloWorld)
        });
    });
}
```
Run this now. The window will look the same, but our code is now structured with a `View` that will render our UI.

### Adding Text

Let's make our view render some text.

Modify the `render` method in `src/main.rs`:
```rust
// ... existing code ...
impl Render for HelloWorld {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            // The `child` method adds a child element.
            // The `text` element renders a string of text.
            .child(text("Hello, GPUI!"))
    }
}
// ... existing code ...
```
Run the app again. You'll see "Hello, GPUI!" in the top-left corner of the window.

### Styling and Centering

The text is a bit lonely in the corner. Let's center it and give it a color. GPUI uses a fluent, method-chaining style for styling that is inspired by Tailwind CSS.

Modify the `render` method again:
```rust
// ... existing code ...
impl Render for HelloWorld {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            // Use flexbox to control layout.
            .flex()
            .justify_center() // Center horizontally
            .items_center()   // Center vertically
            .size_full()      // Make the div take up the full window size
            .child(
                text("Hello, GPUI!")
                    // Most styling methods are available on all elements.
                    .text_color(rgb(0xffffff)) // Set text color to white
            )
    }
}
// ... existing code ...
```
Now when you run the app, the text will be perfectly centered and colored white, standing out against the default dark background.

### Adding an Asset

GPUI applications need an `assets` directory in the project root for static files like images, fonts, and icons. Let's add the Zed logo next to our text.

1.  Create an `assets/icons` directory in your project root:
    ```sh
    mkdir -p assets/icons
    ```
2.  Download the Zed logo SVG and save it as `assets/icons/logo.svg`. You can get it from the Zed repository or use a command like `curl`:
    ```sh
    curl -o assets/icons/logo.svg https://raw.githubusercontent.com/zed-industries/zed/main/assets/icons/logo.svg
    ```

Now, update the `render` method to include the SVG:
```rust
// ... existing code ...
impl Render for HelloWorld {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .flex()
            .justify_center()
            .items_center()
            .size_full()
            .gap_4() // Add some space between our children
            .child(
                // The `svg` element renders an SVG image from the assets directory.
                svg()
                    .path("icons/logo.svg")
                    .text_color(rgb(0xffffff)) // SVGs can be colored like text
                    .w_8() // Set width (w-8 = 2rem = 32px)
                    .h_8(), // Set height
            )
            .child(
                text("Hello, GPUI!")
                    .text_color(rgb(0xffffff))
            )
    }
}
// ... existing code ...
```
There's a small problem in our `main` function. The `run` closure has a typo `AppCsontext` instead of `AppContext`. Let's fix that.
```rust
// ... existing code ...
fn main() {
    App::new().run(|cx: &mut AppContext| { // Corrected from AppCsontext
        cx.open_window(WindowOptions::default(), |cx| {
            cx.new_view(|_cx| HelloWorld)
        });
    });
}
```

### Run the Final Application

You're all set! Compile and run your application from the project root:

```sh
cargo run
```

After a few moments of compiling, you should see a window appear with the Zed logo and the text "Hello, GPUI!" centered nicely.

Congratulations! You've successfully built and run your first GPUI application, starting from a blank window and progressively adding components and styling.

## Summary

In this chapter, you learned how to:
- Set up a new Rust project with the GPUI dependency.
- Install the necessary system libraries for your operating system.
- Write a simple application that renders text and an SVG image.
- Progressively build a UI, from a blank window to a styled view.
- Understand the roles of `App`, `Window`, `View`, and `Elements` in a GPUI application.
- Include and use assets like SVG icons.

You now have a working GPUI development environment. You've taken the first and most important step on your journey to mastering GPUI.

## What's Next?

In **Chapter 2: Understanding the Architecture**, we will take a closer look at the core concepts of GPUI, such as the rendering pipeline, contexts, and views, to build a deeper understanding of how the framework operates.
