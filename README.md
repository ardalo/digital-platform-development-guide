# Ardalo Digital Platform Development Guide
Provides guidelines and FAQ for the development of the Ardalo Digital Platform.

## Table of Contents
* [Service Implementation Guide](#service-implementation-guide)
  * [Monitoring](#monitoring)
    * [\[Must\] Service exposes Health Check Endpoints](#must-service-exposes-health-check-endpoints)
    * [\[Must\] Service exposes a Prometheus Metrics Endpoint](#must-service-exposes-a-prometheus-metrics-endpoint)
  * [Documentation](#documentation)
    * [\[Must\] Service provides an Open API documentation](#must-service-provides-an-open-api-documentation)
    * [\[Could\] Service contains status badges in README](#could-service-contains-status-badges-in-readme)
  * [Infrastructure](#infrastructure)
    * [\[Must\] Service is Dockerized](#must-service-is-dockerized)
    * [\[Must\] Service is added to docker-compose.yml of digital-platform-overview](#must-service-is-added-to-docker-composeyml-of-digital-platform-overview)
* [Service Implementation Checklist](#service-implementation-checklist)
* [FAQ](#faq)
  * [Update Project Dependencies](#update-project-dependencies)
    * [Update Gradle Wrapper](#update-gradle-wrapper)

## Service Implementation Guide

### Monitoring

#### [Must] Service exposes Health Check Endpoints

**Details:**
* Service exposes an alive check endpoint
  * This endpoint indicates whether the service itself is running
  * Endpoint example: `/alive`
  * Returns HTTP Status Code `200` if the service is alive
  * If the service does not respond with HTTP Status Code `200`, it is considered dead and will be restarted
* Service exposes a readiness check endpoint
  * This endpoint indicates whether the service is able to process requests
  * Endpoint example: `/ready`
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
  * If a service is not alive, i.e. does not respond with HTTP Status Code `200` to a `alive` request,
    the service can be detected as dead and restarted automatically.
  * If a service is not ready, i.e. does not respond with HTTP Status Code `200` to a `ready` request,
    the service will be removed from load balancing and only added again once the `ready` endpoint
    return HTTP Status Code `200`

#### [Must] Service exposes a Prometheus Metrics Endpoint

**Details:**
* Service exposes an Endpoint providing Prometheus metrics

**Background:**
* Prometheus is the default monitoring solution of the Ardalo Digital Platform. Thus every service
  needs to play well with it.

### Documentation

#### [Must] Service provides an Open API documentation

**Details:**
* Service provides an Open API documentation accessible via Browser

**Background:**
* With every service providing an Open API documentation it is easy to explore existing APIs using
  a familiar interface. Familiar because this is set as a standard for every service and thus exploring
  different services works quite equal.

#### [Could] Service contains status badges in README

**Details:**
* Service may contain badges in its README file which provide insights of its current status
* Common badges:
  * Build Status, e.g. ![Build Status](https://img.shields.io/badge/build-passing-success)

**Background:**
* The README file of a Service is the place to go when looking for information. Status badges provide
  a brief overview of interesting service measures like the build status.

### Infrastructure

#### [Must] Service is Dockerized

**Details:**
* Service needs to provide the infrastructure to build a Docker Image of itself, so the service
  can be run as a Docker Container
* A `Dockerfile` needs to exist in the service which builds a Docker Image of the service
* A `docker-compose.yml` needs to exist in the service which can start the whole service including its
  dependencies (e.g. database)
* A `.dockerignore` file should be present in the service to exclude unnecessary files from Docker Images

**Background:**
* Docker has been chosen as the runtime environment of the Ardalo Digital Platform. Thus every service
  must provide a Docker Image of itself.

#### [Must] Service is added to docker-compose.yml of digital-platform-overview

**Details:**
* The service needs to be added to [`docker-compose.yml`](https://github.com/ardalo/digital-platform-overview/blob/master/docker-compose.yml)

**Background:**
* The [digital-platform-overview](https://github.com/ardalo/digital-platform-overview) repository provides
  a `docker-compose.yml` file which starts the whole Ardalo Digital Platform in a Docker Environment. Thus
  every component of the Ardalo Digital Platform needs to be added manually to this `docker-compose.yml`.

## Service Implementation Checklist
This checklist shall help to evaluate whether a service conforms to the [Service Implementation Guide](#service-implementation-guide)
and additionally show some Best Practices which should be adopted by services of the Ardalo Digital Platform.

- Monitoring
  - [ ] Service exposes an alive health check endpoint ([ℹ](#must-service-exposes-health-check-endpoints))
  - [ ] Service exposes a readiness health check endpoint ([ℹ](#must-service-exposes-health-check-endpoints))
  - [ ] Service exposes an endpoint providing Prometheus style metrics ([ℹ](#must-service-exposes-a-prometheus-metrics-endpoint))
- Documentation
  - [ ] Service provides an Open API documentation accessible via Browser ([ℹ](#must-service-provides-an-open-api-documentation))
  - [ ] (Optional) Service contains a build status badge in its README ([ℹ](#could-service-contains-status-badges-in-readme))
- Infrastructure
  - [ ] Service provides a `Dockerfile` to build a Docker Image of itself ([ℹ](#must-service-is-dockerized))
  - [ ] Service provides a `docker-compose.yml` ([ℹ](#must-service-is-dockerized))
  - [ ] (Optional) Service has a `.dockerignore` file to exclude unnecessary files from Docker Images ([ℹ](#must-service-is-dockerized))
  - [ ] Service is added to [`docker-compose.yml` of `digital-platform-overview`](https://github.com/ardalo/digital-platform-overview/blob/master/docker-compose.yml)
    ([ℹ](#must-service-is-added-to-docker-composeyml-of-digital-platform-overview))

## FAQ

### Update Project Dependencies

#### Update Gradle Wrapper

1. Find current Gradle version: https://gradle.org/releases/
2. Check release notes for breaking changes, new features etc.
3. Run Gradle wrapper update command:
    ```console
    $ ./gradlew wrapper --gradle-version=<gradle version, e.g. 6.6.1> --distribution-type=all
    ```
