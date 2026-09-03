# APK Builder GitHub Action

Automate building **Release** or **Debug** APKs for Android and Compose Multiplatform repositories without requiring local Android SDK or Gradle dependencies.

This action acts as a universal cloud APK build server:
- 🚀 **Gradle Caching**: Integrates `gradle/actions/setup-gradle@v4` for fast, cached builds.
- 🧩 **Universal Project Detection**: Automatically detects Compose Multiplatform (`:composeApp`), standard Android (`:app`), and single-module projects.
- 📦 **Multi-APK Discovery**: Accurately discovers and standardizes all valid APK outputs (including ABI splits), discarding test and unaligned files.
- 🏷️ **GitHub Release Publishing**: Automatically creates GitHub Releases and attaches finished APKs as downloadable assets.
- 🌐 **Web App Support**: Auto-wraps Node.js Web Apps (React, Vue, Vite, etc.) into native Android APKs via [Capacitor](https://capacitorjs.com/).
- 🔒 **Private & Public Repos**: Full support for private repositories using GitHub tokens or Personal Access Tokens (PATs).

---

## Usage

### Option 1: Manual Workflow Dispatch (Fork & Run)

1. [Fork](https://github.com/Enternameet/apk-builder-action/fork) this repository on GitHub.
2. Go to the [Build Android APK Workflow](/../../actions/workflows/build-apk.yaml) and click **Run workflow**.
3. Fill in the parameters:
   - **Repository URL**: Git URL of the Android project (e.g. `https://github.com/Enternameet/komi-store`).
   - **Branch Name**: Branch to clone (optional, defaults to repository default).
   - **Publish to GitHub Releases**: Check to automatically create a release and attach the APK(s).
4. Run the workflow. When finished:
   - Generated APKs will appear as downloadable workflow artifacts under the **Summary** tab.
   - If enabled, a new **GitHub Release** with direct APK download links will be published on your repository.

---

### Option 2: Use in Your Own Workflow

Include this action in any GitHub workflow:

```yaml
name: Build and Release APK

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write # Required for publishing GitHub Releases
    steps:
      - name: Build Android APK
        uses: Enternameet/apk-builder-action@main
        with:
          repository: 'https://github.com/Enternameet/komi-store'
          branchName: 'fix/private-repo-downloads'
          taskName: 'auto' # Smart auto-detects :composeApp:assembleRelease, :app, etc.
          publishRelease: 'true'
          releasePrerelease: 'true'
          githubToken: ${{ secrets.GITHUB_TOKEN }}
```

To build a repository already checked out in your workflow (avoiding double-cloning):

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Build APK from current repo
        uses: Enternameet/apk-builder-action@main
        with:
          repository: '.'
          taskName: 'auto'
          uploadArtifact: 'true'
```

---

## Action Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `repository` | Git repository URL, or `.` / `local` to build the current checked-out repo | Yes | `https://github.com/android/sunflower` |
| `authToken` | Personal Access Token (PAT) or auth token for private repositories | No | `""` |
| `branchName` | Specific branch to clone | No | `""` (default branch) |
| `subdir` | Relative subdirectory path if project is nested | No | `""` |
| `jdkVersion` | OpenJDK version (`17` or `21`) | No | `21` |
| `taskName` | Gradle task (e.g. `auto`, `assembleDebug`, `assembleRelease`, `:composeApp:assembleDebug`) | No | `auto` |
| `uploadArtifact` | Upload APK artifact to GitHub Actions workflow run | No | `true` |
| `retentionDays` | Artifact retention in days | No | `7` |
| `publishRelease` | Publish generated APK(s) directly to a GitHub Release | No | `false` |
| `releaseTag` | Custom tag name for GitHub release | No | Auto-generated |
| `releaseTitle` | Custom title for GitHub release | No | Auto-generated |
| `releasePrerelease` | Mark release as pre-release (`true`/`false`) | No | `true` |
| `releaseDraft` | Mark release as draft (`true`/`false`) | No | `false` |
| `githubToken` | Token with `contents: write` permissions for release creation | No | `${{ github.token }}` |

---

## Action Outputs

| Output | Description |
|---|---|
| `apk-path` | File path to the primary generated APK |
| `apk-dir` | Directory path containing all generated APKs |
| `artifact-name` | Name of the uploaded workflow artifact |
| `release-url` | URL of the published GitHub release (if `publishRelease: true`) |
