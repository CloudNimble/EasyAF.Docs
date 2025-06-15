# Site Structure

## Information Architecture

The SimpleMessageBus documentation site is organized to support multiple user journeys while maintaining a clear hierarchy that matches the project's modular architecture.

## Navigation Hierarchy

```
SimpleMessageBus Documentation
├── Home (Landing Page)
├── Getting Started
│   ├── Overview
│   ├── Installation
│   ├── Quick Start (5 minutes)
│   ├── Your First Message
│   └── Next Steps
├── Core Concepts
│   ├── Architecture Overview
│   ├── Messages & Envelopes
│   ├── Handlers & Processing
│   ├── Publishers & Dispatchers
│   ├── Queue Providers
│   └── Error Handling & Poison Queues
├── Guides
│   ├── Choosing a Provider
│   ├── Message Design Patterns
│   ├── Testing with Breakdance
│   ├── Performance Optimization
│   ├── Monitoring & Observability
│   └── Migration Guides
├── Providers
│   ├── Azure Storage Queues
│   │   ├── Setup & Configuration
│   │   ├── WebJobs Integration
│   │   ├── Scaling Strategies
│   │   └── Best Practices
│   ├── File System
│   │   ├── Setup & Configuration
│   │   ├── Cross-Platform Usage
│   │   ├── Performance Tuning
│   │   └── Development Workflow
│   ├── Amazon SQS
│   │   ├── Setup & Configuration
│   │   ├── IAM & Security
│   │   ├── FIFO vs Standard
│   │   └── Cost Optimization
│   └── IndexedDb (Blazor)
│       ├── Setup & Configuration
│       ├── Browser Compatibility
│       ├── Offline Scenarios
│       └── Performance Limits
├── API Reference
│   ├── Core
│   │   ├── IMessage
│   │   ├── IMessageHandler
│   │   ├── MessageEnvelope
│   │   └── [All Types...]
│   ├── Publishing
│   │   ├── IMessagePublisher
│   │   ├── FileSystemMessagePublisher
│   │   └── [All Types...]
│   ├── Dispatching
│   │   ├── IMessageDispatcher
│   │   ├── IQueueProcessor
│   │   └── [All Types...]
│   └── Extensions
│       ├── Hosting Extensions
│       ├── DI Extensions
│       └── [All Extensions...]
├── Samples & Tutorials
│   ├── Console Application
│   ├── Azure WebJobs
│   ├── Blazor WebAssembly
│   ├── Microservices Pattern
│   └── Real-World Scenarios
├── Contributing
│   ├── Development Setup
│   ├── Architecture Guide
│   ├── Adding Providers
│   └── Documentation Guide
└── Resources
    ├── Roadmap
    ├── Changelog
    ├── FAQ
    └── Support
```

## URL Structure

```
/                                    # Landing page
/docs/getting-started                # Getting started section
/docs/getting-started/overview       # Individual pages
/docs/concepts/architecture          # Core concepts
/docs/guides/message-patterns        # How-to guides
/docs/providers/azure                # Provider docs
/docs/providers/azure/setup          # Provider details
/api/                               # API reference root
/api/core/imessage                  # Individual API pages
/api/core/imessagehandler           
/samples/                           # Interactive samples
/samples/console-app                # Sample walkthroughs
/blog/                             # News and updates
/blog/2025/01/v6-release           # Blog posts
```

## Page Types

### 1. Landing Page
- Hero section with value proposition
- Quick start CTA
- Feature highlights
- Provider comparison
- Community stats

### 2. Documentation Pages
- Clear page title
- Table of contents
- Content with examples
- Related links
- Edit on GitHub link

### 3. API Reference Pages
- Type/member name
- Inheritance hierarchy
- Syntax highlighting
- Parameters table
- Examples section
- See also links

### 4. Interactive Pages
- Code playground
- Live examples
- Configuration builders
- Provider selector wizard

## Navigation Components

### Top Navigation Bar
```
[Logo] SimpleMessageBus  Docs  API  Samples  Blog  GitHub
                                                    [Search]
```

### Sidebar Navigation
- Collapsible sections
- Active page highlighting
- Breadcrumb trail
- Version selector
- Progress indicators

### Footer
```
Community          Resources         More
GitHub             Documentation      Blog
Discord            Roadmap            CloudNimble
Stack Overflow     Samples            Privacy
Contributing       Support            Terms
```

## Content Organization Principles

### 1. Task-Oriented Structure
- Group by what users want to do
- Clear action-oriented titles
- Progressive disclosure

### 2. Provider-Specific Sections
- Consistent structure across providers
- Easy comparison
- Provider-specific features highlighted

### 3. API Organization
- Namespace-based grouping
- Alphabetical within namespaces
- Inheritance relationships visible

### 4. Search-First Design
- Every page optimized for search
- Rich metadata
- Contextual results

## Mobile Navigation

### Responsive Breakpoints
- Desktop: 1024px+
- Tablet: 768px-1023px
- Mobile: <768px

### Mobile Menu
- Hamburger menu
- Full-screen overlay
- Touch-optimized
- Search prominent

## Accessibility

### WCAG 2.1 Compliance
- Semantic HTML structure
- ARIA labels
- Keyboard navigation
- Skip navigation links

### Reading Experience
- High contrast mode
- Adjustable font size
- Clear typography
- Sufficient whitespace

## SEO Structure

### URL Best Practices
- Descriptive URLs
- Consistent patterns
- No deep nesting
- Canonical URLs

### Metadata
- Page titles
- Meta descriptions
- Open Graph tags
- Schema.org markup

## Analytics Tracking

### Key Metrics
- Page views by section
- Search queries
- 404 errors
- User journeys
- Time on page

### Events
- Downloads
- Code copy
- External links
- Search usage
- Feedback submission