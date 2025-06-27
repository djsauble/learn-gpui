# Chapter 1: Getting Started

Welcome to the GPUI Book! This first chapter will guide you through setting up your development environment and creating your very first GPUI application. We'll start with the basics, ensuring you have a solid foundation before we move on to more complex topics.

## Learning Objectives

By the end of this chapter, you will be able to:
- Understand what GPUI is and its core philosophy.
- Set up your development environment for GPUI on macOS or Linux.
- Create and run your first "Hello, GPUI!" application.
- Understand the basic structure of a GPUI app.

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

Let's dive in and build a classic "Hello, World!" style application. We'll call it "Hello, GPUI!".

### Step 1: Project Setup

First, create a new binary Rust project using Cargo:

```sh
cargo new hello_gpui
cd hello_gpui
```

### Step 2: Add the GPUI Dependency

GPUI is not yet published on `crates.io`. To use it, you need to add the Zed repository as a dependency in your `Cargo.toml` file.

Open `Cargo.toml` and add the following under the `[dependencies]` section:

```toml
[dependencies]
gpui = { git = "https://github.com/zed-industries/zed.git", features = ["macros"] }
```

Your `Cargo.toml` should look something like this:

```toml
[package]
name = "hello_gpui"
version = "0.1.0"
edition = "2021"

[dependencies]
gpui = { git = "https://github.com/zed-industries/zed.git", features = ["macros"] }
```

The `macros` feature enables helpful macros that simplify the development process, which we will use.

### Step 3: System Dependencies

GPUI has some system dependencies that you need to install.

**macOS**:
Ensure you have the Xcode command-line tools installed. If you don't, you can install them by running:
```sh
xcode-select --install
```

### Step 4: Write the Code

Now, replace the contents of `src/main.rs` with the following code:

```rust
// 1. Import the necessary items from the gpui crate.
use gpui::*;

// 2. Define a struct for our view. Views are the fundamental building
//    blocks of a GPUI application. They hold state and are responsible
//    for rendering a piece of the UI.
struct HelloWorld;

// 3. Implement the `Render` trait for our view. The `render` method is
//    called by the framework whenever the UI needs to be redrawn.
impl Render for HelloWorld {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        // The `div` element is a basic container, similar to `<div>` in HTML.
        div()
            // We can chain methods to style the element.
            // Here, we center its children both horizontally and vertically.
            .flex()
            .justify_center()
            .items_center()
            .size_full() // Take up the full size of the window.
            .gap_4() // Add some space between our children.
            // The `child` method adds a child element.
            .child(
                // The `svg` element renders an SVG image.
                // We can specify the path to the SVG and style it.
                svg()
                    .path("icons/logo.svg")
                    .text_color(rgb(0xffffff))
                    .w_8()
                    .h_8(),
            )
            .child(
                // The `text` element renders a string of text.
                text("Hello, GPUI!").text_color(rgb(0xffffff)),
            )
    }
}

// 4. The `main` function is the entry point of our application.
fn main() {
    // 5. The `App::new()` function creates a new application instance.
    App::new()
        // The `run` method starts the application and takes a closure
        // that is called once the application is initialized.
        .run(|cx: &mut AppCsontext| {
            // 6. `cx.open_window` opens a new window. We provide window options
            //    and a closure that builds the view for the window.
            cx.open_window(WindowOptions::default(), |cx| {
                // 7. `cx.new_view` creates a new instance of our `HelloWorld` view.
                cx.new_view(|_cx| HelloWorld)
            });
        });
}
```

### Step 5: Add an Asset

Our code references an SVG icon at `icons/logo.svg`. GPUI applications need an `assets` directory in the project root for static files like images, fonts, and icons.

1.  Create an `assets/icons` directory in your project root:
    ```sh
    mkdir -p assets/icons
    ```
2.  Download the Zed logo SVG and save it as `assets/icons/logo.svg`. You can get it from the Zed repository or use a command like `curl`:
    ```sh
    curl -o assets/icons/logo.svg https://raw.githubusercontent.com/zed-industries/zed/main/assets/icons/logo.svg
    ```

### Step 6: Run the Application

You're all set! Compile and run your application from the project root:

```sh
cargo run
```

After a few moments of compiling, you should see a window appear with the text "Hello, GPUI!" and the Zed logo.

Congratulations! You've successfully built and run your first GPUI application.

## Summary

In this chapter, you learned how to:
- Set up a new Rust project with the GPUI dependency.
- Install the necessary system libraries for your operating system.
- Write a simple application that renders text and an SVG image.
- Understand the roles of `App`, `Window`, `View`, and `Elements` in a GPUI application.
- Include and use assets like SVG icons.

You now have a working GPUI development environment. You've taken the first and most important step on your journey to mastering GPUI.

## What's Next?

In **Chapter 2: Understanding the Architecture**, we will take a closer look at the core concepts of GPUI, such as the rendering pipeline, contexts, and views, to build a deeper understanding of how the framework operates.
