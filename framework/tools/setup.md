# dotnet easyaf setup

Set up local development environment for existing EasyAF projects by configuring user secrets and connection strings.

## Synopsis

```bash
dotnet easyaf setup -c <connection-string> [options]
```

## Description

The `setup` command configures an existing EasyAF project for local development:

- **User Secrets**: Stores connection strings securely in user secrets
- **Multi-Context Support**: Handles projects with multiple DbContexts
- **Auto-Discovery**: Finds and configures all .edmx.config files in the solution
- **Validation**: Tests connection strings before storing them
- **Dry Run**: Preview configuration changes without applying them

This command is ideal for:
- Setting up development environments on new machines
- Configuring local connection strings for team members
- Switching between different database environments (dev, staging, etc.)

## Required Options

### `-c|--connection-string <value>`
Local connection string to store in user secrets.

**Examples:**
```bash
# SQL Server with Windows Authentication
-c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true"

# SQL Server with SQL Authentication
-c "Server=localhost;Database=MyApp_Dev;User Id=dev;Password=dev123"

# PostgreSQL
-c "Host=localhost;Database=myapp_dev;Username=dev;Password=dev123"

# LocalDB
-c "Server=(localdb)\\mssqllocaldb;Database=MyApp_Dev;Trusted_Connection=true"
```

## Optional Options

### `-x|--context-name <value>`
DbContext class name to configure (without "DbContext" suffix).

**Default**: Configures all contexts found in the solution
**Examples:**
```bash
-x "MyApp"      # Configures MyAppDbContext only
-x "Identity"   # Configures IdentityDbContext only
```

### `--dry-run`
Show what would be configured without making changes.

**Usage**: Preview the configuration changes before applying them.
```bash
dotnet easyaf setup -c "connection-string" --dry-run
```

### `-s|--solution-folder <value>`
Solution directory path.

**Default**: Current directory
**Example**: `-s "C:\Source\MyProject"`

## How It Works

### Discovery Process

1. **Find Solution**: Locates .sln file in current or parent directories
2. **Find Data Project**: Discovers projects ending with `.Data` or `Data`
3. **Find Config Files**: Locates all `.edmx.config` files in the Data project
4. **Extract Settings**: Reads connection string names and context information
5. **Store Secrets**: Saves connection strings to user secrets for each project

### Configuration Sources

The command reads existing configuration from:
- `.edmx.config` files in the Data project
- `appsettings.json` files for connection string names
- `Directory.Build.props` for project structure

### User Secrets Integration

Connection strings are stored using the .NET user secrets system:
```bash
# Equivalent to running:
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "your-connection-string"
```

## Examples

### Basic Setup

```bash
# Configure all contexts with local connection string
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true"
```

### Specific Context

```bash
# Configure only the main application context
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true" -x "MyApp"
```

### Different Database per Context

```bash
# Configure main application database
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true" -x "MyApp"

# Configure separate identity database
dotnet easyaf setup -c "Server=localhost;Database=Identity_Dev;Trusted_Connection=true" -x "Identity"
```

### Preview Changes

```bash
# See what would be configured without making changes
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true" --dry-run
```

### PostgreSQL Setup

```bash
# Configure PostgreSQL connection
dotnet easyaf setup -c "Host=localhost;Database=myapp_dev;Username=postgres;Password=dev123"
```

### LocalDB Setup

```bash
# Configure SQL Server LocalDB
dotnet easyaf setup -c "Server=(localdb)\\mssqllocaldb;Database=MyApp_Dev;Trusted_Connection=true"
```

### Team Development

```bash
# Developer 1: Uses local SQL Server instance
dotnet easyaf setup -c "Server=dev-sql01;Database=MyApp_Dev;Trusted_Connection=true"

# Developer 2: Uses local PostgreSQL
dotnet easyaf setup -c "Host=localhost;Database=myapp_dev;Username=dev;Password=dev123"

# Developer 3: Uses LocalDB
dotnet easyaf setup -c "Server=(localdb)\\mssqllocaldb;Database=MyApp_Dev;Trusted_Connection=true"
```

## Multi-Context Projects

### Example Project Structure

```
MyCompany.MyApp/
├── MyCompany.MyApp.Data/
│   ├── MyApp.edmx.config           # Main application context
│   ├── Identity.edmx.config        # Identity context
│   └── Logging.edmx.config         # Logging context
└── MyCompany.MyApp.Api/
    └── appsettings.json
```

### Configuration Example

```bash
# Configure all contexts at once
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true"
```

This will:
1. Set `ConnectionStrings:DefaultConnection` for MyAppDbContext
2. Set `ConnectionStrings:IdentityConnection` for IdentityDbContext  
3. Set `ConnectionStrings:LoggingConnection` for LoggingDbContext

### Per-Context Configuration

```bash
# Configure each context with different databases
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true" -x "MyApp"
dotnet easyaf setup -c "Server=localhost;Database=Identity_Dev;Trusted_Connection=true" -x "Identity"
dotnet easyaf setup -c "Server=localhost;Database=Logging_Dev;Trusted_Connection=true" -x "Logging"
```

## Output Examples

### Successful Configuration

