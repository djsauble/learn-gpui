# The GPUI Book: A Comprehensive Learning Plan

A progressive, hands-on guide to mastering [GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui) - the fast, productive UI framework for Rust from the creators of Zed.

## Target Audience

- Rust developers new to GUI programming
- Developers familiar with other UI frameworks (React, Flutter, etc.)
- Experienced GUI developers wanting to learn GPUI specifically

## Book Structure

### [Chapter 1: Getting Started](./book/CHAPTER_01.md)
- What is GPUI and why use it?
- Installation and setup (macOS, Linux)
- Development environment setup
- Your first GPUI application
- **Project**: "Hello, world!" - Basic window with text

### [Chapter 2: Understanding the Architecture](./book/CHAPTER_02.md)
- App, Contexts, and Views explained
- The rendering pipeline
- Immediate vs retained mode concepts
- Element tree and layout system
- **Project**: "Multiple Views" - App with multiple windows

### Chapter 3: Basic Elements and Styling
- Core elements: `div`, `text`, `img`, `svg`
- Styling system (flexbox, colors, fonts)
- Layout fundamentals
- Responsive design principles
- **Project**: "Profile Card" - Styled user profile component

### Chapter 4: Handling User Input
- Mouse events (click, hover, drag)
- Keyboard input and focus management
- Form elements and validation
- **Project**: "Interactive Counter" - Click handlers and state updates

### Chapter 5: State Management Basics
- View state vs shared state
- Using `ViewContext` for local state
- Re-rendering and reactivity
- **Project**: "Todo Item" - Stateful component with toggles

### Chapter 6: Building Reusable Components
- Component composition patterns
- Props and configuration
- Component lifecycle
- **Project**: "Button Library" - Reusable button components

### Chapter 7: Actions and Keybindings
- Defining actions
- Keyboard shortcuts and menu integration
- Command palette patterns
- **Project**: "Text Editor Basics" - Simple editor with keybindings

### Chapter 8: Models and Shared State
- Global state management with Models
- Observer pattern and notifications
- Data flow best practices
- **Project**: "Shopping Cart" - Multi-view state sharing

### Chapter 9: Lists and Collections
- Rendering dynamic lists
- Virtual scrolling and performance
- List item selection and interaction
- **Project**: "File Browser" - Hierarchical list navigation

### Chapter 10: Advanced Layout Patterns
- Complex flexbox layouts
- Grid systems
- Responsive breakpoints
- Custom layout algorithms
- **Project**: "Dashboard" - Multi-panel responsive layout

### Chapter 11: Styling and Theming
- Theme system architecture
- CSS-like styling patterns
- Dynamic theming
- Design system creation
- **Project**: "Theme Switcher" - Light/dark mode toggle

### Chapter 12: Images, Icons, and Assets
- Asset loading and management
- SVG rendering and manipulation
- Image optimization
- Icon systems
- **Project**: "Photo Gallery" - Image display with thumbnails

### Chapter 13: Animation and Transitions
- Animation system overview
- Easing functions and curves
- Performance considerations
- Micro-interactions
- **Project**: "Animated Dashboard" - Smooth transitions

### Chapter 14: Custom Elements and Rendering
- Creating custom elements
- Canvas and direct rendering
- Graphics primitives
- **Project**: "Chart Component" - Custom data visualization

### Chapter 15: Drag and Drop
- Drag and drop architecture
- Data transfer between components
- Visual feedback patterns
- **Project**: "Kanban Board" - Drag and drop task management

### Chapter 16: Window Management
- Multiple windows and dialogs
- Window positioning and sizing
- Modal patterns
- **Project**: "IDE-like Interface" - Multi-window application

### Chapter 17: Performance Optimization
- Profiling GPUI applications
- Rendering optimization techniques
- Memory management
- Large dataset handling
- **Project**: "Performance Dashboard" - Monitoring and optimization

### Chapter 18: Testing GPUI Applications
- Unit testing strategies
- Integration testing
- Visual regression testing
- **Project**: "Test Suite" - Comprehensive testing examples

### Chapter 19: Text Editor Deep Dive
- Advanced text rendering
- Syntax highlighting integration
- Plugin architecture
- **Project**: "Code Editor" - Feature-rich text editor

### Chapter 20: Database Integration
- Async operations in GPUI
- Database connectivity patterns
- Real-time data updates
- **Project**: "Contact Manager" - CRUD application

### Chapter 21: Networking and APIs
- HTTP client integration
- WebSocket connections
- Error handling strategies
- **Project**: "Chat Application" - Real-time messaging

### Chapter 22: File System Integration
- File I/O operations
- File watching and updates
- Cross-platform considerations
- **Project**: "Note-Taking App" - File-based note management

### Chapter 23: Advanced Architecture Patterns
- Plugin systems
- Event-driven architecture
- Modular application design
- **Project**: "Extensible App" - Plugin-based architecture

### Chapter 24: Deployment and Distribution
- Building for production
- Cross-platform compilation
- Package management
- App store distribution
- **Project**: "Released App" - Complete deployment pipeline
