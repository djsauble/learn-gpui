# Chapter 3: Basic Elements and Styling

## Learning Objectives

By the end of this chapter, you will be able to:
- Use core GPUI elements (`div`, `text`, `img`, `svg`) to build interfaces
- Apply styling using GPUI's CSS-like system
- Understand flexbox layout in GPUI
- Create responsive designs that adapt to different screen sizes
- Build a complete, styled user profile component

## Prerequisites

- Completion of Chapter 1 (Getting Started) and Chapter 2 (Understanding the Architecture)
- Basic understanding of CSS concepts (helpful but not required)
- Familiarity with Rust structs and methods

## Introduction to GPUI Elements

GPUI provides a set of fundamental elements that serve as building blocks for your user interfaces. These elements are similar to HTML elements but are optimized for high-performance rendering and native desktop applications.

### The Element Hierarchy

```rust
use gpui::*;

// Every GPUI element implements the IntoElement trait
// This allows them to be composed together in a tree structure
div()                    // Container element
    .child(text("Hello")) // Text element as child
    .child(               // Another child
        div()
            .child(text("Nested content"))
    )
```

## Core Elements Deep Dive

### The `div` Element

The `div` element is the most fundamental container in GPUI. It's equivalent to HTML's `<div>` and serves as the foundation for layout and styling.

```rust
use gpui::*;

struct BasicDiv;

impl Render for BasicDiv {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .size_full()                    // Take full available space
            .bg(rgb(0xf0f0f0))             // Light gray background
            .border_1()                     // 1px border
            .border_color(rgb(0xcccccc))   // Border color
            .p_4()                         // 16px padding on all sides
            .child("This is a basic div")
    }
}
```

**Key `div` methods:**
- `size_*()`: Control dimensions (`size_full()`, `w_64()`, `h_32()`)
- `bg()`: Background color
- `border_*()`: Border properties
- `p_*()`, `m_*()`: Padding and margin
- `rounded_*()`: Border radius

### The `text` Element

Text elements handle all text rendering, including font styling, color, and typography.

```rust
use gpui::*;

struct TextExamples;

impl Render for TextExamples {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .flex()
            .flex_col()
            .gap_4()
            .p_6()
            .child(
                text("Large Title")
                    .text_3xl()                    // Large text size
                    .font_bold()                   // Bold weight
                    .text_color(rgb(0x1a1a1a))    // Dark gray color
            )
            .child(
                text("This is a subtitle with different styling")
                    .text_lg()                     // Large text
                    .text_color(rgb(0x666666))    // Medium gray
                    .italic()                      // Italic style
            )
            .child(
                text("Body text with custom font family")
                    .text_base()                   // Base text size
                    .font_family("Inter")          // Custom font
                    .line_height(1.6)             // Line spacing
            )
    }
}
```

### Working with Images

The `img` element handles image display with various sizing and positioning options.

```rust
use gpui::*;

struct ImageExample;

impl Render for ImageExample {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .flex()
            .items_center()
            .gap_4()
            .p_6()
            .child(
                img("assets/profile-photo.jpg")
                    .w_16()                        // 64px width
                    .h_16()                        // 64px height
                    .rounded_full()                // Circular image
                    .object_cover()                // Cover the container
            )
            .child(
                div()
                    .flex()
                    .flex_col()
                    .child(text("John Doe").font_semibold())
                    .child(text("Software Engineer").text_sm().text_color(rgb(0x666666)))
            )
    }
}
```

## Styling System

GPUI's styling system is inspired by Tailwind CSS, providing utility classes for rapid UI development.

### Color System

```rust
use gpui::*;

struct ColorExamples;

impl Render for ColorExamples {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .flex()
            .gap_2()
            .p_4()
            // Using predefined colors
            .child(
                div()
                    .w_16()
                    .h_16()
                    .bg(blue_500())                // Predefined blue
                    .rounded_lg()
            )
            // Using RGB values
            .child(
                div()
                    .w_16()
                    .h_16()
                    .bg(rgb(0x10b981))            // Custom green
                    .rounded_lg()
            )
            // Using HSL values
            .child(
                div()
                    .w_16()
                    .h_16()
                    .bg(hsl(220, 0.9, 0.6))       // Custom blue via HSL
                    .rounded_lg()
            )
    }
}
```

### Spacing and Sizing

GPUI uses a consistent spacing scale based on a 4px grid system:

```rust
use gpui::*;

struct SpacingExample;

impl Render for SpacingExample {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .p_8()                             // 32px padding
            .child(
                div()
                    .w_64()                    // 256px width
                    .h_32()                    // 128px height
                    .bg(blue_100())
                    .border_2()                // 2px border
                    .border_color(blue_500())
                    .rounded_xl()              // Large border radius
                    .m_4()                     // 16px margin
                    .flex()
                    .items_center()
                    .justify_center()
                    .child(text("Styled Box"))
            )
    }
}
```

**Spacing Scale:**
- `1` = 4px
- `2` = 8px
- `4` = 16px
- `8` = 32px
- `16` = 64px
- `32` = 128px
- `64` = 256px

