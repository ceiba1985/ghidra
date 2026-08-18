# Ghidra repository instructions

## Project structure
- This is a large multi-module Gradle repository rooted at `Ghidra/`, with most production code under:
  - `Ghidra/Framework` for shared infrastructure
  - `Ghidra/Features` for end-user features and tools
  - `Ghidra/Processors` for processor definitions
  - `Ghidra/Debug` for debugger components
  - `Ghidra/Extensions` for optional extensions
  - `Ghidra/Configurations` for packaged configurations
  - `Ghidra/Test` for higher-level test projects
- `GhidraBuild/` contains build support, packaging logic, and Eclipse plugin projects.
- `GhidraDocs/` contains project documentation.
- `GPL/` contains GPL-licensed standalone modules and must remain isolated from Apache-licensed code.
- The Gradle root includes nested projects from `settings.gradle`; prefer matching the existing module layout instead of creating new top-level directories.

## Coding conventions
- Primary language is Java, with supporting C++, Python, Sleigh, and Jython code in language-specific modules.
- Follow the existing local style of the file you are editing. Keep changes focused and avoid broad refactors or repository-wide formatting changes.
- Preserve the standard Ghidra Apache 2.0 license header on source files.
- For Java formatting, use the Eclipse formatter and shared preferences in:
  - `eclipse/GhidraEclipseFormatter.xml`
  - `eclipse/GhidraSharedPreferences.epf`
- Existing Java code uses tabs for indentation in many files and keeps braces on the same line; do not normalize unrelated formatting.
- Contributor guidance in `CONTRIBUTING.md` is important here: keep patches small, avoid unnecessary renames or find-and-replace edits, and do not add generated binaries.

## Build and development
- Required tools are documented in `README.md` and `DevGuide.md`:
  - JDK 25 for development builds
  - Gradle 9.1+ (or the provided `./gradlew` wrapper when available)
  - Python 3.9 through 3.14 with `pip`
  - Native toolchains for native components (`gcc`/`clang`/`make` on Linux/macOS, Visual Studio build tools on Windows)
- Before a full build, fetch non-Maven dependencies:
  - `./gradlew -I gradle/support/fetchDependencies.gradle`
- Common build commands:
  - `./gradlew buildGhidra` builds the compressed platform distribution in `build/dist/`
  - `./gradlew assembleAll` builds an uncompressed platform distribution
  - `./gradlew buildNatives` builds native components
  - `./gradlew prepdev eclipse` prepares the repository for Eclipse-based development
- The repository CI workflow in `.github/workflows/build-ghidra.yml` fetches dependencies and runs `./gradlew buildGhidra --parallel` on Ubuntu.

## Running the project
- For an installed or built distribution, launch the main application with `./ghidraRun` (`ghidraRun.bat` on Windows).
- Launch PyGhidra with `./support/pyghidraRun` when working in Python mode.
- Eclipse is the preferred IDE for developing Ghidra itself; `README.md` and `DevGuide.md` describe the `prepdev`, `eclipse`, and related setup tasks.

## Test framework
- The repository uses Gradle-managed JUnit 4 tests (`junit:junit:4.13.2` in `gradle/javaProject.gradle`).
- Common test source sets include:
  - `src/test/java` for unit tests
  - `src/integrationTest/java` for integration tests
  - `src/test.slow/java` for slower or headed tests
  - `src/pcodeTest/java` for p-code and processor-oriented tests
- Common test commands from `DevGuide.md`:
  - `./gradlew unitTestReport`
  - `./gradlew integrationTest`
  - `./gradlew combinedTestReport`
- Additional Gradle test configuration lives in `gradle/root/test.gradle` and `gradle/javaTestProject.gradle`.
- Some GUI-oriented tests need a display in CI or headless Linux environments. Use Xvfb and set `DISPLAY`, and set `JAVA_TOOL_OPTIONS=-DUSER_AGREEMENT=ACCEPT` for non-interactive startup when needed.

## Practical guidance for agents
- Read `README.md`, `DevGuide.md`, and `CONTRIBUTING.md` before making non-trivial changes.
- Prefer the smallest module-local change that fits the existing architecture.
- When editing tests or build logic, check the Gradle helper scripts under `gradle/` because many repository conventions are encoded there.
