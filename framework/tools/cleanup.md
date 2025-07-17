# dotnet easyaf cleanup

Clean up build artifacts and lock files from the solution to free disk space and resolve build issues.

## Synopsis

```bash
dotnet easyaf cleanup [options]
```

## Description

The `cleanup` command recursively removes build artifacts and temporary files from your solution:

- **Build Artifacts**: Removes `bin` and `obj` directories
- **Test Results**: Cleans `TestResults` directories  
- **Lock Files**: Removes `packages.lock.json` files
- **Disk Space**: Reports disk space freed
- **Safety**: Comprehensive error handling and dry-run option

This command is useful for:
- Resolving build issues caused by corrupted artifacts
- Freeing disk space on development machines
- Preparing clean builds for CI/CD
- Troubleshooting package restore problems

## Options

### `-p|--path <value>`
Root directory to clean.

**Default**: Current directory
**Examples:**
```bash
-p "C:\Source\MyProject"
-p "../MyOtherProject"
-p "."
```

### `--dry-run`
Show what would be deleted without actually deleting files.

**Usage**: Preview the cleanup operation before executing it.
```bash
dotnet easyaf cleanup --dry-run
```

### `-q|--quiet`
Quiet mode with minimal output.

**Usage**: Suppress detailed file listings and show only summary information.
```bash
dotnet easyaf cleanup -q
```

## What Gets Cleaned

### Build Directories
- **bin**: Binary output directories
- **obj**: Intermediate object files
- **TestResults**: Test execution results

### Lock Files
- **packages.lock.json**: NuGet package lock files
- **project.assets.json**: MSBuild asset files (in obj directories)

### Preserved Files
The following are **NOT** deleted:
- Source code files (.cs, .csproj, .sln)
- Configuration files (appsettings.json, web.config)
- Documentation files (.md, .txt)
- User secrets and local settings
- Node modules and other non-.NET artifacts

## Examples

### Basic Cleanup

```bash
# Clean current directory and subdirectories
dotnet easyaf cleanup

# Clean with verbose output
dotnet easyaf cleanup -v
```

### Specific Directory

```bash
# Clean specific project directory
dotnet easyaf cleanup -p "C:\Source\MyProject"

# Clean parent directory
dotnet easyaf cleanup -p ".."
```

### Preview Mode

```bash
# See what would be deleted without deleting
dotnet easyaf cleanup --dry-run

# Preview specific directory
dotnet easyaf cleanup -p "C:\Source\MyProject" --dry-run
```

### Quiet Mode

```bash
# Minimal output, just summary
dotnet easyaf cleanup -q

# Quiet cleanup of specific path
dotnet easyaf cleanup -p "../MyProject" -q
```

### Large Solution Cleanup

```bash
# Clean entire solution tree
dotnet easyaf cleanup -p "C:\Source\MyLargeSolution"

# Preview cleanup of large solution
dotnet easyaf cleanup -p "C:\Source\MyLargeSolution" --dry-run
```

## Output Examples

### Normal Output

```
🧹 Cleaning build artifacts...
📁 Scanning: C:\Source\MyProject

🗂️  Found directories to clean:
   📁 MyProject.Core\bin (2.5 MB)
   📁 MyProject.Core\obj (1.8 MB)
   📁 MyProject.Data\bin (3.2 MB)
   📁 MyProject.Data\obj (2.1 MB)
   📁 MyProject.Api\bin (4.7 MB)
   📁 MyProject.Api\obj (3.3 MB)
   📁 TestResults (15.6 MB)

📄 Found lock files to clean:
   📄 MyProject.Core\packages.lock.json (12 KB)
   📄 MyProject.Data\packages.lock.json (8 KB)
   📄 MyProject.Api\packages.lock.json (15 KB)

🔥 Deleting directories...
   ✅ Deleted: MyProject.Core\bin
   ✅ Deleted: MyProject.Core\obj
   ✅ Deleted: MyProject.Data\bin
   ✅ Deleted: MyProject.Data\obj
   ✅ Deleted: MyProject.Api\bin
   ✅ Deleted: MyProject.Api\obj
   ✅ Deleted: TestResults

🔥 Deleting lock files...
   ✅ Deleted: MyProject.Core\packages.lock.json
   ✅ Deleted: MyProject.Data\packages.lock.json
   ✅ Deleted: MyProject.Api\packages.lock.json

✅ Cleanup completed successfully!

📊 Summary:
   🗂️  Directories cleaned: 7
   📄 Lock files removed: 3
   💾 Disk space freed: 33.2 MB
   ⏱️  Time taken: 1.2 seconds
```

### Dry Run Output

```
🧹 Cleaning build artifacts (DRY RUN)...
📁 Scanning: C:\Source\MyProject

🗂️  Would delete directories:
   📁 MyProject.Core\bin (2.5 MB)
   📁 MyProject.Core\obj (1.8 MB)
   📁 MyProject.Data\bin (3.2 MB)
   📁 MyProject.Data\obj (2.1 MB)
   📁 TestResults (15.6 MB)

📄 Would delete lock files:
   📄 MyProject.Core\packages.lock.json (12 KB)
   📄 MyProject.Data\packages.lock.json (8 KB)

📊 Would free: 25.1 MB of disk space

🔍 DRY RUN: No files were deleted. Remove --dry-run to perform cleanup.
```