## Flexbox Layout

GPUI's layout system is built on Flexbox, providing powerful and intuitive layout capabilities.

### Basic Flex Container

```rust
use gpui::*;

struct FlexExample;

impl Render for FlexExample {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .flex()                            // Enable flexbox
            .flex_row()                        // Horizontal direction (default)
            .justify_between()                 // Space between items
            .items_center()                    // Center items vertically
            .p_6()
            .gap_4()                          // Space between children
            .child(
                div()
                    .w_16()
                    .h_16()
                    .bg(red_500())
                    .rounded_lg()
            )
            .child(
                div()
                    .flex_1()                  // Take remaining space
                    .h_16()
                    .bg(green_500())
                    .rounded_lg()
            )
            .child(
                div()
                    .w_16()
                    .h_16()
                    .bg(blue_500())
                    .rounded_lg()
            )
    }
}
```

### Vertical Layout

```rust
use gpui::*;

struct VerticalLayout;

impl Render for VerticalLayout {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .flex()
            .flex_col()                        // Vertical direction
            .h_96()                           // Fixed height
            .p_6()
            .gap_3()
            .child(
                div()
                    .h_16()
                    .bg(purple_500())
                    .rounded_lg()
                    .flex()
                    .items_center()
                    .justify_center()
                    .child(text("Header").text_white().font_semibold())
            )
            .child(
                div()
                    .flex_1()                  // Expand to fill space
                    .bg(gray_100())
                    .rounded_lg()
                    .p_4()
                    .child(text("Main Content Area"))
            )
            .child(
                div()
                    .h_12()
                    .bg(gray_600())
                    .rounded_lg()
                    .flex()
                    .items_center()
                    .justify_center()
                    .child(text("Footer").text_white().text_sm())
            )
    }
}
```

## Responsive Design

GPUI supports responsive design through conditional styling and dynamic layout adjustments.

```rust
use gpui::*;

struct ResponsiveCard;

impl Render for ResponsiveCard {
    fn render(&mut self, cx: &mut ViewContext<Self>) -> impl IntoElement {
        let window_size = cx.window_bounds().size;
        let is_mobile = window_size.width < px(768.0);
        
        div()
            .flex()
            .when(is_mobile, |div| {
                div.flex_col()                 // Stack vertically on mobile
            })
            .when(!is_mobile, |div| {
                div.flex_row()                 // Side by side on desktop
            })
            .gap_4()
            .p_6()
            .child(
                img("assets/product-image.jpg")
                    .when(is_mobile, |img| {
                        img.w_full().h_48()    // Full width on mobile
                    })
                    .when(!is_mobile, |img| {
                        img.w_64().h_64()      // Fixed size on desktop
                    })
                    .object_cover()
                    .rounded_lg()
            )
            .child(
                div()
                    .flex()
                    .flex_col()
                    .flex_1()
                    .gap_2()
                    .child(
                        text("Product Name")
                            .text_xl()
                            .font_bold()
                    )
                    .child(
                        text("$29.99")
                            .text_lg()
                            .text_color(green_600())
                            .font_semibold()
                    )
                    .child(
                        text("This is a description of the product with details about its features and benefits.")
                            .text_sm()
                            .text_color(gray_600())
                            .line_height(1.5)
                    )
            )
    }
}
```

## Hands-On Exercise: Profile Card

Let's build a complete user profile card component that demonstrates all the concepts we've learned.

### Step 1: Basic Structure

Create a new file `src/profile_card.rs`:

```rust
use gpui::*;

pub struct ProfileCard {
    name: SharedString,
    title: SharedString,
    avatar_url: SharedString,
    is_online: bool,
}

impl ProfileCard {
    pub fn new(name: &str, title: &str, avatar_url: &str, is_online: bool) -> Self {
        Self {
            name: name.into(),
            title: title.into(),
            avatar_url: avatar_url.into(),
            is_online,
        }
    }
}

impl Render for ProfileCard {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .w_80()                           // Fixed width
            .bg(white())                      // White background
            .border_1()                       // Subtle border
            .border_color(gray_200())
            .rounded_xl()                     // Rounded corners
            .shadow_lg()                      // Drop shadow
            .p_6()                           // Internal padding
            .flex()
            .flex_col()
            .gap_4()
            .child(self.render_header())
            .child(self.render_info())
            .child(self.render_actions())
    }
}
```

### Step 2: Header with Avatar

```rust
impl ProfileCard {
    fn render_header(&self) -> impl IntoElement {
        div()
            .flex()
            .items_center()
            .gap_4()
            .child(
                div()
                    .relative()                   // Position context for badge
                    .child(
                        img(&self.avatar_url)
                            .w_16()
                            .h_16()
                            .rounded_full()
                            .object_cover()
                            .border_2()
                            .border_color(gray_200())
                    )
                    .child(
                        // Online status indicator
                        div()
                            .absolute()
                            .bottom_0()
                            .right_0()
                            .w_4()
                            .h_4()
                            .rounded_full()
                            .border_2()
                            .border_color(white())
                            .bg(if self.is_online { 
                                green_500() 
                            } else { 
                                gray_400() 
                            })
                    )
            )
            .child(
                div()
                    .flex()
                    .flex_col()
                    .child(
                        text(&self.name)
                            .text_lg()
                            .font_semibold()
                            .text_color(gray_900())
                    )
                    .child(
                        text(&self.title)
                            .text_sm()
                            .text_color(gray_500())
                    )
            )
    }
}
```

