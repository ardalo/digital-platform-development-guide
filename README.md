# Ardalo Digital Platform Development Guide
Provides guidelines and FAQ for the development of the Ardalo Digital Platform.

## Table of Contents
* [Service Implementation Guide](#service-implementation-guide)
  * [Monitoring](#monitoring)
    * [\[Must\] Every Service exposes Health Check Endpoints](#must-every-service-exposes-health-check-endpoints)
    * [\[Must\] Every Service exposes a Prometheus Metrics Endpoint](#must-every-service-exposes-a-prometheus-metrics-endpoint)
  * [Documentation](#documentation)
    * [\[Must\] Every Service provides an Open API documentation](#must-every-service-provides-an-open-api-documentation)
  * [Infrastructure](#infrastructure)
    * [\[Must\] Every Service needs to be Dockerized](#must-every-service-needs-to-be-dockerized)
* [Service Implementation Checklist](#service-implementation-checklist)
* [FAQ](#faq)
  * [Update Project Dependencies](#update-project-dependencies)
    * [Update Gradle Wrapper](#update-gradle-wrapper)

## Service Implementation Guide

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

#### [Must] Every Service exposes a Prometheus Metrics Endpoint

**Details:**
* Every Service exposes an Endpoint providing Prometheus metrics

**Background:**
* Prometheus is the default monitoring solution of the Ardalo Digital Platform. Thus every service
  needs to play well with it.

### Documentation

#### [Must] Every Service provides an Open API documentation

**Details:**
* Every Service provides an Open API documentation accessible via Browser

**Background:**
* With every service providing an Open API documentation it is easy to explore existing APIs using
  a familiar interface. Familiar because this is set as a standard for every service and thus exploring
  different services works quite equal.

### Infrastructure

#### [Must] Every Service needs to be Dockerized

**Details:**
* Every Service needs to provide the infrastructure to build a Docker Image of itself, so the service
  can be run as a Docker Container
* A `Dockerfile` needs to exist in the service which builds a Docker Image of the service
* A `docker-compose.yml` needs to exist in the service which can start the whole service including its
  dependencies (e.g. database)
* A `.dockerignore` file should be present in the service to exclude unnecessary files from Docker Images

**Background:**
* Docker has been chosen as the runtime environment of the Ardalo Digital Platform. Thus every service
  must provide a Docker Image of itself.

## Service Implementation Checklist
This checklist shall help to evaluate whether a service conforms to the [Service Implementation Guide](#service-implementation-guide)
and additionally show some Best Practices which should be adopted by services of the Ardalo Digital Platform.

- [ ] Service exposes a `/alive` health check endpoint ([ℹ](#must-every-service-exposes-health-check-endpoints))
- [ ] Service exposes a `/ready` health check endpoint ([ℹ](#must-every-service-exposes-health-check-endpoints))
- [ ] Service exposes an endpoint providing Prometheus style metrics ([ℹ](#must-every-service-exposes-a-prometheus-metrics-endpoint))
- [ ] Service provides an Open API documentation accessible via Browser ([ℹ](#must-every-service-provides-an-open-api-documentation))
- [ ] Service provides a `Dockerfile` to build a Docker Image of itself ([ℹ](#must-every-service-needs-to-be-dockerized))
- [ ] Service provides a `docker-compose.yml` ([ℹ](#must-every-service-needs-to-be-dockerized))
- [ ] (Optional) Service has a `.dockerignore` file do exclude unnecessary files from Docker Images ([ℹ](#must-every-service-needs-to-be-dockerized))

## FAQ

### Update Project Dependencies

#### Update Gradle Wrapper

1. Find current Gradle version: https://gradle.org/releases/
2. Check release notes for breaking changes, new features etc.
3. Run Gradle wrapper update command:
    ```
    ./gradlew wrapper --gradle-version=<gradle version, e.g. 6.6.1> --distribution-type=all
    ```
