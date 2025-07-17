# Code Generation Commands

Automatically generate C# code components for EasyAF projects, including entities, managers, controllers, and API endpoints.

## Overview

The EasyAF code generation system creates comprehensive application layers from your Entity Framework models:

- **Entities**: Core domain models with proper inheritance
- **Managers**: Business logic layer with dependency injection
- **Controllers**: REST API endpoints with full CRUD operations
- **Data Layer**: DbContext partials and view configurations
- **SimpleMessageBus**: Event messages for entity changes

## Commands

### [`generate`](./generate.md)
Generate code components for EasyAF projects.

```bash
dotnet easyaf code generate <component> [options]
```

## Supported Components

### `core`
Generates entity classes in the .Core project.

**Features:**
- Inherits from EasyAF base classes
- Proper property declarations
- Validation attributes
- Navigation properties

### `business` 
Generates manager classes and dependency injection configuration.

**Features:**
- Manager classes with CRUD operations
- Dependency injection setup
- Interface implementations
- Service registrations

### `data`
Generates DbContext partials and view configurations.

**Features:**
- DbContext partial classes
- Entity configurations
- Database view mappings
- Connection string management

### `api`
Generates REST API controllers and supporting infrastructure.

**Features:**
- CRUD controllers with proper routing
- Model builders and DTOs
- Authorization policies
- API documentation attributes

### `simplemessagebus`
Generates message classes for entity change events.

**Features:**
- Created/Updated/Deleted message classes
- Event payload definitions
- Message bus integration
- Serialization support

### `all`
Generates all components in the correct dependency order.

## Quick Start

### Generate All Components
```bash
# Generate all code components
dotnet easyaf code generate all

# Generate with verbose output
dotnet easyaf code generate all -v
```

### Generate Specific Components
```bash
# Generate only entity classes
dotnet easyaf code generate core

# Generate business layer
dotnet easyaf code generate business

# Generate API controllers
dotnet easyaf code generate api
```

## Project Structure Requirements

The code generation system expects projects following these naming patterns:

### Required Projects
- **Data Project**: `*.Data` or `*Data` (contains DbContext and EDMX)
- **Core Project**: `*.Core` or `*Core` (contains entity classes)

### Optional Projects
- **Business Project**: `*.Business` or `*Business` (contains manager classes)
- **API Project**: `*.Api` or `*Api` (contains controllers)
- **SimpleMessageBus Project**: `*.SimpleMessageBus`, `*.MessageBus`, or `*.EventBus`

### Example Structure
```
MyCompany.MyApp/
├── MyCompany.MyApp.Core/           # Entity classes
├── MyCompany.MyApp.Data/           # DbContext, EDMX files
├── MyCompany.MyApp.Business/       # Manager classes
├── MyCompany.MyApp.Api/            # Controllers, DTOs
└── MyCompany.MyApp.Messages/       # Message bus events
```

## Configuration

### Directory.Build.props
The code generation system uses MSBuild properties defined in `Directory.Build.props`:

```xml
<Project>
  <PropertyGroup>
    <!-- Project type configuration -->
    <EasyAFProjectType Condition="$(MSBuildProjectName.EndsWith('.Core'))">Core</EasyAFProjectType>
    <EasyAFProjectType Condition="$(MSBuildProjectName.EndsWith('.Data'))">Data</EasyAFProjectType>
    <EasyAFProjectType Condition="$(MSBuildProjectName.EndsWith('.Business'))">Business</EasyAFProjectType>
    <EasyAFProjectType Condition="$(MSBuildProjectName.EndsWith('.Api'))">Api</EasyAFProjectType>
    
    <!-- Code generation settings -->
    <EasyAFApiInheritance>CloudNimble.EasyAF.Http.OData.RestierController</EasyAFApiInheritance>
    <EasyAFApiAdditionalUsings>using Microsoft.AspNet.OData;</EasyAFApiAdditionalUsings>
  </PropertyGroup>
</Project>
```

### EDMX Configuration
Each DbContext requires an `.edmx.config` file in the Data project:

```xml
<?xml version="1.0" encoding="utf-8"?>
<EntityFrameworkConfig>
  <ContextName>MyAppDbContext</ContextName>
  <Provider>SqlServer</Provider>
  <ConnectionStringName>DefaultConnection</ConnectionStringName>
  <OutputDirectory>.</OutputDirectory>
  <Namespace>MyCompany.MyApp.Data</Namespace>
  <ObjectsNamespace>MyCompany.MyApp.Core</ObjectsNamespace>
</EntityFrameworkConfig>
```

## Generated Code Examples

### Entity Class (Core)
```csharp
using CloudNimble.EasyAF.Core;
using System;
using System.ComponentModel.DataAnnotations;

namespace MyCompany.MyApp.Core
{
    /// <summary>
    /// Represents a user in the system.
    /// </summary>
    public partial class User : EasyAFItem
    {
        /// <summary>
        /// Gets or sets the user's email address.
        /// </summary>
        [Required]
        [EmailAddress]
        public string Email { get; set; }
        
        /// <summary>
        /// Gets or sets the user's display name.
        /// </summary>
        [Required]
        public string DisplayName { get; set; }
    }
}
```

