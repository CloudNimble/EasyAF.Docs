# dotnet easyaf mintlify generate

Generate Mintlify MDX documentation from .NET source code using DocFX for superior type resolution.

## Synopsis

```bash
dotnet easyaf mintlify generate [options]
```

## Description

The `generate` command creates comprehensive Mintlify documentation from your .NET source code:

- **DocFX Integration**: Uses DocFX for superior type resolution and metadata extraction
- **MDX Generation**: Creates Mintlify-compatible MDX files from XML documentation
- **Navigation Structure**: Automatically generates docs.json with navigation hierarchy
- **Multi-Solution Support**: Can process multiple solutions into a single documentation site
- **Conceptual Documentation**: Supports both API reference and conceptual content

## Options

### Project Selection

#### `-p|--project <value>`
Specific project to generate documentation for.

**Default**: Processes all eligible projects in the solution
**Example**: `-p "MyCompany.Core"`

#### `-s|--solution <value>`
Solution file path.

**Default**: Auto-detected from current directory
**Example**: `-s "C:\Source\MyProject\MyProject.sln"`

#### `--exclude <patterns>`
Comma-separated list of project name patterns to exclude (supports wildcards).

**Examples:**
```bash
--exclude "*.Tests.*,*.Samples.*"
--exclude "*Test*,*Demo*"
```

#### `--single-project`
Force single-project mode even if solutions.json is detected.

**Usage**: When you have a solutions.json file but want to generate docs for just the current project.

### Output Configuration

#### `-o|--output <path>`
Output directory for generated documentation.

**Default**: `./docs`
**Examples:**
```bash
-o "./documentation"
-o "C:\MyProject\docs"
```

#### `-c|--clean`
Clean the api-reference directory before generating.

**Note**: Only cleans `api-reference/` to preserve conceptual and override content.

### Content Filtering

#### `--include-internal`
Include internal members in the documentation.

**Default**: Only public and protected members are included

#### `--namespace-filter <regex>`
Filter namespaces to include using regex pattern.

**Examples:**
```bash
--namespace-filter "^MyCompany\.Core"
--namespace-filter "MyProject\.(Business|Data)"
```

#### `--type-filter <regex>`
Filter types to include using regex pattern.

**Examples:**
```bash
--type-filter ".*Controller$"
--type-filter "(Manager|Service|Repository)$"
```

### Configuration Options

#### `--config`
Generate docs.json configuration file.

**Default**: `true`

#### `--no-config`
Skip generating docs.json configuration file.

#### `--config-only`
Only generate docs.json without generating MDX files.

**Use Case**: When you want to update navigation without regenerating all content.

### Build Options

#### `--force-build`
Force a rebuild instead of using existing binaries.

**Default**: Uses existing build outputs if available

#### `-v|--verbose`
Enable verbose output for detailed logging.

## Multi-Solution Documentation

### Automatic Detection

When `solutions.json` exists in the current directory, the command automatically switches to multi-solution mode:

```bash
# This will auto-detect solutions.json and process all configured solutions
dotnet easyaf mintlify generate
```

### solutions.json Configuration

Create a `solutions.json` file to configure multi-solution documentation:

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
      "path": "./src/MyFramework.Core",
      "outputPath": "core",
      "projects": ["*.csproj"],
      "exclude": ["*.Tests.*.csproj"],
      "navigation": {
        "inherit": true
      }
    },
    {
      "name": "Extensions",
      "tab": "Extensions",
      "source": "local", 
      "path": "./src/MyFramework.Extensions",
      "outputPath": "extensions",
      "projects": ["*.csproj"],
      "exclude": ["*.Tests.*.csproj"],
      "navigation": {
        "groups": [
          {
            "group": "Getting Started",
            "pages": ["index", "quickstart"]
          },
          {
            "group": "API Reference",
            "source": "api-reference"
          }
        ]
      }
    }
  ],
  "globalSettings": {
    "siteName": "My Framework Documentation",
    "theme": "mint",
    "colors": {
      "primary": "#0078d4",
      "dark": "#0056b3"
    },
    "logo": {
      "light": "/logo/light.svg",
      "dark": "/logo/dark.svg"
    },
    "favicon": "/favicon.ico"
  }
}
```

### Solution Source Types

#### Local Generation
```json
{
  "source": "local",
  "path": "./src/MyProject",
  "projects": ["*.csproj"],
  "exclude": ["*.Tests.*.csproj"]
}
```

#### External Documentation
```json
{
  "source": "external",
  "docsPath": "./external-docs",
  "mergeConfig": {
    "preserveStructure": true,
    "transformations": {
      "pathPrefix": "external"
    }
  }
}
```

#### Hybrid Approach
```json
{
  "source": "hybrid",
  "path": "./src/MyProject",
  "docsPath": "./existing-docs"
}
```

## Examples

### Basic Documentation Generation

```bash
# Generate docs for all projects in current solution
dotnet easyaf mintlify generate

# Generate with verbose output
dotnet easyaf mintlify generate -v

# Clean output before generating
dotnet easyaf mintlify generate --clean
```

### Specific Project Documentation

```bash
# Generate docs for a specific project
dotnet easyaf mintlify generate -p "MyCompany.Core"

# Generate with custom output directory
dotnet easyaf mintlify generate -p "MyCompany.Core" -o "./api-docs"
```

### Filtered Documentation

```bash
# Include only specific namespaces
dotnet easyaf mintlify generate --namespace-filter "^MyCompany\.(Core|Business)"

