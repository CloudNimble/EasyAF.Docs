# Mintlify Documentation Commands

Generate beautiful, interactive documentation from your .NET source code using Mintlify's modern documentation platform.

## Overview

The EasyAF Mintlify commands provide powerful documentation generation capabilities:

- **Auto-Generation**: Creates MDX documentation from XML comments
- **Multi-Solution Support**: Combines multiple projects into one documentation site
- **DocFX Integration**: Superior type resolution for generics and complex types
- **Live Updates**: Watch mode for real-time documentation updates
- **Mintlify Compatible**: Generates docs.json and MDX files ready for Mintlify

## Commands

### [`generate`](./generate.md) 
Generate Mintlify MDX documentation from .NET source code using DocFX.

```bash
dotnet easyaf mintlify generate [options]
```

### [`init`](./init.md)
Initialize a new Mintlify documentation site configuration.

```bash
dotnet easyaf mintlify init -n "Site Name" [options]
```

### [`watch`](./watch.md)
Watch for changes and regenerate documentation automatically.

```bash
dotnet easyaf mintlify watch [options]
```

## Quick Start

### Single Project Documentation

1. **Initialize documentation:**
   ```bash
   dotnet easyaf mintlify init -n "My Project API"
   ```

2. **Generate documentation:**
   ```bash
   dotnet easyaf mintlify generate
   ```

3. **Preview locally:**
   ```bash
   npx mintlify dev
   ```

### Multi-Solution Documentation

1. **Create solutions.json:**
   ```bash
   dotnet easyaf mintlify init --use-solutions -n "My Framework"
   ```

2. **Edit solutions.json to configure multiple solutions:**
   ```json
   {
     "solutions": [
       {
         "name": "Core Framework",
         "tab": "Core",
         "path": "../MyFramework.Core",
         "outputPath": "core"
       },
       {
         "name": "Extensions",
         "tab": "Extensions", 
         "path": "../MyFramework.Extensions",
         "outputPath": "extensions"
       }
     ]
   }
   ```

3. **Generate combined documentation:**
   ```bash
   dotnet easyaf mintlify generate
   ```

## Multi-Solution Configuration

### solutions.json Structure

```json
{
  "$schema": "https://easyaf.dev/schemas/solutions.json",
  "version": "1.0",
  "ignoreFolders": ["research", "temp"],
  "solutions": [
    {
      "name": "Core Framework",
      "tab": "Core",
      "source": "local",
      "path": "./src/core",
      "outputPath": "core",
      "projects": ["*.csproj"],
      "exclude": ["*.Tests.*.csproj"],
      "navigation": {
        "inherit": true
      }
    }
  ],
  "globalSettings": {
    "siteName": "My Documentation",
    "theme": "mint",
    "colors": {
      "primary": "#0078d4",
      "dark": "#0056b3"
    }
  }
}
```

### Solution Sources

#### Local Generation (`"source": "local"`)
Generate documentation from source code using DocFX:
```json
{
  "source": "local",
  "path": "./src/MyProject",
  "projects": ["*.csproj"],
  "exclude": ["*.Tests.*.csproj"]
}
```

#### External Documentation (`"source": "external"`)
Use pre-generated documentation from another location:
```json
{
  "source": "external", 
  "docsPath": "./external-docs",
  "mergeConfig": {
    "preserveStructure": true
  }
}
```

#### Hybrid Approach (`"source": "hybrid"`)
Generate API docs but use existing conceptual documentation:
```json
{
  "source": "hybrid",
  "path": "./src/MyProject", 
  "docsPath": "./existing-docs"
}
```

### Navigation Inheritance

Set `"inherit": true` to automatically use navigation from each solution's docs.json:

```json
{
  "navigation": {
    "inherit": true
  }
}
```

This will look for `docs.json` files in each solution and inherit their navigation structure.

## Features

### 🎯 **Intelligent Project Discovery**
- Auto-detects eligible projects in solutions
- Excludes test, template, and tool projects
- Supports custom inclusion/exclusion patterns

### 📝 **Rich Documentation Generation**
- Converts XML documentation to Mintlify MDX
- Generates navigation structure automatically
- Creates comprehensive API reference

### 🔗 **Superior Type Resolution**
- DocFX integration for complex generics
- Proper inheritance chains
- Cross-reference linking

### 🎨 **Customizable Theming**
- Multiple Mintlify themes
- Custom color schemes
- Logo and favicon support

### 📊 **Multi-Solution Aggregation**
- Combine multiple .NET solutions
- Tab-based navigation
- Unified search and branding

### ⚡ **Development Workflow**
- File watching for auto-regeneration
- Configurable change detection
- Real-time documentation updates

## Best Practices

### XML Documentation
Ensure your code has comprehensive XML documentation:

```csharp
/// <summary>
/// Represents a user in the system.
/// </summary>
/// <remarks>
/// This class provides core user functionality including authentication
/// and profile management capabilities.
/// </remarks>
public class User
{
    /// <summary>
    /// Gets or sets the unique identifier for the user.
    /// </summary>
    /// <value>A GUID that uniquely identifies the user.</value>
    public Guid Id { get; set; }
    
    /// <summary>
    /// Validates the user's credentials.
    /// </summary>
    /// <param name="password">The password to validate.</param>
    /// <returns>True if the password is valid; otherwise, false.</returns>
    /// <exception cref="ArgumentNullException">
    /// Thrown when <paramref name="password"/> is null or empty.
    /// </exception>
    public bool ValidatePassword(string password)
    {
        // Implementation
    }
}
```

### Project Configuration
Enable XML documentation generation in your .csproj files:

```xml
<PropertyGroup>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
  <DocumentationFile>bin\$(Configuration)\$(TargetFramework)\$(AssemblyName).xml</DocumentationFile>
</PropertyGroup>
```

### Conceptual Documentation
Create conceptual documentation files alongside API reference:
- `docs/conceptual/` - Tutorial and guide content
- `docs/overrides/` - Custom frontmatter and content injection

## Troubleshooting

### "No eligible projects found"
- Ensure projects have `GenerateDocumentationFile=true`
- Check that projects have been built at least once
- Verify projects are not test/template projects

### "DocFX metadata generation failed"
- Ensure all project dependencies are restored
- Check for compilation errors in source projects
- Verify .NET SDK version compatibility

### "Navigation inheritance failed"
- Check that docs.json files exist in solution paths
- Verify JSON syntax is valid
- Ensure file permissions allow reading

## Examples

See individual command documentation for detailed examples:
- [Generate Examples](./generate.md#examples)
- [Init Examples](./init.md#examples) 
- [Watch Examples](./watch.md#examples)

## Next Steps

- [Generate your first documentation](./generate.md)
- [Set up multi-solution docs](./generate.md#multi-solution-documentation)
- [Learn about watch mode](./watch.md)
- [Configure custom themes](./init.md#theme-configuration)