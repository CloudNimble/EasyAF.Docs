# Component Design

## Overview

Custom React components are essential for creating an interactive, consistent documentation experience that matches learn.microsoft.com's quality while showcasing SimpleMessageBus effectively.

## Core Components

### 1. ApiReference Component

Displays comprehensive API documentation for types and members.

```jsx
interface ApiReferenceProps {
  type: TypeInfo;
  members: MemberInfo[];
  inheritance: InheritanceChain;
  examples: CodeExample[];
}

<ApiReference>
  <ApiHeader />
  <ApiInheritance />
  <ApiSyntax />
  <ApiDescription />
  <ApiMembers />
  <ApiExamples />
  <ApiRemarks />
  <ApiSeeAlso />
</ApiReference>
```

**Features:**
- Syntax highlighting with Prism
- Collapsible sections
- Copy code buttons
- Cross-reference links
- Type parameter documentation

### 2. CodeExample Component

Interactive code examples with multiple language support.

```jsx
interface CodeExampleProps {
  title?: string;
  description?: string;
  code: {
    csharp: string;
    xml?: string;
    json?: string;
  };
  runnable?: boolean;
  output?: string;
}

<CodeExample 
  title="Publishing a Message"
  runnable={true}
>
  {`var message = new OrderCreatedMessage 
  { 
    Id = Guid.NewGuid(),
    OrderNumber = "ORD-001" 
  };
  await publisher.PublishAsync(message);`}
</CodeExample>
```

**Features:**
- Language tabs
- Syntax highlighting
- Copy button
- Run button (for compatible examples)
- Output display
- Line highlighting

### 3. ProviderComparison Component

Interactive comparison matrix for queue providers.

```jsx
<ProviderComparison>
  <ComparisonRow feature="Setup Complexity" 
    azure="Medium" 
    filesystem="Low" 
    amazon="Medium" 
    indexeddb="Low" 
  />
  <ComparisonRow feature="Scalability" 
    azure="High" 
    filesystem="Low" 
    amazon="High" 
    indexeddb="Medium" 
  />
</ProviderComparison>
```

**Features:**
- Sortable columns
- Filterable rows
- Responsive design
- Tooltips for details
- Visual indicators

### 4. InteractiveArchitecture Component

SVG-based architecture diagrams with interactivity.

```jsx
<InteractiveArchitecture>
  <ArchNode id="publisher" label="Message Publisher" />
  <ArchNode id="queue" label="Queue Storage" />
  <ArchNode id="processor" label="Queue Processor" />
  <ArchNode id="handler" label="Message Handler" />
  <ArchFlow from="publisher" to="queue" />
  <ArchFlow from="queue" to="processor" />
  <ArchFlow from="processor" to="handler" />
</InteractiveArchitecture>
```

**Features:**
- Clickable nodes
- Animated flows
- Zoom and pan
- Tooltips
- Export as image

### 5. ConfigurationBuilder Component

Interactive configuration generator.

```jsx
<ConfigurationBuilder provider="azure">
  <ConfigOption 
    name="ConnectionString" 
    type="string" 
    required={true}
  />
  <ConfigOption 
    name="QueueName" 
    type="string" 
    default="messages"
  />
  <ConfigOutput format="json" />
  <ConfigOutput format="csharp" />
</ConfigurationBuilder>
```

**Features:**
- Dynamic form generation
- Validation
- Multiple output formats
- Copy configuration
- Import/export

### 6. MessageFlowDiagram Component

Visualize message lifecycle through the system.

```jsx
<MessageFlowDiagram scenario="poison-queue">
  <FlowStep status="success">Message Published</FlowStep>
  <FlowStep status="success">Message Queued</FlowStep>
  <FlowStep status="error">Handler Fails</FlowStep>
  <FlowStep status="warning">Retry Attempt 1</FlowStep>
  <FlowStep status="error">Handler Fails</FlowStep>
  <FlowStep status="danger">Moved to Poison Queue</FlowStep>
</MessageFlowDiagram>
```

**Features:**
- Step-by-step visualization
- Status indicators
- Expandable details
- Timeline view
- Error highlighting

### 7. SearchResults Component

Enhanced search results with categorization.

```jsx
<SearchResults query="message">
  <SearchCategory name="API Reference" count={15}>
    <SearchResult 
      title="IMessage Interface"
      path="/api/core/imessage"
      excerpt="Defines the required composition..."
      type="interface"
    />
  </SearchCategory>
  <SearchCategory name="Guides" count={8}>
    <SearchResult 
      title="Message Design Patterns"
      path="/docs/guides/message-patterns"
      excerpt="Best practices for designing..."
      type="guide"
    />
  </SearchCategory>
</SearchResults>
```

**Features:**
- Categorized results
- Type indicators
- Highlighted matches
- Instant preview
- Keyboard navigation

### 8. VersionSelector Component

Documentation version management.

```jsx
<VersionSelector currentVersion="6.0">
  <Version value="6.0" label="v6.0 (latest)" />
  <Version value="5.0" label="v5.0" />
  <Version value="4.0" label="v4.0 (deprecated)" />
</VersionSelector>
```

**Features:**
- Version switching
- Migration warnings
- Release notes link
- Comparison view

## Shared Component Library

### UI Primitives

```jsx
// Consistent UI components
<Alert type="info" />
<Badge text="New" variant="success" />
<Button variant="primary" size="large" />
<Card title="Quick Start" icon="rocket" />
<Tabs defaultTab="csharp" />
<Tooltip content="More info" />
<Collapsible title="Advanced Options" />
```

### Code Components

```jsx
// Code-specific components
<SyntaxHighlighter language="csharp" />
<LineNumbers start={1} highlight={[3,4,5]} />
<CopyButton text={codeContent} />
<DiffViewer before={oldCode} after={newCode} />
```

### Navigation Components

```jsx
// Navigation helpers
<Breadcrumbs />
<TableOfContents headings={headings} />
<Pagination prev={prevPage} next={nextPage} />
<SidebarMenu items={menuItems} />
```

## Component Architecture Principles

### 1. Composition over Inheritance
- Small, focused components
- Composable building blocks
- Reusable patterns

### 2. Accessibility First
- ARIA labels
- Keyboard navigation
- Screen reader support
- Focus management

### 3. Performance Optimization
- Lazy loading
- Code splitting
- Memoization
- Virtual scrolling

### 4. Responsive Design
- Mobile-first approach
- Flexible layouts
- Touch-friendly interactions
- Progressive enhancement

## Styling Strategy

### CSS Modules
```css
/* components/ApiReference/styles.module.css */
.container {
  border: 1px solid var(--ifm-color-emphasis-200);
  border-radius: var(--ifm-global-radius);
}

.header {
  background: var(--ifm-color-emphasis-100);
  padding: var(--ifm-spacing-vertical);
}
```

### Theme Variables
```css
/* Custom theme variables */
:root {
  --smb-color-primary: #0078d4;
  --smb-syntax-bg: #1e1e1e;
  --smb-inline-code-bg: #f3f4f6;
}
```

### Dark Mode Support
```css
[data-theme='dark'] {
  --smb-syntax-bg: #1a1a1a;
  --smb-inline-code-bg: #2d2d2d;
}
```

## Component Testing Strategy

### Unit Tests
- React Testing Library
- Component behavior
- Accessibility checks
- Snapshot tests

### Integration Tests
- User interactions
- API integration
- Search functionality
- Navigation flows

### Visual Regression
- Storybook snapshots
- Cross-browser testing
- Responsive layouts
- Theme variations