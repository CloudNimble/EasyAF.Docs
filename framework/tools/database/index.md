# Database Commands

Manage Entity Framework Core database scaffolding and EDMX file generation for EasyAF projects.

## Overview

The EasyAF database commands streamline the process of generating and maintaining Entity Framework models from existing databases:

- **Generate**: Create EDMX files from database schema using existing configuration
- **Refresh**: Update existing EDMX files with current database schema
- **Multi-Context Support**: Handle multiple DbContexts in a single solution
- **Configuration-Driven**: Uses .edmx.config files for consistent generation

## Commands

### [`generate`](./generate.md)
Generate EDMX files from database using existing .edmx.config configuration.

```bash
dotnet easyaf database generate [options]
```

### [`refresh`](./refresh.md)
Refresh existing EDMX files from the database using current schema.

```bash
dotnet easyaf database refresh [options]
```

## Quick Start

### Generate Database Entities

1. **Initialize project configuration:**
   ```bash
   dotnet easyaf init -c "Server=localhost;Database=MyApp;Trusted_Connection=true" -x "MyApp" -p SqlServer
   ```

2. **Generate EDMX from database:**
   ```bash
   dotnet easyaf database generate
   ```

3. **Generate code components:**
   ```bash
   dotnet easyaf code generate all
   ```

### Multi-Context Projects

```bash
# Generate all contexts
dotnet easyaf database generate

# Generate specific context
dotnet easyaf database generate -x "MyAppDbContext"

# Refresh all contexts
dotnet easyaf database refresh
```

## Configuration

### .edmx.config Files

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
  <IncludePrivateMembers>false</IncludePrivateMembers>
  <NoDataAnnotations>false</NoDataAnnotations>
  <NoPluralize>false</NoPluralize>
  <Tables>
    <Table>Users</Table>
    <Table>Products</Table>
    <Table>Orders</Table>
  </Tables>
  <ExcludeTables>
    <Table>__EFMigrationsHistory</Table>
    <Table>sysdiagrams</Table>
  </ExcludeTables>
</EntityFrameworkConfig>
```

### Configuration Elements

#### Required Elements
- **ContextName**: Name of the DbContext class (without "DbContext" suffix)
- **Provider**: Database provider (`SqlServer` or `PostgreSQL`)
- **ConnectionStringName**: Name of connection string in user secrets
- **Namespace**: Namespace for the DbContext class
- **ObjectsNamespace**: Namespace for entity classes

#### Optional Elements
- **OutputDirectory**: Directory for generated files (default: current)
- **IncludePrivateMembers**: Include internal members (default: false)
- **NoDataAnnotations**: Use Fluent API instead of data annotations (default: false)
- **NoPluralize**: Disable entity name pluralization (default: false)
- **Tables**: Specific tables to include (empty = all tables)
- **ExcludeTables**: Tables to exclude from generation

### Multiple Contexts

For solutions with multiple databases or contexts:

```
MyApp.Data/
├── MyApp.edmx.config           # Main application context
├── Identity.edmx.config        # Identity/auth context
├── Logging.edmx.config         # Logging context
└── Generated/
    ├── MyAppDbContext.cs
    ├── IdentityDbContext.cs
    └── LoggingDbContext.cs
```

## Project Structure

### Auto-Discovery

The database commands automatically discover:

1. **Data Project**: Projects ending with `.Data` or `Data`
2. **Solution File**: `.sln` files in current or parent directories
3. **Config Files**: `.edmx.config` files in the Data project
4. **User Secrets**: Connection strings stored via `dotnet user-secrets`

### Example Structure
```
MyCompany.MyApp/
├── MyCompany.MyApp.sln
├── MyCompany.MyApp.Data/
│   ├── MyApp.edmx.config
│   ├── MyAppDbContext.cs       # Generated
│   └── Entities/               # Generated entity classes
├── MyCompany.MyApp.Core/
│   └── Models/                 # Entity interfaces/base classes
└── MyCompany.MyApp.Business/
    └── Managers/               # Business logic
```

## Workflow Integration

### Initial Setup Workflow

1. **Project Initialization:**
   ```bash
   dotnet easyaf init -c "ConnectionString" -x "MyApp" -p SqlServer
   ```

2. **Database Generation:**
   ```bash
   dotnet easyaf database generate
   ```

3. **Code Generation:**
   ```bash
   dotnet easyaf code generate all
   ```

### Development Workflow

1. **Update Database Schema:**
   ```sql
   -- Make database changes
   ALTER TABLE Users ADD COLUMN LastLoginDate datetime2;
   ```

2. **Refresh Models:**
   ```bash
   dotnet easyaf database refresh
   ```

3. **Regenerate Code:**
   ```bash
   dotnet easyaf code generate all
   ```

### Watch Mode Integration

Use EDMX watch mode for real-time updates:

```bash
# Terminal 1: Watch for EDMX changes
dotnet easyaf edmx watch

# Terminal 2: Make database changes
# EDMX files will be automatically updated and code regenerated
```

## Supported Database Providers

### Microsoft SQL Server
```xml
<Provider>SqlServer</Provider>
```

**Features:**
- Full schema support
- Stored procedures and functions
- Views and indexes
- Custom data types

**Connection String Examples:**
```bash
# Windows Authentication
"Server=localhost;Database=MyApp;Trusted_Connection=true"

