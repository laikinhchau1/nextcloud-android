<!--
 ~ SPDX-FileCopyrightText: 2025 Nextcloud GmbH and Nextcloud contributors
 ~ SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Nextcloud Android Client - Agent Guide

This file contains essential information for AI coding agents working on the Nextcloud Android project.

## Project Overview

The **Nextcloud Android Client** is the official Android application for [Nextcloud](https://nextcloud.com), a self-hosted cloud storage and collaboration platform. The app enables users to access, sync, and manage their files on a Nextcloud server from their Android devices.

- **Package Name**: `com.nextcloud.client`
- **Language**: Kotlin (primary), Java (legacy)
- **License**: AGPL-3.0-or-later OR GPL-2.0-only
- **Repository**: https://github.com/nextcloud/android

## Technology Stack

### Core Technologies
- **Language**: Kotlin 2.3.10, Java 21
- **Build System**: Gradle 8.x with Kotlin DSL
- **Android SDK**: 
  - Min SDK: 28 (Android 9.0)
  - Compile/Target SDK: 36 (Android 16)
- **NDK**: 21.4.7075529 (optional, for smaller APKs)

### Architecture & Frameworks
- **Dependency Injection**: Dagger 2
- **Database**: Room (SQLite)
- **Background Jobs**: WorkManager
- **Networking**: Nextcloud Android Library (WebDAV-based)
- **Media Playback**: ExoPlayer (Media3)
- **UI**: Jetpack Compose + traditional Views
- **Async**: Coroutines and WorkManager

### Key Libraries
- **UI**: Material Design Components, Jetpack Compose, PhotoView, Flexbox
- **Image Loading**: Glide, Coil
- **Testing**: JUnit, Espresso, Mockito, MockK, Shot (screenshots)
- **Code Quality**: Spotless, Detekt, SpotBugs, Checkstyle, PMD
- **Security**: Conscrypt, BouncyCastle

## Project Structure

```
nextcloud-android/
├── app/                          # Main application module
│   ├── src/main/java/com/
│   │   ├── nextcloud/client/     # Modern Kotlin code
│   │   │   ├── account/          # User account management
│   │   │   ├── assistant/        # AI assistant feature (Compose UI)
│   │   │   ├── core/             # Core utilities (async, clock, etc.)
│   │   │   ├── database/         # Room database entities and DAOs
│   │   │   ├── device/           # Device info and power management
│   │   │   ├── di/               # Dependency injection modules
│   │   │   ├── documentscan/     # Document scanning feature
│   │   │   ├── etm/              # Enterprise Test Mode
│   │   │   ├── jobs/             # Background workers
│   │   │   ├── logger/           # Logging infrastructure
│   │   │   ├── media/            # Audio/video playback
│   │   │   ├── migrations/       # Database migrations
│   │   │   ├── network/          # Network connectivity
│   │   │   ├── notifications/    # Notification handling
│   │   │   ├── onboarding/       # First-run experience
│   │   │   ├── preferences/      # Settings management
│   │   │   └── utils/            # Utility classes
│   │   └── owncloud/android/     # Legacy Java code
│   │       ├── authentication/   # Login/account setup
│   │       ├── datamodel/        # Data models
│   │       ├── files/            # File operations
│   │       ├── operations/       # Remote operations
│   │       ├── providers/        # Content providers
│   │       ├── services/         # Background services
│   │       ├── syncadapter/      # Sync functionality
│   │       ├── ui/               # Activities and Fragments
│   │       └── utils/            # Legacy utilities
│   ├── src/androidTest/          # Instrumented tests
│   ├── src/test/                 # Unit tests
│   ├── src/generic/              # F-Droid flavor code
│   ├── src/gplay/                # Google Play flavor code
│   ├── src/versionDev/           # Dev build flavor
│   └── src/qa/                   # QA build flavor
├── appscan/                      # Document scanning library module
├── scripts/                      # Build and CI scripts
├── fastlane/                     # Deployment automation
└── gradle/                       # Gradle wrapper and version catalog
```

## Build System

### Build Variants (Flavors)
1. **generic** - F-Droid build (no Google dependencies)
2. **gplay** - Google Play Store build (with Firebase Cloud Messaging)
3. **huawei** - Huawei AppGallery build
4. **versionDev** - Development build (separate app package)
5. **qa** - QA testing build

### Key Gradle Tasks

```bash
# Build APKs
./gradlew assembleGenericDebug
./gradlew assembleGplayDebug
./gradlew assembleGplayRelease

# Run tests
./gradlew testGplayDebugUnitTest           # Unit tests
./gradlew createGplayDebugCoverageReport   # Instrumented tests with coverage

# Code quality checks
./gradlew check                            # Run all checks
./gradlew spotlessCheck                    # Check code formatting
./gradlew spotlessApply                    # Auto-format code
./gradlew detekt                           # Static analysis (Kotlin)
./gradlew lint                             # Android lint
./gradlew spotbugsGplayDebug               # Bug pattern detection
./gradlew checkstyle                       # Java style check
./gradlew pmd                              # Java code analysis

# Coverage
./gradlew jacocoTestGplayDebugUnitTest     # Unit test with JaCoCo coverage
```

### Build Configuration
- Version defined in `app/build.gradle.kts`:
  - `versionMajor = 3`
  - `versionMinor = 37`
  - `versionPatch = 0`
- Version code format: `major * 10000000 + minor * 10000 + patch * 100 + build`

## Development Conventions

### Code Style
- **Line length**: 120 characters (EditorConfig enforced)
- **Indentation**: 4 spaces (no tabs)
- **Formatting**: Spotless with ktlint
- **Imports**: Auto-optimized on the fly

### File Naming (based on Ribot's guidelines)
| Component | Class Name | Layout Name |
|-----------|------------|-------------|
| Activity | `UserProfileActivity` | `activity_user_profile.xml` |
| Fragment | `SignUpFragment` | `fragment_sign_up.xml` |
| Dialog | `ChangePasswordDialog` | `dialog_change_password.xml` |
| Adapter Item | - | `item_person.xml` |
| Partial Layout | - | `partial_stats_bar.xml` |
| Menu | - | `activity_user_profile.xml` |

### License Headers
Every new file must include a license header:

**Kotlin/Java:**
```java
/*
 * Nextcloud - Android Client
 *
 * SPDX-FileCopyrightText: 2025 Your Name <your@email.com>
 * SPDX-License-Identifier: AGPL-3.0-or-later
 */
```

**XML:**
```xml
<!--
  ~ Nextcloud - Android Client
  ~ 
  ~ SPDX-FileCopyrightText: 2025 Your name <your@email.com>
  ~ SPDX-License-Identifier: AGPL-3.0-or-later
 -->
```

### Dependency Injection Guidelines
- Use **Dagger 2** for major Android components (Activity, Fragment, Service, BroadcastReceiver, ContentProvider)
- Use **constructor injection** for other classes
- Avoid calling constructors inside constructors

### Custom Platform APIs
Avoid using Android platform APIs directly when Nextcloud-specific replacements exist:
- Account management → Nextcloud account APIs
- Preferences → Nextcloud preferences
- Background tasks → WorkManager wrappers
- Networking → Nextcloud library
- Logging → Logger API

## Testing Strategy

### Test Types

1. **Unit Tests** (`src/test/`)
   - Isolated, no Android SDK required
   - Run with: `./gradlew jacocoTestGplayDebugUnitTest`
   - Coverage report: `app/build/reports/jacoco/`

2. **Instrumented Tests** (`src/androidTest/`)
   - Require Android SDK/emulator
   - Run with: `./gradlew createGplayDebugCoverageReport -Pcoverage=true`
   - Run specific test: `./gradlew createGplayDebugCoverageReport -Pcoverage=true -Pandroid.testInstrumentationRunnerArguments.class=ClassName`

3. **UI/Screenshot Tests**
   - Uses Shot library
   - Requires `SHOT_TEST=true` environment variable
   - Check screenshots: `scripts/androidScreenshotTest`
   - Update screenshots: `scripts/updateScreenshots.sh`

4. **Server Integration Tests**
   - Extend `AbstractOnServerIT`
   - Configure server in `~/.gradle/gradle.properties`:
     ```
     NC_TEST_SERVER_BASEURL=http://server
     NC_TEST_SERVER_USERNAME=test
     NC_TEST_SERVER_PASSWORD=test
     ```

### Test Coverage
- Coverage reports uploaded to Codecov
- Target: all new code should have tests
- JaCoCo configuration in `jacoco.gradle.kts`

## CI/CD Pipeline

### GitHub Actions Workflows
- **unit-tests.yml** - Unit tests with coverage
- **check.yml** - Code quality (detekt, spotless, lint)
- **assembleFlavors.yml** - Build all flavors
- **qa.yml** - QA builds for PRs
- **screenShotTest.yml** - Screenshot tests
- **analysis.yml** - Static analysis
- **codeql.yml** - Security analysis

### Drone CI
- Runs instrumented tests on Android emulator
- Tests against Nextcloud server (stable and master branches)
- Integration with end-to-end encryption, text, activity apps

### Quality Gates
All PRs must pass:
1. Unit tests
2. Instrumented tests (excl. screenshot tests)
3. Spotless formatting check
4. Detekt static analysis
5. Android lint
6. SpotBugs bug patterns

## Security Considerations

1. **No hardcoded secrets** - Use BuildConfig fields for sensitive data
2. **Certificate pinning** - Conscrypt for TLS
3. **End-to-end encryption** - E2EE support for sensitive files
4. **App signing** - Different certificates for each flavor
5. **Security scanning**:
   - SpotBugs with FindSecBugs plugin
   - CodeQL analysis
   - Dependency vulnerability scanning (Renovate)

## Performance Guidelines

For performance analysis, build with:
```bash
./gradlew installGplayDebug -PperfAnalysis
```
This enables:
- LeakCanary (memory leak detection)
- StrictMode (detects policy violations)

## Release Process

### Version Types
1. **Stable** - Production releases on Play Store and F-Droid
2. **Release Candidate** - Beta releases for testing
3. **Dev** - Daily development builds

### Branching Model
- `master` - Main development branch, all PRs target this
- `stable-X.Y.x` - Bugfix branches for stable releases
- Use `/backport to stable-X.Y` for backporting PRs

### Release Cycle
- ~2 months between releases
- 6 weeks development, 2 weeks stabilization
- Bugfix releases (dot releases) 4 weeks after stable

## Debugging

### Log Collection
```bash
# With adb
adb logcat --pid=$(adb shell pidof -s 'com.nextcloud.client') > logcat.txt

# On rooted device
logcat -d --pid $(pidof -s com.nextcloud.client) -f /sdcard/logcat.txt
```

### Enterprise Test Mode (ETM)
Access hidden debugging features:
- Settings → Version number (tap 10 times)
- Provides access to background jobs, preferences, migrations

## Useful Resources

- **Setup Guide**: [SETUP.md](SETUP.md)
- **Contributing Guidelines**: [CONTRIBUTING.md](CONTRIBUTING.md)
- **Security**: [SECURITY.md](SECURITY.md)
- **Translations**: [Transifex](https://app.transifex.com/nextcloud/nextcloud/android/)
- **Issue Tracker**: https://github.com/nextcloud/android/issues
- **Documentation**: https://docs.nextcloud.com/

## Common Issues

1. **Out of Memory**: Increase heap in `gradle.properties`:
   ```
   org.gradle.jvmargs=-Xmx4G
   ```

2. **Shot tests fail**: Ensure `SHOT_TEST=true` environment variable is set

3. **Emulator tests hang**: Check emulator skin size (500x833 required)

4. **Translation changes**: Only modify `values/strings.xml`, never language-specific files (managed by Transifex)
