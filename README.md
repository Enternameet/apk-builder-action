# APK Builder GitHub Action

I've often wanted to test an app from an Android repository, but the developers didn't provide a downloadable APK.
While we could always clone the source code and build it ourselves, that often means downloading gigabytes of dependencies for what's typically a one-time use.

This GitHub action completely automates the _release_ or _debug_ APK building process, similar to a build server.

## Usage

### Option 1: Manual Workflow Dispatch (Fork & Run)

1. [Fork](https://github.com/ni554n/apk-builder-action/fork) this repo on GitHub
2. Go to the [Build Android APK Workflow](/../../actions/workflows/build-apk.yaml) and select `Run workflow` dropdown menu
3. Enter the Git repository URL of the Android project
4. (Optional) Provide a **Personal Access Token (PAT)** or Auth Token if the repository is private
5. Run the workflow and wait
6. If the build is successful, the `apk-archive` artifact will appear at the bottom of the **Summary** tab (refresh the page if it does not). If the build fails, check the **build** logs under the **Jobs** section to determine the cause.

### Option 2: Use as a GitHub Action in Your Workflow

Include this action in any GitHub workflow:

```yaml
jobs:
  build-apk:
    runs-on: ubuntu-latest
    steps:
      - name: Build Android APK
        uses: ni554n/apk-builder-action@v1
        with:
          repository: 'https://github.com/your-org/your-android-app'
          authToken: ${{ secrets.PAT_TOKEN }} # Required only for private repos
          taskName: 'assembleRelease'
```

### Action Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `repository` | Git repository URL of the Android project | Yes | `https://github.com/android/sunflower` |
| `authToken` | Personal Access Token (PAT) or auth token for private repos | No | `""` |
| `branchName` | Specific branch to clone | No | `""` (default branch) |
| `subdir` | Relative path if the Android project is nested | No | `""` |
| `jdkVersion` | OpenJDK version (e.g. 17, 21) | No | `21` |
| `taskName` | Gradle task (e.g. `assembleDebug`, `assembleRelease`) | No | `assembleRelease` |
| `uploadArtifact` | Upload APK artifact to GitHub workflow run | No | `true` |
| `retentionDays` | Artifact retention in days | No | `1` |

## Information

**Author:** Nissan Ahmed ([@ni554n](https://x.com/ni554n))

**Website:** [anissan.com](https://anissan.com)
<img src="https://ping.anissan.com/?repo=apk-builder-action" width="0" height="0" align="right">
