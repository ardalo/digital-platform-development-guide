# Ardalo Digital Platform Development Guide
Provides guidelines and FAQ for the development of the Ardalo Digital Platform.

# FAQ

## Update Project Dependencies

### Update Gradle Wrapper

1. Find current Gradle version: https://gradle.org/releases/
2. Check release notes for breaking changes, new features etc.
3. Run Gradle wrapper update command:
    ```
    ./gradlew wrapper --gradle-version=<gradle version, e.g. 6.6.1> --distribution-type=all
    ```