# SQL Server Authentication
"Server=localhost;Database=MyApp;User Id=myuser;Password=mypass"

# Azure SQL Database
"Server=myserver.database.windows.net;Database=MyApp;User Id=myuser;Password=mypass"
```

### PostgreSQL
```xml
<Provider>PostgreSQL</Provider>
```

**Features:**
- Schema support
- Custom types and enums
- Views and materialized views
- Array and JSON columns

**Connection String Examples:**
```bash
# Basic connection
"Host=localhost;Database=myapp;Username=postgres;Password=password"

# With specific port and schema
"Host=localhost;Port=5432;Database=myapp;Username=postgres;Password=password;SearchPath=public"

# SSL connection
"Host=myserver.com;Database=myapp;Username=user;Password=pass;SSL Mode=Require"
```

## Advanced Configuration

### Table Filtering

#### Include Specific Tables
```xml
<Tables>
  <Table>Users</Table>
  <Table>Products</Table>
  <Table>Orders</Table>
  <Table>OrderItems</Table>
</Tables>
```

#### Exclude System Tables
```xml
<ExcludeTables>
  <Table>__EFMigrationsHistory</Table>
  <Table>sysdiagrams</Table>
  <Table>AspNetUsers</Table>
  <Table>AspNetRoles</Table>
  <Table>AspNetUserRoles</Table>
</ExcludeTables>
```

### Schema Configuration

#### Use Fluent API
```xml
<NoDataAnnotations>true</NoDataAnnotations>
```

Generates:
```csharp
// Instead of data annotations
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
}

// Uses Fluent API in OnModelCreating
entity.Property(e => e.Name)
    .IsRequired()
    .HasMaxLength(100);
```

#### Disable Pluralization
```xml
<NoPluralize>true</NoPluralize>
```

Example:
- With pluralization: `Users` table → `User` entity
- Without pluralization: `Users` table → `Users` entity

### Namespace Organization

#### Separate Namespaces
```xml
<Namespace>MyCompany.MyApp.Data.Contexts</Namespace>
<ObjectsNamespace>MyCompany.MyApp.Core.Entities</ObjectsNamespace>
```

Generated:
```csharp
// DbContext in Data.Contexts namespace
namespace MyCompany.MyApp.Data.Contexts
{
    public partial class MyAppDbContext : DbContext
    {
        // ...
    }
}

// Entities in Core.Entities namespace
namespace MyCompany.MyApp.Core.Entities
{
    public partial class User
    {
        // ...
    }
}
```

## Error Handling

### Common Issues

#### "Data project not found"
**Cause**: No project with "Data" in the name found.
**Solution**: Ensure you have a project ending with `.Data` (e.g., `MyApp.Data.csproj`).

#### "No .edmx.config files found"
**Cause**: Missing configuration files.
**Solution**: Run `dotnet easyaf init` to create initial configuration.

#### "Connection string not found"
**Cause**: Connection string not set in user secrets.
**Solution**: 
```bash
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "your-connection-string"
```

#### "Database connection failed"
**Cause**: Invalid connection string or database not accessible.
**Solutions**:
- Verify connection string is correct
- Ensure database server is running
- Check firewall and network settings
- Verify user permissions

### Debugging

#### Verbose Output
```bash
dotnet easyaf database generate -v
dotnet easyaf database refresh -v
```

#### Test Connection
```bash
# Test connection string manually
sqlcmd -S "localhost" -d "MyApp" -E

# For PostgreSQL
psql -h localhost -d myapp -U postgres
```

## Best Practices

### Configuration Management
- Store connection strings in user secrets, not source control
- Use consistent naming conventions for contexts and entities
- Organize entities into logical namespaces

### Schema Design
- Use proper data types and constraints
- Include primary keys and foreign key relationships
- Add meaningful indexes for performance

### Version Control
- Include `.edmx.config` files in source control
- Exclude generated `.cs` files (regenerate from database)
- Use database migration scripts for schema changes

### Team Development
- Standardize connection string names across team
- Use shared database for consistent schema
- Document any manual customizations to generated code

## Performance Considerations

### Large Databases
- Use table filtering to include only needed tables
- Consider separate contexts for different bounded contexts
- Exclude large tables that don't need EF mapping

### Generation Speed
- Use existing build outputs when possible
- Consider incremental generation for large schemas
- Cache connection validation results

## Integration with EF Core

### Migration Support
The generated DbContexts work seamlessly with EF Core migrations:

```bash
# Add migration after schema changes
dotnet ef migrations add UpdateUserTable

# Update database
dotnet ef database update
```

### Code-First vs Database-First
EasyAF database commands support database-first development:
- Generate models from existing database
- Keep database as source of truth
- Use migrations for controlled schema evolution

## See Also

- [database generate](./generate.md) - Generate EDMX files from database
- [database refresh](./refresh.md) - Refresh existing EDMX files
- [init](../init.md) - Initialize project configuration
- [code generate](../code/generate.md) - Generate code from EDMX files