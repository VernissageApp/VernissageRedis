# Vernissage Redis

[![Redis Stack](https://img.shields.io/badge/Redis%20Stack-latest-red.svg?style=flat)](https://redis.io)
[![Fly.io](https://img.shields.io/badge/Platform-Fly.io-8B5CF6.svg?style=flat)](https://fly.io)
[![Purpose](https://img.shields.io/badge/Role-Queue%20%26%20Cache-lightgray.svg?style=flat)](https://github.com/VernissageApp/VernissageServer)

Infrastructure repository for the dedicated Redis instance used by **Vernissage** on **Fly.io**.

This repository exists because the managed Redis offering previously used on Fly.io proved unstable in production. Instead of relying on that service, Vernissage runs Redis as a separate Fly application with its own minimal, explicit configuration.

## What This Repository Contains

This is not an application repository. It only stores the deployment configuration for a standalone Redis service:

- `fly.toml` - Fly.io app definition for the Redis machine
- `README.md` - operational notes for developers and maintainers

The deployed service is used by the Vernissage backend as shared infrastructure for:

- distributed cache,
- queue backend,
- scheduled/background work coordination.

## How It Fits Into Vernissage

Redis is an internal infrastructure component. Clients do not connect to it directly.

- `VernissageMobile` and `VernissageWeb` talk to `VernissageServer`
- `VernissageServer` uses Redis for queues and cache
- this repository defines the Redis deployment that backs that server-side workload

Related repositories:

- [VernissageServer](https://github.com/VernissageApp/VernissageServer)
- [VernissageMobile](https://github.com/VernissageApp/VernissageMobile)
- [VernissageWeb](https://github.com/VernissageApp/VernissageWeb)

## Current Fly.io Configuration

The current `fly.toml` deploys Redis with these core settings:

- app name: `vernissage-redis`
- primary region: `ams`
- image: `redis/redis-stack-server:latest`
- Redis port: `6379/tcp`
- metrics endpoint: `9091/metrics`
- machine policy: auto-start enabled, auto-stop enabled, minimum `1` running machine
- VM size: `2 shared CPUs`, `1 GB RAM`

## Requirements

To work with this repository you usually only need:

- [Fly CLI](https://fly.io/docs/flyctl/install/)
- Docker, if you want to run Redis locally without Fly.io

## Deploying

Deploy the Redis machine from the repository root:

```bash
$ fly deploy
```

Useful Fly commands during support work:

```bash
$ fly status
$ fly logs
$ fly machine list
$ fly ssh console
```

If you change machine sizing, region, ports, metrics, or mounts, update `fly.toml` and redeploy.

## Running Redis Locally

For local development or troubleshooting outside Fly.io, you can start a Redis container with Docker:

```bash
$ docker run -p 127.0.0.1:6379:6379/tcp \
  --name vernissage-redis \
  -e REDIS_ARGS="--requirepass secretpass" \
  redis/redis-stack-server:latest
```

This is useful when testing `VernissageServer` locally with a Redis-backed queue URL such as:

```text
redis://:secretpass@127.0.0.1:6379
```

## Operational Notes

- This repository is intentionally small. Most behavior is defined by the upstream Redis container image and Fly.io machine settings.
- If you need authentication or additional Redis runtime flags in Fly.io, they should be passed through environment variables or Fly secrets such as `REDIS_ARGS`.
- Redis is a support service for Vernissage Server, so incidents here usually surface first as queue, cache, or background job failures in the API.
- If data durability becomes important for your deployment mode, review whether the Fly volume mount should be enabled.

## License

This project is licensed under the Apache License 2.0.
