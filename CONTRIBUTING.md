I'll help you draft a CONTRIBUTING.md file for the NEXA-offline-messenger repository. Let me first check the current repository to understand its structure and any existing contribution guidelines.Based on the README for the NEXA offline messenger project, here's a comprehensive CONTRIBUTING.md file tailored to your project:

```markdown name="CONTRIBUTING.md"
# Contributing to NEXA

Thank you for your interest in contributing to NEXA! This document provides guidance on how to set up your development environment, understand the codebase, and submit your contributions.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)
- [Testing Requirements](#testing-requirements)
- [Architecture & Design Patterns](#architecture--design-patterns)
- [Submitting Changes](#submitting-changes)
- [Pull Request Process](#pull-request-process)
- [Reporting Issues](#reporting-issues)

---

## Code of Conduct

We are committed to providing a welcoming and inclusive environment for all contributors. Please:

- Be respectful and constructive in all interactions
- Focus on the code, not the person
- Help others learn and grow
- Report any inappropriate behavior to the maintainers

---

## Getting Started

### Prerequisites

Before you start, ensure you have the following installed:

| Requirement | Version |
|---|---|
| **Android Studio** | Ladybug (2025.x) or later |
| **JDK** | 17 |
| **Android SDK** | compileSdk 35, minSdk 24 |
| **Gradle** | 8.13+ (wrapper included) |
| **Git** | Latest stable |

> **Note:** Two physical Android devices are required for testing Nearby Connections features (emulators do not support the Nearby Connections API).

### Setting Up Your Development Environment

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Tasima/NEXA-offline-messenger.git
   cd NEXA-offline-messenger
   ```

2. **Open in Android Studio:**
   - File → Open → select the repository directory
   - Android Studio will prompt you to sync Gradle — click "Sync Now"

3. **Verify your setup:**
   ```bash
   ./gradlew --version     # Check Gradle installation
   ./gradlew assembleDebug # Build the debug variant
   ```

4. **Review the documentation:**
   Read through the recommended docs in this order:
   - [`README.md`](README.md) — project overview
   - [`docs/DESIGN.md`](docs/DESIGN.md) — architecture and design rationale
   - [`docs/PROJECT_BREAKDOWN.md`](docs/PROJECT_BREAKDOWN.md) — feature scope and roadmap
   - [`docs/design-patterns-used.md`](docs/design-patterns-used.md) — design patterns in the codebase

---

## Development Workflow

### 1. Create a Feature Branch

```bash
git checkout -b feature/your-feature-name
# or for bug fixes:
git checkout -b bugfix/issue-description
```

Use descriptive branch names:
- `feature/qr-code-improvements`
- `bugfix/offline-message-sync-issue`
- `docs/add-api-reference`

### 2. Make Your Changes

- Work on a single feature or bug fix per branch
- Keep commits atomic and logically separated
- Write clear commit messages:
  ```
  Add QR code scanning improvements
  
  - Improve QR code error handling
  - Add validation for malformed QR codes
  - Add unit tests for validation logic
  ```

### 3. Test Locally

