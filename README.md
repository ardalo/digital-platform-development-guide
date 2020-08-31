# Ardalo Digital Platform Development Guide
Provides guidelines and FAQ for the development of the Ardalo Digital Platform.

## Table of contents
* [Service Implementation](#service-implementation)
  * [Monitoring](#monitoring)
    * [\[Must\] Every Service exposes Health Check Endpoints](#must-every-service-exposes-health-check-endpoints)
* [FAQ](#faq)
  * [Update Project Dependencies](#update-project-dependencies)
    * [Update Gradle Wrapper](#update-gradle-wrapper)

## Service Implementation

### Monitoring

#### [Must] Every Service exposes Health Check Endpoints

**Details:**
* Every Service exposes an alive check endpoint
  * This endpoint indicates whether the service itself is running
  * Endpoint: `/alive`
  * Returns HTTP Status Code `200` if the service is alive
  * If the service does not respond with HTTP Status Code `200`, it is considered dead and will be restarted
* Every Service exposes a readiness check endpoint
  * This endpoint indicates whether the service is able to process requests
  * Endpoint: `/ready`
  * Returns HTTP Status Code `200` if the service is ready
  * May return a JSON object providing some context about the checked dependencies and its statuses, e.g.
    ```json
    {
      "status": "UP",
      "database": "UP"
    }
    ```
  * If the service does not respond with HTTP Status Code `200`, it will be removed from load balancing
    until ready again
  * The endpoint must only check own dependencies, e.g. its own database. To prevent cascading errors,
    no external dependencies will be checked

**Background:**
* Health Check Endpoints are essential for an automated infrastructure:
  * If a service is not alive, i.e. does not respond with HTTP Status Code `200` to a `/alive` request,
    the service can be detected as dead and restarted automatically.
  * If a service is not ready, i.e. does not respond with HTTP Status Code `200` to a `/ready` request,
    the service will be removed from load balancing and only added again once the `/ready` endpoint
    return HTTP Status Code `200`

## FAQ

### Update Project Dependencies

#### Update Gradle Wrapper

1. Find current Gradle version: https://gradle.org/releases/
2. Check release notes for breaking changes, new features etc.
3. Run Gradle wrapper update command:
    ```
    ./gradlew wrapper --gradle-version=<gradle version, e.g. 6.6.1> --distribution-type=all
    ```
