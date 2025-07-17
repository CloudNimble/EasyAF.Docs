# EasyAF CLI Tools

The EasyAF CLI provides a comprehensive set of tools for managing EasyAF projects, from initial setup to documentation generation. This command-line interface streamlines development workflows and automates common tasks.

## Installation

The EasyAF CLI is available as a .NET Global Tool:

```bash
dotnet tool install --global CloudNimble.EasyAF.Tools
```

## Quick Start

1. **Initialize a new EasyAF project:**
   ```bash
   dotnet easyaf init -c "Server=localhost;Database=MyApp;Trusted_Connection=true" -x "AppDbContext" -p SqlServer
   ```

2. **Generate documentation:**
   ```bash
   dotnet easyaf mintlify generate
   ```

3. **Generate code:**
   ```bash
   dotnet easyaf code generate all
   ```

## Command Structure

The EasyAF CLI is organized into logical command groups:

### Root Commands
- [`init`](./init.md) - Initialize EasyAF project configuration
- [`setup`](./setup.md) - Set up local development environment  
- [`cleanup`](./cleanup.md) - Clean build artifacts

### Command Groups
- [`code`](./code/index.md) - C# code generation commands
- [`database`](./database/index.md) - Database scaffolding commands
- [`edmx`](./edmx/index.md) - EDMX file utilities
- [`mintlify`](./mintlify/index.md) - Documentation generation commands

## Features

### 🚀 **Project Initialization**
- Auto-configures EasyAF projects with proper structure
- Sets up user secrets and connection strings
- Configures analyzers and Directory.Build.props

### 🔧 **Code Generation** 
- Generates entities, managers, controllers, and API endpoints
- Supports SimpleMessageBus integration
- Customizable templates and namespaces

### 📊 **Database Integration**
- Entity Framework Core scaffolding
- EDMX file generation and management
- Multi-provider support (SQL Server, PostgreSQL)

### 📚 **Documentation**
- Mintlify documentation generation from XML comments
- Multi-solution documentation aggregation
- DocFX integration for superior type resolution

### 🔍 **File Watching**
- Real-time code generation on EDMX changes
- Automatic documentation regeneration
- Configurable change detection

## Getting Help

Each command includes built-in help. Use the `-h` or `--help` flag with any command:

```bash
dotnet easyaf --help
dotnet easyaf mintlify generate --help
dotnet easyaf code generate --help
```

## Configuration

EasyAF CLI commands automatically detect and work with:
- **Project Structure**: Auto-discovers .Data, .Business, .Api, and .Core projects
- **Solution Files**: Automatically finds .sln files in current or parent directories
- **User Secrets**: Securely stores connection strings using .NET user secrets
- **Directory.Build.props**: Configures MSBuild properties for consistent builds

## Common Workflows

### New Project Setup
```bash
# Initialize project with database scaffolding
dotnet easyaf init -c "ConnectionString" -x "MyDbContext" -p SqlServer

# Generate all code components
dotnet easyaf code generate all

# Create documentation
dotnet easyaf mintlify init -n "My Project"
dotnet easyaf mintlify generate
```

### Development Workflow
```bash
# Watch for EDMX changes and regenerate code
dotnet easyaf edmx watch

# In another terminal, watch for code changes and regenerate docs
dotnet easyaf mintlify watch
```

### Multi-Solution Documentation
```bash
# Create solutions.json configuration
dotnet easyaf mintlify init --use-solutions

# Generate combined documentation site
dotnet easyaf mintlify generate
```

## Next Steps

- [Initialize your first project](./init.md)
- [Learn about code generation](./code/index.md)
- [Set up documentation](./mintlify/index.md)
- [Explore database commands](./database/index.md)