### Manager Class (Business)
```csharp
using CloudNimble.EasyAF.Business;
using MyCompany.MyApp.Core;
using MyCompany.MyApp.Data;

namespace MyCompany.MyApp.Business
{
    /// <summary>
    /// Manages operations for User entities.
    /// </summary>
    public class UserManager : EasyAFManager<User, MyAppDbContext>
    {
        /// <summary>
        /// Initializes a new instance of the UserManager class.
        /// </summary>
        public UserManager(MyAppDbContext context) : base(context)
        {
        }
        
        // Custom business logic methods can be added here
    }
}
```

### Controller Class (API)
```csharp
using CloudNimble.EasyAF.Http.OData;
using Microsoft.AspNet.OData;
using MyCompany.MyApp.Business;
using MyCompany.MyApp.Core;

namespace MyCompany.MyApp.Api.Controllers
{
    /// <summary>
    /// REST API controller for User entities.
    /// </summary>
    public class UsersController : RestierController<User, UserManager>
    {
        /// <summary>
        /// Initializes a new instance of the UsersController class.
        /// </summary>
        public UsersController(UserManager manager) : base(manager)
        {
        }
    }
}
```

### Message Class (SimpleMessageBus)
```csharp
using CloudNimble.SimpleMessageBus;
using MyCompany.MyApp.Core;

namespace MyCompany.MyApp.Messages
{
    /// <summary>
    /// Message sent when a User is created.
    /// </summary>
    public class UserCreatedMessage : MessageBase<User>
    {
        /// <summary>
        /// Initializes a new instance of the UserCreatedMessage class.
        /// </summary>
        public UserCreatedMessage() : base()
        {
        }
        
        /// <summary>
        /// Initializes a new instance of the UserCreatedMessage class.
        /// </summary>
        /// <param name="payload">The created user.</param>
        public UserCreatedMessage(User payload) : base(payload)
        {
        }
    }
}
```

## Customization

### Custom Base Classes
Configure custom inheritance in `Directory.Build.props`:

```xml
<PropertyGroup>
  <!-- Use custom API base class -->
  <EasyAFApiInheritance>MyCompany.Api.BaseController</EasyAFApiInheritance>
  
  <!-- Add custom using statements -->
  <EasyAFApiAdditionalUsings>using MyCompany.Common;</EasyAFApiAdditionalUsings>
</PropertyGroup>
```

### Exclude Tables from API
Use the `-notpublic` option to exclude tables from public API generation:

```bash
dotnet easyaf code generate api -notpublic "InternalLogs,SystemConfiguration"
```

### Custom Templates
The code generation system supports custom templates. Place custom templates in:
- `.easyaf/templates/` in your solution root
- Templates follow the same naming convention as built-in templates

## File Management

### Cleanup of Obsolete Files
The code generation system automatically:
- Removes generated files for deleted entities
- Updates file headers with generation timestamps
- Preserves manually created files (checks for generation markers)

### Generation Markers
Generated files include markers to identify them:

```csharp
// <auto-generated>
//     This code was generated by EasyAF.
//     Changes to this file may cause incorrect behavior and will be lost if
//     the code is regenerated.
// </auto-generated>
```

### Partial Classes
Generated classes are partial to allow customization:

```csharp
// Generated file: User.generated.cs
public partial class User : EasyAFItem
{
    // Generated properties
}

// Custom file: User.cs  
public partial class User
{
    // Custom properties and methods
}
```

## Best Practices

### Entity Framework Models
- Use proper data annotations for validation
- Include navigation properties for relationships
- Follow consistent naming conventions

### Business Logic
- Add custom business methods to partial classes
- Implement validation logic in manager classes
- Use dependency injection for external services

### API Design
- Follow REST conventions for controller actions
- Use proper HTTP status codes
- Document APIs with XML comments

### Testing
- Generate code before running tests
- Mock manager classes for unit testing
- Use in-memory databases for integration tests

## Integration

### Build Process
Integrate code generation into your build process:

```xml
<!-- In your .csproj file -->
<Target Name="GenerateCode" BeforeTargets="BeforeBuild">
  <Exec Command="dotnet easyaf code generate all" />
</Target>
```

### CI/CD Pipeline
```yaml
# GitHub Actions example
- name: Generate EasyAF Code
  run: dotnet easyaf code generate all
  
- name: Build Solution
  run: dotnet build
```

## Troubleshooting

### "No EDMX files found"
- Ensure .edmx files exist in the Data project
- Check that .edmx.config files are properly configured
- Verify DbContext has been generated

### "Project not found"
- Check project naming follows conventions (*.Core, *.Data, etc.)
- Ensure projects exist and are buildable
- Verify solution structure is correct

### "Build errors after generation"
- Check for missing NuGet packages
- Verify namespace references are correct
- Ensure base classes are available

### "Duplicate type definitions"
- Remove old manually created files that conflict with generated ones
- Check for naming conflicts between entities
- Verify partial class declarations are correct

## See Also

- [code generate](./generate.md) - Detailed generate command documentation
- [init](../init.md) - Initialize EasyAF project configuration
- [database generate](../database/generate.md) - Generate EDMX files from database