### Step 3: Information Section

```rust
impl ProfileCard {
    fn render_info(&self) -> impl IntoElement {
        div()
            .flex()
            .justify_between()
            .py_4()
            .border_y_1()
            .border_color(gray_100())
            .child(
                div()
                    .flex()
                    .flex_col()
                    .items_center()
                    .child(
                        text("1,234")
                            .text_lg()
                            .font_bold()
                            .text_color(gray_900())
                    )
                    .child(
                        text("Followers")
                            .text_xs()
                            .text_color(gray_500())
                            .uppercase()
                    )
            )
            .child(
                div()
                    .flex()
                    .flex_col()
                    .items_center()
                    .child(
                        text("567")
                            .text_lg()
                            .font_bold()
                            .text_color(gray_900())
                    )
                    .child(
                        text("Following")
                            .text_xs()
                            .text_color(gray_500())
                            .uppercase()
                    )
            )
            .child(
                div()
                    .flex()
                    .flex_col()
                    .items_center()
                    .child(
                        text("89")
                            .text_lg()
                            .font_bold()
                            .text_color(gray_900())
                    )
                    .child(
                        text("Posts")
                            .text_xs()
                            .text_color(gray_500())
                            .uppercase()
                    )
            )
    }
}
```

### Step 4: Action Buttons

```rust
impl ProfileCard {
    fn render_actions(&self) -> impl IntoElement {
        div()
            .flex()
            .gap_3()
            .child(
                button("Follow")
                    .flex_1()
                    .h_10()
                    .bg(blue_500())
                    .hover(|style| style.bg(blue_600()))
                    .text_color(white())
                    .font_medium()
                    .rounded_lg()
                    .transition_colors()
            )
            .child(
                button("Message")
                    .flex_1()
                    .h_10()
                    .bg(gray_100())
                    .hover(|style| style.bg(gray_200()))
                    .text_color(gray_700())
                    .font_medium()
                    .rounded_lg()
                    .border_1()
                    .border_color(gray_300())
                    .transition_colors()
            )
    }
}
```

### Step 5: Using the Profile Card

In your `main.rs`:

```rust
use gpui::*;
mod profile_card;
use profile_card::ProfileCard;

struct App {
    profile: View<ProfileCard>,
}

impl Render for App {
    fn render(&mut self, _cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .size_full()
            .bg(gray_50())
            .flex()
            .items_center()
            .justify_center()
            .child(self.profile.clone())
    }
}

fn main() {
    App::new().run(|cx: &mut AppContext| {
        cx.open_window(WindowOptions::default(), |cx| {
            let profile = cx.new_view(|_cx| ProfileCard::new(
                "Sarah Johnson",
                "Senior UI/UX Designer",
                "https://images.unsplash.com/photo-1494790108755-2616b612b786",
                true
            ));
            
            cx.new_view(|_cx| App { profile })
        });
    });
}
```

## Challenge Problems

### Challenge 1: Theme Variants
Modify the ProfileCard to support light and dark themes. Add a `theme` parameter that changes the color scheme.

### Challenge 2: Responsive Layout
Make the ProfileCard responsive so it stacks elements vertically on narrow screens and horizontally on wide screens.

### Challenge 3: Interactive States
Add hover and focus states to the action buttons, and implement click handlers that show different button text when activated.

### Challenge 4: Custom Avatar Fallback
When an avatar image fails to load, show a generated avatar with the user's initials instead.

## Summary

In this chapter, you learned:

- **Core Elements**: How to use `div`, `text`, and `img` elements as building blocks
- **Styling System**: GPUI's utility-first approach to styling with colors, spacing, and typography
- **Flexbox Layout**: Creating flexible, responsive layouts with flexbox properties
- **Responsive Design**: Building interfaces that adapt to different screen sizes
- **Component Composition**: Combining elements into reusable, well-structured components

These fundamentals form the foundation for all GPUI interfaces. In the next chapter, we'll explore handling user input and creating interactive components.

## What's Next?

In **Chapter 4: Handling User Input**, you'll learn how to:
- Respond to mouse clicks and keyboard input
- Manage focus and form interactions
- Handle drag and drop operations
- Build truly interactive applications

The profile card you built here will serve as the foundation for adding interactive features in the upcoming chapters.

## Additional Resources

- [GPUI Styling Reference](../appendix/styling-reference.md)
- [Flexbox Layout Guide](../appendix/flexbox-guide.md)
- [Color System Documentation](../appendix/color-system.md)
- [Community Examples Gallery](https://examples.gpui.rs/styling)

---

*Estimated completion time: 45-60 minutes*  
*Difficulty: Beginner to Intermediate*  
*Prerequisites: Chapters 1-2*