### Quiet Mode Output

```
🧹 Cleaning build artifacts...
✅ Cleanup completed: 7 directories, 3 lock files, 33.2 MB freed
```

### No Files Found

```
🧹 Cleaning build artifacts...
📁 Scanning: C:\Source\MyProject

ℹ️  No build artifacts found to clean.
   The directory appears to already be clean.

✅ Cleanup completed successfully!
```

## Integration with Development Workflow

### Before Major Builds

```bash
# Clean before important builds
dotnet easyaf cleanup
dotnet restore
dotnet build --configuration Release
```

### Troubleshooting Build Issues

```bash
# Clean and rebuild when facing build errors
dotnet easyaf cleanup
dotnet clean
dotnet restore
dotnet build
```

### CI/CD Pipeline Integration

```yaml
# GitHub Actions example
- name: Clean Build Artifacts
  run: dotnet easyaf cleanup -q

- name: Restore Dependencies
  run: dotnet restore
  
- name: Build Solution
  run: dotnet build --configuration Release
```

### Disk Space Management

```bash
# Check what would be cleaned first
dotnet easyaf cleanup --dry-run

# Clean if significant space would be freed
dotnet easyaf cleanup
```

## Advanced Usage

### Multiple Solutions

```bash
# Clean multiple solution directories
dotnet easyaf cleanup -p "C:\Source\Solution1"
dotnet easyaf cleanup -p "C:\Source\Solution2"
dotnet easyaf cleanup -p "C:\Source\Solution3"
```

### Batch Processing

```bash
# PowerShell: Clean all solution directories
Get-ChildItem -Path "C:\Source" -Directory | ForEach-Object {
    Write-Host "Cleaning $($_.Name)..."
    dotnet easyaf cleanup -p $_.FullName -q
}
```

```bash
# Bash: Clean all solution directories
for dir in /source/*/; do
    echo "Cleaning $(basename "$dir")..."
    dotnet easyaf cleanup -p "$dir" -q
done
```

### Scheduled Cleanup

```bash
# Windows Task Scheduler command
dotnet easyaf cleanup -p "C:\Source\MyProjects" -q

# Cron job (Linux/macOS)
# Add to crontab: 0 2 * * 0 /usr/local/bin/dotnet easyaf cleanup -p "/source/projects" -q
```

## Safety Features

### Error Handling
- Continues processing even if individual deletions fail
- Reports errors for locked files or permission issues
- Provides detailed error messages for troubleshooting

### File Protection
- Only deletes known build artifact directories
- Skips files and directories that don't match expected patterns
- Never deletes source code or configuration files

### Rollback Protection
- No rollback capability (deletions are permanent)
- Use `--dry-run` to preview operations
- Consider backing up important data before cleanup

## Performance Considerations

### Large Solutions
- Processing time scales with number of projects
- Deletion speed depends on storage type (SSD vs HDD)
- Network drives may be significantly slower

### Parallel Processing
- Deletions are performed sequentially for safety
- Multiple cleanup commands can run in parallel on different directories
- Consider system I/O limits for large operations

## Common Scenarios

### Build Server Maintenance

```bash
# Daily cleanup of build servers
dotnet easyaf cleanup -p "C:\BuildAgent\work" -q
```

### Developer Machine Cleanup

```bash
# Weekly cleanup of development directories
dotnet easyaf cleanup -p "C:\Source" --dry-run
# Review output, then run without --dry-run
dotnet easyaf cleanup -p "C:\Source"
```

### Release Preparation

```bash
# Clean before creating release builds
dotnet easyaf cleanup
dotnet restore
dotnet build --configuration Release --no-restore
dotnet test --configuration Release --no-build
```

### Storage Optimization

```bash
# Find largest cleanup opportunities first
dotnet easyaf cleanup --dry-run | findstr "MB"
```

## Troubleshooting

### "Access Denied" Errors
**Cause**: Files are locked by running processes or insufficient permissions.
**Solutions**:
- Close Visual Studio and other development tools
- Stop running applications and services
- Run command prompt as administrator
- Check antivirus software isn't locking files

### "Directory Not Found"
**Cause**: Specified path doesn't exist.
**Solution**: Verify the path is correct and accessible.

### "Path Too Long" Errors
**Cause**: Windows path length limitations (260 characters).
**Solutions**:
- Use shorter directory paths
- Enable long path support in Windows 10/11
- Use PowerShell instead of Command Prompt

### Slow Performance
**Cause**: Large number of files or slow storage.
**Solutions**:
- Use SSD instead of traditional hard drives
- Exclude network drives from cleanup
- Run cleanup during off-peak hours

### Partial Cleanup
**Cause**: Some files couldn't be deleted due to locks or permissions.
**Solution**: Review error messages and address specific file locks.

## Integration with Other Commands

### After Database Changes

```bash
# Clean after major database schema changes
dotnet easyaf cleanup
dotnet easyaf database refresh
dotnet easyaf code generate all
dotnet build
```

### Before Documentation Generation

```bash
# Clean before generating documentation
dotnet easyaf cleanup
dotnet build --configuration Release
dotnet easyaf mintlify generate
```

## See Also

- [init](./init.md) - Initialize new EasyAF project
- [setup](./setup.md) - Set up development environment  
- [code generate](./code/generate.md) - Generate code components