Before submitting, ensure:
- Your code builds without errors: `./gradlew assembleDebug`
- All tests pass (see [Testing Requirements](#testing-requirements))
- You've tested the feature manually on at least one physical device

### 4. Keep Your Branch Updated

```bash
git fetch origin
git rebase origin/main  # or the default branch
```

---

## Coding Standards

### Kotlin Style Guide

- Follow the [Official Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html)
- Use Android Studio's built-in code formatter: **Code → Reformat Code**

### Key Conventions

| Aspect | Rule |
|---|---|
| **Naming** | Use camelCase for variables/functions, PascalCase for classes |
| **Visibility** | Keep functions/properties private by default; make public only when necessary |
| **Nullability** | Use nullable types sparingly; prefer default parameters or sealed classes |
| **Comments** | Document complex logic; avoid obvious comments |
| **File Organization** | Group related functionality; max 400 lines per file |

### Android Best Practices

- **Use coroutines** for asynchronous operations (avoid callbacks)
- **Use StateFlow/SharedFlow** for UI state management (not LiveData)
- **Keep Activities and Services lightweight** — move logic to ViewModels and utilities
- **Use dependency injection** where practical
- **Don't hold strong references** to Activities/Services in other objects

### Architecture Reminders

NEXA follows a **layered architecture** with clear separation of concerns:

```
UI Layer (Activities, Screens, ViewModels)
    ↓
Logic Layer (MessageCenter, EnhancedMsgHandler)
    ↓
DataLayer (Database, Repositories)
    ↓
CommsLayer (NearbyService, DTN)
    ↓
Utils Layer (Crypto, Permissions, QR)
```

When adding new code:
- Place business logic in the appropriate layer
- Avoid tight coupling between layers
- Use abstractions (interfaces) for cross-layer dependencies
- Refer to [`docs/design-patterns-used.md`](docs/design-patterns-used.md) before modifying existing modules

---

## Testing Requirements

All new code **must** include tests. We use:
- **JUnit 4** for unit tests
- **Mockito** or built-in Kotlin test doubles for mocking
- **Instrumented tests** for Android-specific features

### Writing Unit Tests

1. Place tests in `app/src/test/` for non-Android code
2. Place tests in `app/src/androidTest/` for Android code
3. Follow the naming convention: `FeatureTest.kt` or `FeatureTestCase.kt`
4. Aim for >70% code coverage on new logic

Example unit test structure:
```kotlin
class MessageCenterTest {
    @Before
    fun setUp() {
        // Initialize test fixtures
    }

    @Test
    fun testSendMessageToReachablePeer() {
        // Arrange
        val message = Message(content = "Hello")
        
        // Act
        messageCenter.send(message, peerId)
        
        // Assert
        assertTrue(messageCenter.wasSent(message))
    }

    @After
    fun tearDown() {
        // Clean up
    }
}
```

### Running Tests

```bash
# Unit tests
./gradlew test

# Instrumented tests (requires a connected device)
./gradlew connectedAndroidTest

# Both
./gradlew testDebug
```

Review test results in: `app/build/reports/tests/`

---

## Architecture & Design Patterns

### Key Design Patterns Used

NEXA uses several Gang of Four (GoF) patterns. Familiarize yourself with:

- **Observer Pattern** — StateFlow/SharedFlow for UI updates
- **Repository Pattern** — DatabaseRepository for data access
- **Service Locator / Singleton** — Application-wide services (NearbyService, MessageCenter)
- **Factory Pattern** — Message and DTN bundle creation
- **Strategy Pattern** — Different messaging modes (direct vs. DTN routing)

**Before modifying existing code**, review [`docs/design-patterns-used.md`](docs/design-patterns-used.md) to understand the patterns in use.

### Offline-First Design Philosophy

NEXA prioritizes offline functionality and serverless architecture. When contributing:

- **Do not introduce cloud/server dependencies** without discussion
- **Assume intermittent connectivity** in your design
- **Persist data locally** before attempting to sync
- **Handle network failures gracefully** with retry logic and user feedback

---

## Submitting Changes

### Step 1: Commit Your Changes

Write clear, descriptive commit messages:

```bash
git add .
git commit -m "Add DTN route caching for improved performance

- Implement LRU cache for DTN routes
- Add unit tests for cache eviction
- Update MessageCenter to use cached routes
- Closes #42"
```

Good commit messages:
- Start with a present-tense, imperative verb
- Reference related issues (e.g., "Closes #42")
- Explain the **why**, not just the **what**

### Step 2: Push to Your Fork

```bash
git push origin feature/your-feature-name
```

### Step 3: Create a Pull Request (PR)

On GitHub:

1. Click "New Pull Request"
2. Compare your branch against the main branch
3. Fill out the PR template (if available) with:
   - **Title:** Brief description of changes
   - **Description:** What changed and why
   - **Related Issues:** Link to any open issues (e.g., "Fixes #42")
   - **Testing:** Describe how you tested the changes
   - **Screenshots/Videos:** Include visuals for UI changes

---

## Pull Request Process

### Before Your PR is Merged

1. **All tests must pass** — both local and CI/CD
2. **Code review:** At least one maintainer will review your changes
3. **No merge conflicts** — keep your branch up to date with main
4. **Documentation:** Update docs if your changes affect architecture or user behavior

### Code Review Expectations

Reviewers may ask you to:
- Add/improve test coverage
- Refactor code for clarity or performance
- Add comments explaining complex logic
- Update documentation

This is normal and helps maintain code quality. Be open to feedback!

### After Your PR is Merged

- Delete your branch (GitHub offers this option)
- Check the next release notes to see your contribution acknowledged

---

## Reporting Issues

### Found a Bug?

1. **Check existing issues** first to avoid duplicates
2. **Create a new issue** with:
   - Clear, descriptive title
   - Steps to reproduce
   - Expected vs. actual behavior
   - Device info (model, Android version)
   - Relevant logs (use code blocks)
   - Screenshots if applicable

### Have a Feature Idea?

1. **Check the Roadmap** in [`docs/PROJECT_BREAKDOWN.md`](docs/PROJECT_BREAKDOWN.md)
2. **Search existing issues** to avoid duplicates
3. **Create a new discussion** with:
   - What feature you want to add
   - Why it would be useful
   - Potential implementation approach (if you have one)

---

## Questions or Need Help?

- Review the [Documentation Guide](README.md#-documentation-guide) in the README
- Check existing issues and pull requests
- Create a new discussion for questions
- Reach out to maintainers politely in issue comments

---

## License

By contributing to NEXA, you agree that your contributions will be licensed under the same license as the project (see LICENSE file).

---

## Recognition

We appreciate all contributions, big and small! Contributors will be recognized in:
- Release notes
- GitHub contributors page
- Project documentation (for significant contributions)

Thank you for helping make NEXA better! 🚀

```

---

## Next Steps

Would you like me to:

1. **Create and push this file** to your repository?
2. **Customize any sections** (e.g., add specific guidelines for certain areas)?
3. **Add additional sections** (e.g., local build troubleshooting, CI/CD pipeline details)?

Let me know how you'd like to proceed!
