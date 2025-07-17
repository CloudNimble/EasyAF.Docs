# dotnet easyaf init

Initialize EasyAF project configuration including database scaffolding, project types, and analyzer setup.

## Synopsis

```bash
dotnet easyaf init -c <connection-string> -x <context-name> -p <provider> [options]
```

## Description

The `init` command sets up a new EasyAF project with all necessary configuration:

- **Database Scaffolding**: Configures Entity Framework Core scaffolding
- **Project Structure**: Auto-detects and configures .Data, .Business, .Api, and .Core projects
- **User Secrets**: Securely stores connection strings
- **Directory.Build.props**: Sets up MSBuild properties and analyzers
- **Code Generation**: Prepares project for EasyAF code generation

## Required Options

### `-c|--connection-string <value>`
Connection string source or actual connection string.

**Examples:**
```bash
# Direct connection string
-c "Server=localhost;Database=MyApp;Trusted_Connection=true"

# Connection string name from appsettings.json
-c "DefaultConnection"

# PostgreSQL connection string  
-c "Host=localhost;Database=myapp;Username=postgres;Password=password"
```

### `-x|--context-name <value>`
DbContext class name (without "DbContext" suffix).

**Examples:**
```bash
-x "App"           # Creates AppDbContext
-x "Northwind"     # Creates NorthwindDbContext
-x "MyCompany"     # Creates MyCompanyDbContext
```

### `-p|--provider <value>`
Database provider to use.

**Supported Values:**
- `SqlServer` - Microsoft SQL Server
- `PostgreSQL` - PostgreSQL

**Examples:**
```bash
-p SqlServer
-p PostgreSQL
```

## Optional Options

### `--dbcontext-namespace <value>`
Namespace for the generated DbContext class.

**Default**: Auto-detected from project structure
**Example**: `--dbcontext-namespace "MyCompany.Data"`

### `-e|--exclude-tables <value>`
Comma-separated list of table names to exclude from scaffolding.

**Examples:**
```bash
-e "AspNetUsers,AspNetRoles"
-e "__EFMigrationsHistory,sysdiagrams"
```

### `--no-data-annotations`
Use Fluent API instead of data annotations for entity configuration.

**Default**: Uses data annotations
**Example**: `--no-data-annotations`

### `--no-pluralizer`
Disable pluralization of entity names.

**Default**: Pluralization enabled (e.g., "Users" table → "User" entity)
**Example**: `--no-pluralizer`

### `--objects-namespace <value>`
Namespace for generated entity objects.

**Default**: Auto-detected from project structure
**Example**: `--objects-namespace "MyCompany.Core.Entities"`

### `-s|--solution-folder <value>`
Solution directory path.

**Default**: Current directory
**Example**: `-s "C:\Source\MyProject"`

### `-t|--tables <value>`
Comma-separated list of specific table names to include (includes only these tables).

**Examples:**
```bash
-t "Users,Products,Orders"
-t "Customer,Invoice"
```

### `--simplemessagebus-project <value>`
Name of SimpleMessageBus project to create.

**Example**: `--simplemessagebus-project "MyCompany.Messages"`

## Examples

### Basic SQL Server Setup
```bash
dotnet easyaf init \
  -c "Server=localhost;Database=Northwind;Trusted_Connection=true" \
  -x "Northwind" \
  -p SqlServer
```

### PostgreSQL with Custom Namespaces
```bash
dotnet easyaf init \
  -c "Host=localhost;Database=ecommerce;Username=dev;Password=dev123" \
  -x "ECommerce" \
  -p PostgreSQL \
  --dbcontext-namespace "MyCompany.Data.Contexts" \
  --objects-namespace "MyCompany.Core.Entities"
```

### Exclude System Tables
```bash
dotnet easyaf init \
  -c "DefaultConnection" \
  -x "App" \
  -p SqlServer \
  -e "AspNetUsers,AspNetRoles,__EFMigrationsHistory,sysdiagrams"
```

### Include Specific Tables Only
```bash
dotnet easyaf init \
  -c "Server=localhost;Database=CRM;Trusted_Connection=true" \
  -x "CRM" \
  -p SqlServer \
  -t "Customers,Leads,Opportunities,Activities"
```

### With SimpleMessageBus Integration
```bash
dotnet easyaf init \
  -c "DefaultConnection" \
  -x "MyApp" \
  -p SqlServer \
  --simplemessagebus-project "MyApp.Messages"
```

### Use Fluent API Configuration
```bash
dotnet easyaf init \
  -c "Server=localhost;Database=MyApp;Trusted_Connection=true" \
  -x "MyApp" \
  -p SqlServer \
  --no-data-annotations \
  --no-pluralizer
```

## What Gets Created

### Directory.Build.props
The command creates or updates `Directory.Build.props` with:
- EasyAF analyzer packages
- Global MSBuild properties
- Project type configurations

### User Secrets
Connection strings are stored securely using .NET user secrets:
```bash
# Connection string stored as:
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "your-connection-string"
```

### .edmx.config Files
Configuration files for database scaffolding are created in the .Data project:
```xml
<?xml version="1.0" encoding="utf-8"?>
<EntityFrameworkConfig>
  <ContextName>NorthwindDbContext</ContextName>
  <Provider>SqlServer</Provider>
  <!-- Additional configuration -->
</EntityFrameworkConfig>
```

### Project Structure Detection
The command auto-detects projects following these patterns:
- **Data Project**: `*.Data.csproj` or `*Data.csproj`
- **Business Project**: `*.Business.csproj` or `*Business.csproj`
- **API Project**: `*.Api.csproj` or `*Api.csproj`
- **Core Project**: `*.Core.csproj` or `*Core.csproj`

## Next Steps

After initialization, you can:

1. **Generate database entities:**
   ```bash
   dotnet easyaf database generate
   ```

2. **Generate all code components:**
   ```bash
   dotnet easyaf code generate all
   ```

3. **Set up documentation:**
   ```bash
   dotnet easyaf mintlify init -n "My Project"
   ```

## Troubleshooting

### "No .Data project found"
Ensure your solution has a project with "Data" in the name (e.g., `MyApp.Data.csproj`).

### "Connection string test failed"
- Verify the connection string is correct
- Ensure the database server is running and accessible
- Check firewall settings and network connectivity

### "Provider not supported"
Currently supported providers:
- SqlServer (Microsoft.EntityFrameworkCore.SqlServer)
- PostgreSQL (Npgsql.EntityFrameworkCore.PostgreSQL)

### "Tables not found"
- Verify the database exists and has tables
- Check that the user has appropriate permissions
- Ensure table names are spelled correctly in `-t` or `-e` options

## See Also

- [setup](./setup.md) - Set up local development environment
- [database generate](./database/generate.md) - Generate EDMX files
- [code generate](./code/generate.md) - Generate code components