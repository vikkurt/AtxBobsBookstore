# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0

## Summary

The transformation appears to have completed successfully. No build errors were detected across any of the three projects in the solution:

- `Bookstore.Domain`
- `Bookstore.Data`
- `Bookstore.Web`

## Validation and Testing

### 1. Restore Dependencies

Run the following command from the solution root to ensure all NuGet packages are restored correctly:

```bash
dotnet restore
```

Review the output for any warnings related to package compatibility or deprecated packages targeting older frameworks.

### 2. Build the Solution

Perform a full solution build to confirm the absence of errors in a clean build environment:

```bash
dotnet build --configuration Release
```

Address any warnings that surface during this step, particularly those related to nullable reference types, obsolete APIs, or platform compatibility.

### 3. Run Unit Tests

If the solution contains test projects, execute them to verify that existing functionality behaves as expected after the migration:

```bash
dotnet test --configuration Release --verbosity normal
```

Review test output carefully. Failing tests may indicate behavioral differences introduced by the migration to cross-platform .NET.

### 4. Verify Runtime Behavior

Run the web application locally to confirm it starts and operates correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Check the following areas manually:

- **Database connectivity**: Confirm that `Bookstore.Data` connects to the expected database. Connection strings may need to be updated in `appsettings.json` if they previously relied on Windows-specific constructs such as Integrated Security or named pipes.
- **Entity Framework migrations**: If the project uses Entity Framework, verify that migrations apply correctly by running:
  ```bash
  dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
  ```
- **Static assets and routing**: Navigate through the application to confirm pages load correctly and routing behaves as expected.

### 5. Review Platform-Specific Code

Even without build errors, certain APIs behave differently or are unavailable on non-Windows platforms. Audit the codebase for the following:

- Use of `System.Drawing` (GDI+), which has limited support outside Windows without additional packages such as `System.Drawing.Common`.
- Windows Registry access via `Microsoft.Win32.Registry`.
- Windows-specific authentication mechanisms such as NTLM or Kerberos.
- File path separators hardcoded as `\` rather than using `Path.Combine` or `Path.DirectorySeparatorChar`.

### 6. Review Configuration Files

Confirm that `appsettings.json` and any environment-specific variants (`appsettings.Production.json`, etc.) are correctly configured for the target environment. Pay particular attention to:

- Connection strings
- Logging providers
- Any paths or directories referenced in configuration

### 7. Target Framework Verification

Open each `.csproj` file and confirm the `<TargetFramework>` element is set to the intended version, for example:

```xml
<TargetFramework>net8.0</TargetFramework>
```

Ensure all three projects target a consistent and currently supported version of .NET.