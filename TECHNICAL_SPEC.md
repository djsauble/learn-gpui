# Learn GPUI Book: Technical Specifications

The GPUI Book will be Markdown files hosted on GitHub.

## File Structure

```
learn-gpui/
├── book/
│   ├── CHAPTER_01.md
│   ├── CHAPTER_02.md
│   ├── CHAPTER_03.md
│   └── ...
└── examples/
```

## Chapter Outline

```markdown
# Chapter Title

## Learning Objectives
- Bullet points of what readers will learn

## Prerequisites
- Required knowledge from previous chapters

## Theory Section
- Concept explanations with diagrams (Mermaid.js)

## Hands-On Exercise
- Guided project, with code examples that progressively add complexity.

## Challenge Problems
- Independent practice opportunities

## Summary
- Key takeaways and next steps
```

## Source documentation

IMPORTANT: DO NOT MAKE THINGS UP, ALWAYS LOAD THE ZED SOURCE CODE INTO CONTEXT

You can reference the GPUI source code and examples at: `https://github.com/zed-industries/zed/tree/main/crates/gpui`

I've also cloned the GPUI code into this project, if that's easier: `src/crates/gpui/`

## Code Example Standards
```rust
// All examples must:
// 1. Have clear comments explaining each step
// 2. Include error handling if appropriate
// 3. Use consistent formatting and style
// 5. Be compilable and runnable
// 6. Have minimal dependencies (except for GPUI)
// 7. Be as simple as possible to illustrate the concept, but no simpler

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
