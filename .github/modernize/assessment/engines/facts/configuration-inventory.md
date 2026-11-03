# Configuration & Externalized Settings Inventory

Configuration is supplied through application JSON files, ASP.NET Core environment conventions, launch profiles, and Docker build settings. No external configuration server or secret-store integration was identified.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| Application settings | JSON | `PhotoAlbum/appsettings.json` | Default connection, uploads, logging, admin username, allowed hosts |
| Development settings | JSON | `PhotoAlbum/appsettings.Development.json` | Development logging and detailed errors |
| Launch profiles | JSON | `PhotoAlbum/Properties/launchSettings.json` | Development environment and local HTTP/HTTPS ports |
| ASP.NET Core environment | Environment variables | Runtime | Standard `ASPNETCORE_ENVIRONMENT` and hierarchical configuration overrides |
| Dockerfile | Container build | `Dockerfile` | .NET 9 runtime/build images and port 8080 |
| Web.config | XML | `PhotoAlbum/Web.config` | IIS connection string entry |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | `dotnet build` default or `-c Debug` | Development build | No profile-specific dependencies identified |
| Release | `-c Release` | Optimized publish/container build | No profile-specific dependencies identified |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Development | `ASPNETCORE_ENVIRONMENT=Development` in launch profiles | `appsettings.json`, `appsettings.Development.json` | Detailed errors and more verbose framework/EF logging |
| Other environments | Set `ASPNETCORE_ENVIRONMENT` at runtime | `appsettings.json` plus environment-specific files if supplied | Standard ASP.NET Core configuration override behavior |
| Test | `IsTestEnvironment=true` configuration value | Test host configuration | Skips startup database migrations |

## Properties Inventory

### PhotoAlbum

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `ConnectionStrings:DefaultConnection` | LocalDB `PhotoAlbumDb`, trusted connection | All | `appsettings.json`; overridden through ASP.NET Core configuration |
| `FileUpload:MaxFileSizeBytes` | `10485760` | All | `appsettings.json` |
| `FileUpload:AllowedMimeTypes` | JPEG, PNG, GIF, WebP | All | `appsettings.json` |
| `FileUpload:MaxFilesPerUpload` | `10` | All | `appsettings.json` |
| `FileUpload:UploadPath` | `wwwroot/uploads` | All | `appsettings.json` |
| `Logging:LogLevel:Default` | `Information` | Default | `appsettings.json` |
| `Logging:LogLevel:Microsoft.AspNetCore` | `Warning` | Default | `appsettings.json` |
| `Logging:LogLevel:Default` | `Debug` | Development | `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft.AspNetCore` | `Information` | Development | `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft.EntityFrameworkCore` | `Information` | Development | `appsettings.Development.json` |
| `DetailedErrors` | Not set | Development | `appsettings.Development.json` |
| `Admin:Username` | `admin` | All | `appsettings.json`; can be overridden |
| `Admin:Password` | Not set; required for login | All | External ASP.NET Core configuration, such as `Admin__Password` environment variable or user secrets |
| `AllowedHosts` | `*` | All | `appsettings.json` |
| `IsTestEnvironment` | `false` | Test | Test-host configuration; controls startup migration behavior |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| PhotoAlbum | .NET 9; no explicit runtime flags identified | Not specified | Not specified |

The Dockerfile exposes port 8080; no CPU, memory, or scaling limits were identified.

## Startup Dependency Chain

1. The application creates `wwwroot/uploads` if it does not exist.
2. Unless `IsTestEnvironment` is enabled, startup creates a database scope and applies EF Core migrations.
3. If migration startup fails, the application logs the error and rethrows rather than starting in a partially initialized state.

No readiness probe or external-service wait mechanism was identified.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `Admin:Password` | Administrator credential | Not present in checked-in application settings; intended to be supplied externally |
| `ConnectionStrings:DefaultConnection` | Database connection | Trusted LocalDB connection; no password is present in the checked-in value |

### Secrets Provisioning Workflow

The login handler reads the administrator password from configuration and fails closed if it is absent. No Key Vault, Vault, AWS Secrets Manager, encryption provider, managed identity, or deployment-time secret provisioning workflow was identified. For deployments, the password can be supplied through an external ASP.NET Core configuration provider such as an environment variable or user secrets; no actual secret value is documented here.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| `IsTestEnvironment` | `false` | Application configuration; suppresses startup migrations for test runs |

No feature-flag framework or rollout controls were identified.

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| .NET target framework | net9.0 | `PhotoAlbum/PhotoAlbum.csproj` |
| ASP.NET Core | 9.0 | `Microsoft.NET.Sdk.Web` and Docker base image |
| Entity Framework Core | 9.0.9 | `PhotoAlbum/PhotoAlbum.csproj` |
| SixLabors.ImageSharp | 3.1.11 | `PhotoAlbum/PhotoAlbum.csproj` |
| .NET SDK/container build image | 9.0 | `Dockerfile` |
| .NET ASP.NET runtime image | 9.0 | `Dockerfile` |
