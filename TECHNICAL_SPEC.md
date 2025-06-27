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
- Step-by-step guided project, with code examples that gradually add complexity.

## Challenge Problems
- Independent practice opportunities

## Summary
- Key takeaways and next steps
```

## Code Example Standards
```rust
// All examples must:
// 1. Have clear comments explaining each step
// 2. Include error handling if appropriate
// 3. Use consistent formatting and style
// 5. Be compilable and runnable
// 6. Have minimal dependencies (except for GPUI)
// 7. Be as simple as possible to illustrate the concept, but no simpler

use gpui::*;

/// A simple counter component demonstrating state management
struct Counter {
    count: i32,
}

impl Counter {
    fn new() -> Self {
        Self { count: 0 }
    }

    fn increment(&mut self) {
        self.count += 1;
    }
}

impl Render for Counter {
    fn render(&mut self, cx: &mut ViewContext<Self>) -> impl IntoElement {
        div()
            .flex()
            .items_center()
            .gap_4()
            .child(format!("Count: {}", self.count))
            .child(
                button("Increment")
                    .on_click(cx.listener(|counter, _event, _cx| {
                        counter.increment();
                    }))
            )
    }
}
```
