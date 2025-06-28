# Chapter 3: Basic Elements and Styling

## Learning Objectives
- Learn to use the core UI elements in GPUI: `div`, `text`, `img`, and `svg`.
- Understand GPUI's styling system, which is based on a subset of CSS properties.
- Apply flexbox for layout and arrangement of elements.
- Use colors, fonts, and other style properties to create visually appealing components.
- Build a "Profile Card" component as a hands-on exercise.

## Prerequisites
- You have completed Chapter 1 and have a basic "Hello, World!" GPUI application running.
- You are familiar with the concepts from Chapter 2, including `App`, `Window`, `View`, and the `Render` trait.

## Theory Section

In this chapter, we move from an empty window to building visible user interfaces. We'll explore the fundamental building blocks of any UI in GPUI: elements and the styles that give them form and color.

### Core Elements

GPUI provides a set of core elements that are sufficient to build a wide range of user interfaces. These are analogous to HTML tags like `<div>`, `<p>`, and `<img>`.

- **`div()`**: The most fundamental container element. It's used to group other elements, control layout, and apply styles. A `div` can contain any number of child elements.

- **`text("your text")`**: Used to render strings of text. You can style its color, font, size, and weight.

- **`img()`**: Displays raster images, such as PNGs or JPEGs. You provide it with a path to an image asset.

- **`svg()`**: Renders Scalable Vector Graphics (SVG) images. This is ideal for icons and illustrations that need to scale without losing quality.

### The Styling System

GPUI's styling system is heavily inspired by CSS but is fully implemented in Rust, providing type safety and compile-time checks. Styles are applied to elements using a chain of method calls.

For example, to create a red square, you would style a `div`:

```rust
use gpui::{div, rgb, Div};

fn red_square() -> Div {
    div()
        .size(100.) // Sets both width and height to 100px
        .bg(rgb(0xff0000)) // Sets background to red
}
```

Common styling properties include:
- **Sizing**: `.size(pixels)`, `.w(pixels)`, `.h(pixels)`
- **Spacing**: `.p(pixels)` (padding), `.m(pixels)` (margin)
- **Colors**: `.bg(color)` (background), `.text_color(color)`
- **Borders**: `.border()`, `.border_color(color)`, `.rounded(pixels)`

### Layout with Flexbox

GPUI uses a flexbox-like model for layout, which should be familiar to web developers. It allows you to arrange elements within a container along a primary axis (horizontal or vertical).

- **`flex()`**: Turns a `div` into a flex container.
- **`flex_row()` / `flex_col()`**: A shorthand for `div().flex()` and setting the flex direction.
- **`justify_center()` / `justify_between()`**: Distributes items along the main axis.
- **`items_center()`**: Aligns items along the cross axis.
- **`gap(pixels)`**: Adds space between flex items.

## Hands-On Exercise: "Profile Card"

Let's apply these concepts to build a styled "Profile Card" component. This exercise will combine layout, styling, text, and images.

### Project Setup

Create a new cargo project.

```sh
cargo new chapter_03
cd chapter_03
```

Install GPUI as a dependency.

```
cargo add gpui --git https://github.com/zed-industries/zed.git
```

### Create the Profile Card Component

First, ensure you have an image asset available. You can create a new file named `app-icon.svg` from the following code block.

```xml app-icon.svg
<svg width="100" height="150" viewBox="0 0 100 150" xmlns="http://www.w3.org/2000/svg">
  <circle cx="50" cy="35" r="25" fill="#fff"/>

  <path d="M20 70 L20 130 L80 130 L80 70 A10 10 0 0 0 70 60 L30 60 A10 10 0 0 0 20 70 Z" fill="#fff"/>
</svg>
```

For this example, we'll assume you've saved it to an `assets` directory in your project's root.

```
chapter_03/
├── assets/
│   └── app-icon.svg
├── src/
│   └── main.rs
└── Cargo.toml
```

Now, let's write the following code to `src/main.rs`.

```rust
use gpui::{
    App, AppContext, Application, AssetSource, Render, Result, SharedString, Window, WindowOptions,
    div, prelude::*, px, rgb, svg,
};
use std::borrow::Cow;
use std::fs;
use std::path::PathBuf;

// 1. Define an AssetSource for our app.
// This tells GPUI where to find assets like images and fonts.
struct Assets {
    base: PathBuf,
}

impl AssetSource for Assets {
    fn load(&self, path: &str) -> Result<Option<Cow<'static, [u8]>>> {
        // For this example, we'll use a simple `include_bytes!` macro
        // to bundle the asset into the binary. A real app might load
        // from the filesystem.
        fs::read(self.base.join(path))
            .map(|data| Some(std::borrow::Cow::Owned(data)))
            .map_err(|err| err.into())
    }

    fn list(&self, path: &str) -> gpui::Result<Vec<SharedString>> {
        fs::read_dir(self.base.join(path))
            .map(|entries| {
                entries
                    .filter_map(|entry| {
                        entry
                            .ok()
                            .and_then(|entry| entry.file_name().into_string().ok())
                            .map(SharedString::from)
                    })
                    .collect()
            })
            .map_err(|err| err.into())
    }
}

// 2. Create the View for our Profile Card.
struct ProfileCard;

impl Render for ProfileCard {
    fn render(&mut self, _window: &mut Window, _cx: &mut Context<Self>) -> impl IntoElement {
        // 3. Build the card using styled divs, an image, and text.
        div()
            // Container styling
            .flex()
            .bg(rgb(0x2e2e2e))
            .p_4()
            .rounded_lg()
            .shadow_lg()
            .gap_4()
            .items_center()
            .w(px(300.))
            // Children
            .child(
                // Profile picture
                svg()
                    .path("app-icon.svg")
                    .size_8()
                    .text_color(rgb(0x00ff00)),
            )
            .child(
                div()
                    .flex_col()
                    .gap_1()
                    .child(
                        // Name
                        div()
                            .text_xl()
                            .text_color(rgb(0xffffff))
                            .child("Ada Lovelace"),
                    )
                    .child(
                        // Bio
                        div()
                            .text_sm()
                            .text_color(rgb(0x8e8e8e))
                            .child("The first programmer."),
                    ),
            )
    }
}

fn main() {
    println!("{}", env!("CARGO_MANIFEST_DIR"));
    Application::new()
        .with_assets(Assets {
            base: PathBuf::from(env!("CARGO_MANIFEST_DIR")).join("assets"),
        })
        .run(|cx: &mut App| {
            cx.open_window(WindowOptions::default(), |_, cx| {
                // Center the profile card in the window
                cx.new(|_| ProfileCard)
            })
            .unwrap();

            cx.activate(true);
        });
}
```

When you run this application (`cargo run`), you should see a styled profile card in the center of your window.

## Challenge Problems

1.  **Add a Status Indicator**: Modify the `ProfileCard` to include a small, colored circle on the corner of the profile picture to indicate an "online" (green) or "offline" (gray) status.
2.  **Create a Horizontal List**: Change the main view to display three `ProfileCard` components arranged in a horizontal row.
3.  **Add a Follow Button**: Add a styled `div` that looks like a button with the text "Follow" to the right of the name and bio. (We'll cover click handlers in the next chapter).

## Summary

In this chapter, you learned how to use GPUI's core elements and its powerful, CSS-inspired styling system. You can now create static UI components, arrange them with flexbox, and apply a wide variety of styles. The "Profile Card" exercise demonstrates how these simple primitives can be composed to build complex and attractive user interfaces.

In the next chapter, we'll make our components interactive by handling user input.
