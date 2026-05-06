# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Minimal Alpine-based Docker image used as a build/demo container. The image copies `quickstart.sh` as its default command and writes a `hello.txt` during the build, which CI uses as a smoke-test artifact.

## Build and Run

```bash
# Build locally
docker build -t buildcontainer .

# Run the container (prints a timestamped greeting)
docker run --rm buildcontainer

# Smoke-test: verify hello.txt exists in the image (mirrors CI test stage)
docker run --rm buildcontainer cat hello.txt
```

## CI

Two CI systems are configured:

- **Travis CI** (`.travis.yml`): builds the Docker image, then tags and pushes to Docker Hub using `$DOCKER_USERNAME` / `$DOCKER_PASSWORD` environment variables. The `master` branch pushes as the latest tag; the `dev` branch pushes with a `:dev` tag. The separate test stage runs the container and verifies `hello.txt`.
- **Jenkins** (`Jenkinsfile`): runs inside a `maven:3-alpine` Docker agent with a local `.m2` cache mount and executes `mvn -B -DskipTests clean package`. This targets a Maven build layer not currently present in the repo — do not remove the Jenkinsfile without confirming upstream pipelines no longer reference it.