```
🔧 Setting up local development environment...
📁 Solution: MyCompany.MyApp.sln
📦 Data Project: MyCompany.MyApp.Data

📋 Found configuration files:
   - MyApp.edmx.config (MyAppDbContext)
   - Identity.edmx.config (IdentityDbContext)

🔗 Testing connection strings...
   ✅ MyAppDbContext: Connection successful
   ✅ IdentityDbContext: Connection successful

💾 Storing connection strings in user secrets...
   ✅ MyApp.Data: ConnectionStrings:DefaultConnection
   ✅ MyApp.Data: ConnectionStrings:IdentityConnection

✅ Development environment setup completed!

Next steps:
   1. Run 'dotnet easyaf database generate' to create EDMX files
   2. Run 'dotnet easyaf code generate all' to generate code components
```

### Dry Run Output

```
🔧 Setting up local development environment (DRY RUN)...
📁 Solution: MyCompany.MyApp.sln
📦 Data Project: MyCompany.MyApp.Data

📋 Found configuration files:
   - MyApp.edmx.config (MyAppDbContext)

🔗 Would test connection string:
   - Server=localhost;Database=MyApp_Dev;Trusted_Connection=true

💾 Would store in user secrets:
   - Project: MyCompany.MyApp.Data
   - Key: ConnectionStrings:DefaultConnection
   - Value: Server=localhost;Database=MyApp_Dev;***

🔍 DRY RUN: No changes were made. Remove --dry-run to apply configuration.
```

## Environment-Specific Setup

### Development Environment

```bash
# Local development with SQL Server
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true"
```

### Testing Environment

```bash
# Local testing with in-memory or test database
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Test;Trusted_Connection=true"
```

### Docker Environment

```bash
# SQL Server in Docker container
dotnet easyaf setup -c "Server=localhost,1433;Database=MyApp_Dev;User Id=sa;Password=YourPassword123"

# PostgreSQL in Docker container
dotnet easyaf setup -c "Host=localhost;Port=5432;Database=myapp_dev;Username=postgres;Password=password"
```

## User Secrets Management

### View Current Secrets

```bash
# List all user secrets for a project
dotnet user-secrets list --project MyCompany.MyApp.Data
```

### Manual Secret Management

```bash
# Set additional secrets manually
dotnet user-secrets set "ApiKeys:ExternalService" "your-api-key" --project MyCompany.MyApp.Data

# Remove secrets
dotnet user-secrets remove "ConnectionStrings:DefaultConnection" --project MyCompany.MyApp.Data

# Clear all secrets
dotnet user-secrets clear --project MyCompany.MyApp.Data
```

### Secrets File Location

User secrets are stored in:
- **Windows**: `%APPDATA%\Microsoft\UserSecrets\{user-secrets-id}\secrets.json`
- **macOS/Linux**: `~/.microsoft/usersecrets/{user-secrets-id}/secrets.json`

## Integration with Development Workflow

### New Team Member Onboarding

1. **Clone repository:**
   ```bash
   git clone https://github.com/company/myapp.git
   cd myapp
   ```

2. **Restore packages:**
   ```bash
   dotnet restore
   ```

3. **Setup local environment:**
   ```bash
   dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true"
   ```

4. **Generate database entities:**
   ```bash
   dotnet easyaf database generate
   ```

5. **Build and run:**
   ```bash
   dotnet build
   dotnet run --project MyCompany.MyApp.Api
   ```

### Environment Switching

```bash
# Switch to staging database
dotnet easyaf setup -c "Server=staging-db;Database=MyApp_Staging;User Id=app;Password=***"

# Switch back to local development
dotnet easyaf setup -c "Server=localhost;Database=MyApp_Dev;Trusted_Connection=true"
```

## Security Considerations

### Connection String Security
- Connection strings are stored in user secrets, not in source control
- Passwords are masked in console output (shown as ***)
- User secrets are encrypted on Windows using DPAPI

### Best Practices
- Use Windows Authentication when possible for local development
- Use environment-specific databases (don't share production data)
- Regularly rotate database passwords
- Use least-privilege database accounts

### Team Sharing
- Don't share user secrets files directly
- Use environment variables or external secret management for CI/CD
- Document connection string formats in team documentation

## Troubleshooting

### "No .edmx.config files found"
**Cause**: No configuration files exist in the Data project.
**Solution**: Run `dotnet easyaf init` first to create initial configuration.

### "Data project not found"
**Cause**: No project with "Data" in the name found.
**Solution**: Ensure you have a project ending with `.Data` (e.g., `MyApp.Data.csproj`).

### "Connection test failed"
**Cause**: Database connection could not be established.
**Solutions**:
- Verify connection string is correct
- Ensure database server is running
- Check firewall settings
- Verify user credentials and permissions

### "User secrets not set"
**Cause**: User secrets initialization failed.
**Solutions**:
- Ensure project has `<UserSecretsId>` in .csproj
- Check file permissions in user secrets directory
- Run with elevated permissions if necessary

### "Multiple contexts found, specify context name"
**Cause**: Multiple .edmx.config files found but no specific context specified.
**Solution**: Use `-x` option to specify which context to configure.

## See Also

- [init](./init.md) - Initialize new EasyAF project configuration
- [database generate](./database/generate.md) - Generate EDMX files from database
- [User Secrets in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets) - Official Microsoft documentation