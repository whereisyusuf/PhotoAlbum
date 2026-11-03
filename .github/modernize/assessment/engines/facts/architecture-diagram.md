# Architecture Diagram

PhotoAlbum is a single web application with a browser-facing Razor Pages layer, photo operations, persistence, and local image-file storage.

## Application Architecture

```mermaid
flowchart TD
    subgraph Client["Client Layer"]
        Browser["Web Browser"]
    end
    subgraph App["Application Layer - ASP.NET Core 9"]
        Pages["Razor Pages"]
        Auth["Cookie Authentication"]
        Service["Photo Service"]
        Images["ImageSharp Processing"]
    end
    subgraph Data["Data and File Storage"]
        EF["Entity Framework Core 9"]
        DB[("SQL Server LocalDB")]
        Files[("wwwroot/uploads")]
    end

    Browser -->|"HTTP and HTTPS requests"| Pages
    Pages -->|"authentication checks"| Auth
    Pages -->|"photo operations"| Service
    Service -->|"detect and validate image content"| Images
    Service -->|"photo metadata operations"| EF
    EF -->|"SQL queries"| DB
    Service -->|"save and delete image files"| Files
    Pages -->|"serve image by photo ID"| Files
```

### Technology Stack Summary

| Layer | Technology | Version | Purpose |
|---|---|---:|---|
| Presentation | ASP.NET Core Razor Pages | 9.0 | Server-rendered gallery, details, login, and upload interaction |
| Business logic | C# / .NET | net9.0 | Photo validation, metadata, and file operations |
| Image processing | SixLabors.ImageSharp | 3.1.11 | Detect raster image formats and dimensions |
| Data access | Entity Framework Core | 9.0.9 | Persist and query photo metadata |
| Database | SQL Server LocalDB | configured | Store photo metadata |
| File storage | Local filesystem | N/A | Store uploaded images under `wwwroot/uploads` |

### Data Storage & External Services

Photo metadata is stored in SQL Server LocalDB, while image bytes are stored in the application's local uploads directory. No external service, message broker, or cache is configured.

### Key Architectural Decisions

- Razor Pages invoke `IPhotoService`, which encapsulates upload, retrieval, and deletion operations.
- EF Core persists metadata separately from filesystem image content; upload rolls back the file if metadata persistence fails.
- Cookie authentication protects destructive photo deletion; gallery browsing and uploads are otherwise served by the same application.

## Component Relationships

```mermaid
flowchart LR
    subgraph Presentation["Presentation"]
        Index["IndexModel"]
        Detail["DetailModel"]
        PhotoFile["PhotoFileModel"]
        Login["LoginModel"]
    end
    subgraph Business["Business Logic"]
        Interface["IPhotoService"]
        PhotoService["PhotoService"]
        ImageSharp["ImageSharp"]
    end
    subgraph DataAccess["Data Access"]
        Context["PhotoAlbumContext"]
        Photo["Photo entity"]
        Database[("SQL Server LocalDB")]
        Uploads[("Uploads directory")]
    end
    subgraph CrossCutting["Cross-Cutting"]
        CookieAuth["Cookie authentication"]
    end

    Index -->|"uses"| Interface
    Detail -->|"uses"| Interface
    PhotoFile -->|"uses"| Interface
    Login -->|"issues sign-in"| CookieAuth
    Detail -.->|"checks authentication"| CookieAuth
    Interface -.->|"implemented by"| PhotoService
    PhotoService -->|"validates image content"| ImageSharp
    PhotoService -->|"queries and saves"| Context
    PhotoService -->|"writes and deletes"| Uploads
    PhotoFile -->|"reads image bytes"| Uploads
    Context -->|"maps"| Photo
    Context -->|"persists metadata"| Database
```

### Component Inventory

| Component | Layer | Type | Responsibility |
|---|---|---|---|
| IndexModel | Presentation | Razor Page model | Lists photos and handles upload requests |
| DetailModel | Presentation | Razor Page model | Displays a photo and handles authenticated deletion |
| PhotoFileModel | Presentation | Razor Page model | Retrieves and serves an image by photo ID |
| LoginModel | Presentation | Razor Page model | Authenticates the configured administrator |
| IPhotoService | Business Logic | Service interface | Defines photo retrieval, upload, and deletion operations |
| PhotoService | Business Logic | Service | Validates images and coordinates file and metadata persistence |
| PhotoAlbumContext | Data Access | EF Core DbContext | Maps and queries photo metadata |
| Photo | Data Access | Entity | Represents photo metadata stored in the database |
