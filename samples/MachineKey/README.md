# System.Web.Security.MachineKey Usage

The adapters expose the `System.Web.Security.MachineKey` APIs to protect and unprotect data. On ASP.NET Framework, these unify with the in-box versions as usual. However, on ASP.NET Core framework, it uses the new [Data Protection APIs].

## ASP.NET Framework Apps

In order to unprotect values from ASP.NET Core apps, .NET Framework apps must configure the [replace](https://learn.microsoft.com/aspnet/core/security/data-protection/compatibility/replacing-machinekey) the machine key implementations with data protection APIs. Once this is done

> NOTE: This will cause the framework application to be unable to unencrypt values previously encrypted. If needed, these values must be migrated in some way to use the data protection APIs

## ASP.NET Core Apps

In order to use the `MachineKey` APIs on ASP.NET Core, it must be opted into:

```csharp
var builder = WebApplication.CreateBuilder(args);

// This must be set up in a way to share keys and application name if multiple apps (i.e. a framework app as well)
// will need to be able to protect/unprotect values
builder.Services.AddDataProtection()
    .SetApplicationName(MachineKeyTest.AppName)
    .PersistKeysToFileSystem(new DirectoryInfo(Path.Combine(Path.GetTempPath(), "sharedkeys", MachineKeyTest.AppName)));

builder.Services.AddSystemWebAdapters()
    // This call by itself does not register any data protection but rather integrates it with the adapters
    .AddMachineKeyProtection();
```