# Include internal members
dotnet easyaf mintlify generate --include-internal

# Exclude test projects
dotnet easyaf mintlify generate --exclude "*.Tests.*,*.Samples.*"
```

### Configuration Management

```bash
# Generate only docs.json configuration
dotnet easyaf mintlify generate --config-only

# Skip generating docs.json
dotnet easyaf mintlify generate --no-config

# Force rebuild of projects
dotnet easyaf mintlify generate --force-build
```

### Multi-Solution Examples

```bash
# Generate multi-solution docs (auto-detects solutions.json)
dotnet easyaf mintlify generate

# Force single-project mode even with solutions.json present
dotnet easyaf mintlify generate --single-project

# Multi-solution with verbose output
dotnet easyaf mintlify generate -v --clean
```

## Generated Output

### Directory Structure

```
docs/
├── docs.json                 # Mintlify configuration
├── api-reference/           # Auto-generated API docs
│   ├── namespace1/
│   │   ├── class1.mdx
│   │   └── class2.mdx
│   └── namespace2/
├── conceptual/              # AI-friendly templates
│   ├── namespace1/
│   │   ├── class1.md
│   │   └── class2.md
│   └── getting-started.md
└── overrides/               # Custom frontmatter
    ├── namespace1/
    │   ├── class1.yml
    │   └── class2.yml
    └── global.yml
```

### Multi-Solution Structure

```
docs/
├── docs.json                # Master configuration with tabs
├── core/                   # Core framework docs
│   └── api-reference/
├── extensions/             # Extensions docs  
│   └── api-reference/
└── shared/                 # Shared resources
    ├── logo/
    └── assets/
```

## Conceptual Documentation

### Override Files
Create `.yml` files in `overrides/` to customize frontmatter:

```yaml
# overrides/MyNamespace/MyClass.yml
uid: MyNamespace.MyClass
frontmatter:
  title: "Custom Title"
  description: "Custom description"
  tags: ["api", "core"]
conceptual:
  usageExamples: |
    ## Usage Examples
    
    Here's how to use MyClass:
    
    ```csharp
    var instance = new MyClass();
    instance.DoSomething();
    ```
```

### Conceptual Templates
AI-friendly templates are generated in `conceptual/` with helpful prompts:

```markdown
<!-- This file contains usage instructions for MyClass -->
<!-- AI: Please provide comprehensive usage examples and explanations -->

## Basic Usage

<!-- Provide basic usage examples for MyClass here -->

## Advanced Scenarios

<!-- Document advanced use cases and patterns -->
```

## Project Requirements

### XML Documentation
Ensure your projects generate XML documentation:

```xml
<PropertyGroup>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
</PropertyGroup>
```

### Build Output
Projects must be built before documentation generation:

```bash
dotnet build
dotnet easyaf mintlify generate
```

### Eligible Projects
The command automatically filters projects to include only:
- ✅ Projects with `GenerateDocumentationFile=true`
- ✅ Non-test projects (excludes `*Test*`, `*.Tests.*`)
- ✅ Non-template projects (excludes `*Template*`)
- ✅ Non-tool projects (excludes `*.Tools.*`)

## Troubleshooting

### "No eligible projects found"
**Cause**: No projects meet the eligibility criteria.
**Solution**: 
- Ensure projects have `<GenerateDocumentationFile>true</GenerateDocumentationFile>`
- Build projects: `dotnet build`
- Check project naming patterns don't match exclusion filters

### "DocFX metadata generation failed"
**Cause**: DocFX couldn't process the project assemblies.
**Solutions**:
- Run `dotnet build` to ensure projects compile successfully
- Check for missing dependencies: `dotnet restore`
- Verify target framework compatibility

### "solutions.json validation failed"
**Cause**: Invalid solutions.json configuration.
**Solutions**:
- Validate JSON syntax
- Check that all specified paths exist
- Ensure required fields are present (name, tab, outputPath)

### "Path not found" errors
**Cause**: Specified paths in solutions.json don't exist.
**Solutions**:
- Verify solution paths are correct and accessible
- Use absolute paths if relative paths are problematic
- Check file permissions

### Empty or missing navigation
**Cause**: No docs.json found for inheritance or navigation not configured.
**Solutions**:
- Create explicit navigation in solutions.json
- Ensure docs.json files exist in solution paths for inheritance
- Check that `inherit: true` is spelled correctly

## Performance Tips

### Large Solutions
For large solutions with many projects:
- Use `--exclude` to filter unnecessary projects
- Consider namespace filtering with `--namespace-filter`
- Use `--single-project` for faster iteration during development

### Incremental Updates
- Use `--config-only` to update navigation without regenerating MDX
- Avoid `--clean` during development to preserve unchanged files
- Use watch mode for real-time updates during development

## Integration with Mintlify

### Preview Documentation
```bash
# Install Mintlify CLI
npm install -g mintlify

# Preview documentation locally
cd docs
mintlify dev
```

### Deploy to Mintlify
```bash
# Deploy to Mintlify hosting
mintlify deploy
```

## See Also

- [mintlify init](./init.md) - Initialize documentation configuration
- [mintlify watch](./watch.md) - Watch for changes and auto-regenerate
- [Mintlify Documentation](https://mintlify.com/docs) - Official Mintlify docs