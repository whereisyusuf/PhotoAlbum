# Security Assessment Report

**Generated:** 2026-11-03T05:25:24.934Z

## Summary

| Metric | Count |
|--------|-------|
| Total Findings | 8 |
| CVE Vulnerabilities | 6 |
| CWE Vulnerabilities | 2 |
| Total Rules Assessed | 59 |
| Rules Passed | 57 |

### By Severity

| Severity | Count |
|----------|-------|
| mandatory | 4 |
| optional | 3 |
| potential | 1 |

## CVE Findings (Dependency Vulnerabilities)

### CVE-2026-106115: ImageSharp: TIFF CCITT T6 encoder writes beyond an undersized output buffer
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** PhotoAlbum/PhotoAlbum.csproj:16

[CVE-2026-106115](https://github.com/advisories/GHSA-jjfr-hcj7-qf5w): ImageSharp: TIFF CCITT T6 encoder writes beyond an undersized output buffer

Severity: HIGH

Affected dependency: SixLabors.ImageSharp 3.1.11, declared at PhotoAlbum/PhotoAlbum.csproj:16. Affected version range(s): >= 2.1.0, < 3.2.0 (patched in 3.2.0); >= 4.0.0, < 4.1.2 (patched in 4.1.2).

Recommended fix: Upgrade to SixLabors.ImageSharp 3.2.0 or later.

### CVE-2026-106116: ImageSharp: BigTIFF IFD count can keep a decoder thread in a non-progressing loop
- **Severity:** optional
- **Story Points:** 1
- **Files:** PhotoAlbum/PhotoAlbum.csproj:16

[CVE-2026-106116](https://github.com/advisories/GHSA-wmxv-xphr-5c9g): ImageSharp: BigTIFF IFD count can keep a decoder thread in a non-progressing loop

Severity: MEDIUM

Affected dependency: SixLabors.ImageSharp 3.1.11, declared at PhotoAlbum/PhotoAlbum.csproj:16. Affected version range(s): >= 2.0.0, < 3.2.0 (patched in 3.2.0); >= 4.0.0, < 4.1.2 (patched in 4.1.2).

Recommended fix: Upgrade to SixLabors.ImageSharp 3.2.0 or later.

### CVE-2026-106110: ImageSharp: TIFF CCITT T4 encoder can write past its compressed output buffer
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** PhotoAlbum/PhotoAlbum.csproj:16

[CVE-2026-106110](https://github.com/advisories/GHSA-j9gm-c75j-xc9q): ImageSharp: TIFF CCITT T4 encoder can write past its compressed output buffer

Severity: HIGH

Affected dependency: SixLabors.ImageSharp 3.1.11, declared at PhotoAlbum/PhotoAlbum.csproj:16. Affected version range(s): >= 2.0.0, < 3.2.0 (patched in 3.2.0); >= 4.0.0, < 4.1.2 (patched in 4.1.2).

Recommended fix: Upgrade to SixLabors.ImageSharp 3.2.0 or later.

### CVE-2026-106113: ImageSharp: HistogramEqualization uses an unvalidated luminance as an unchecked histogram index
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** PhotoAlbum/PhotoAlbum.csproj:16

[CVE-2026-106113](https://github.com/advisories/GHSA-j3p4-wp97-rph4): ImageSharp: HistogramEqualization uses an unvalidated luminance as an unchecked histogram index

Severity: HIGH

Affected dependency: SixLabors.ImageSharp 3.1.11, declared at PhotoAlbum/PhotoAlbum.csproj:16. Affected version range(s): >= 2.0.0, < 3.2.0 (patched in 3.2.0); >= 4.0.0, < 4.1.2 (patched in 4.1.2).

Recommended fix: Upgrade to SixLabors.ImageSharp 3.2.0 or later.

### CVE-2026-106114: ImageSharp: ICC CLUT parsing allocates from unvalidated channel and grid dimensions
- **Severity:** optional
- **Story Points:** 1
- **Files:** PhotoAlbum/PhotoAlbum.csproj:16

[CVE-2026-106114](https://github.com/advisories/GHSA-gwg2-r3hj-4w44): ImageSharp: ICC CLUT parsing allocates from unvalidated channel and grid dimensions

Severity: MEDIUM

Affected dependency: SixLabors.ImageSharp 3.1.11, declared at PhotoAlbum/PhotoAlbum.csproj:16. Affected version range(s): >= 4.0.0, < 4.1.2 (patched in 4.1.2); >= 1.0.0-beta0001, < 3.2.0 (patched in 3.2.0).

Recommended fix: Upgrade to SixLabors.ImageSharp 3.2.0 or later.

### CVE-2026-106118: ImageSharp: Tiled fax TIFF: tile buffer sized by TileWidth but fax decompressor writes scanlines of ImageWidth — heap OOB write
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** PhotoAlbum/PhotoAlbum.csproj:16

[CVE-2026-106118](https://github.com/advisories/GHSA-v76p-62qx-wwq2): ImageSharp: Tiled fax TIFF: tile buffer sized by TileWidth but fax decompressor writes scanlines of ImageWidth — heap OOB write

Severity: HIGH

Affected dependency: SixLabors.ImageSharp 3.1.11, declared at PhotoAlbum/PhotoAlbum.csproj:16. Affected version range(s): >= 3.0.0, < 3.2.0 (patched in 3.2.0); >= 4.0.0, < 4.1.1 (patched in 4.1.1).

Recommended fix: Upgrade to SixLabors.ImageSharp 3.2.0 or later.

## CWE Findings (Code-Level Vulnerabilities)

### CWE-778: Insufficient Logging
- **Category:** Credentials & Secrets
- **Severity:** potential
- **Story Points:** 3
- **Files:** PhotoAlbum/Pages/Login.cshtml.cs:52-57

LoginModel.OnPostAsync returns the same error page for failed username/password attempts without recording the authentication failure. Repeated failed login attempts are therefore not logged for monitoring or audit.

### CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')
- **Category:** Injection Attacks
- **Severity:** optional
- **Story Points:** 8
- **Files:** PhotoAlbum/wwwroot/js/upload.js:189-210, PhotoAlbum/Pages/Index.cshtml.cs:88-92

The upload handler returns a client-supplied filename in failedUploads.fileName (Index.cshtml.cs:88-92). upload.js interpolates photo.originalFileName into HTML attributes and element text in a template string inserted with insertAdjacentHTML (lines 189-210), and interpolates error values into a string assigned to innerHTML (lines 223-226). A crafted filename can therefore inject markup/script into pages shown to other users.
