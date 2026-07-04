# GitHub Actions Workflow for VLC

This repository includes a GitHub Actions workflow that automatically builds VLC for multiple platforms and creates releases with pre-built binaries.

## Features

- **Auto-trigger**: Automatically builds and creates releases when you push a tag starting with `v` (e.g., `v4.0.0`, `v4.0.1`)
- **Manual trigger**: Allows manual workflow dispatch with custom version and platform selection
- **Multi-platform builds**: Builds VLC for:
  - **Windows**: win32, win64, win64-arm
  - **macOS**: x86_64, arm64
  - **Linux**: x86_64, arm
  - **Android**: arm, arm64, x86, x86_64
  - **iOS/tvOS**: arm64, armv7, simulator x86_64, tvos, watchos
- **Auto-generated release notes**: Automatically generates release notes from merged pull requests
- **Artifact packaging**: Packages builds as zip, tar.gz, and DMG files
- **Release management**: Creates GitHub releases with all platform-specific binaries

## How to Use

### Automatic Release (Recommended)

1. **Create and push a tag**:
   ```bash
   git tag -a v4.0.0 -m "Release 4.0.0"
   git push origin v4.0.0
   ```

2. **Workflow will automatically**:
   - Build VLC for all platforms
   - Package artifacts
   - Create a GitHub release with auto-generated release notes
   - Upload all platform binaries

### Manual Trigger

1. **Go to GitHub Actions**:
   - Navigate to your repository's Actions tab
   - Select "Build and Release VLC" workflow
   - Click "Run workflow"

2. **Provide inputs**:
   - **Version**: Enter the version (e.g., `4.0.0`, `4.0.1`)
   - **Platforms**: Enter comma-separated platforms to build (default: `windows,macos,linux`)
     - Options: `windows`, `macos`, `linux`, `android`, `ios`, `tvos`

3. **Workflow will**:
   - Auto-generate the next patch version if not specified
   - Build selected platforms
   - Create a GitHub release

## Platform-Specific Notes

### Windows Builds
- Uses MinGW cross-compilation
- Requires MSYS2 and MinGW tools
- Produces executables and DLLs

### macOS Builds
- Uses Xcode and Homebrew
- Produces DMG disk images
- Supports both Intel and Apple Silicon

### Linux Builds
- Uses Meson build system
- Produces tar.gz archives
- Supports x86_64 and ARM architectures

### Android Builds
- Uses Android SDK and NDK
- Requires libvlcjni repository
- Produces zip packages

### iOS/tvOS Builds
- Uses Apple build scripts
- Produces zip packages
- Supports multiple architectures

## Workflow Structure

```
build-release.yml
├── generate-tag          # Generates version tag
├── build-windows         # Windows builds (win32, win64, win64-arm)
├── build-macos           # macOS builds (x86_64, arm64)
├── build-linux           # Linux builds (x86_64, arm)
├── build-android         # Android builds (arm, arm64, x86, x86_64)
├── build-apple-mobile    # iOS/tvOS builds
├── create-release        # Creates GitHub release
└── auto-tag              # Creates tag for manual triggers
```

## Required Permissions

The workflow requires the following permissions:
- `contents: write` - To create releases and tags
- `packages: write` - For package management

## Customization

### Modifying Build Platforms

Edit the matrix strategy in each build job to add or remove platforms:

```yaml
strategy:
  matrix:
    include:
      - arch: x86_64
        platform: win64
        triplet: x86_64-w64-mingw32
        shortarch: win64
```

### Changing Build Scripts

The workflow uses existing build scripts in `extras/package/`:
- Windows: `extras/package/win32/build.sh`
- macOS: `extras/package/macosx/build.sh`
- Apple mobile: `extras/package/apple/build.sh`

### Release Notes

Release notes are automatically generated from:
- Merged pull requests
- Contributors
- Full changelog link

## Troubleshooting

### Build Failures

1. **Check workflow logs** in GitHub Actions
2. **Verify dependencies** are installed correctly
3. **Check platform-specific requirements**

### Release Creation Issues

1. **Ensure proper permissions** for `GITHUB_TOKEN`
2. **Check tag format** (must start with `v`)
3. **Verify artifacts** are uploaded correctly

### Manual Trigger Issues

1. **Check version format** (should be semver)
2. **Verify platform names** (comma-separated)
3. **Ensure workflow has write permissions**

## Testing Locally

Use `act` to test workflows locally:

```bash
# Install act
brew install act

# List available workflows
act -l

# Run specific job
act -j generate-tag

# Run with custom event
act workflow_dispatch -a version=4.0.0
```

## Security Considerations

- Uses `GITHUB_TOKEN` for authentication
- Does not expose secrets in logs
- Follows GitHub Actions security best practices
- Uses pinned action versions for reliability