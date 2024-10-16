# Vernissage Redis

[![Nginx](https://img.shields.io/badge/Redis-Latest-blue.svg?style=flat)](https://redis.io)
[![Platforms macOS | Linux | Windows](https://img.shields.io/badge/Platforms-macOS%20%7C%20Linux%20%7C%20Windows%20-lightgray.svg?style=flat)](https://www.nginx.com)

Repository which stores fly.io configuration for distributed cache and queue component for Vernissage photos sharing platform.

## Docker

You can also run Redis Docker locally (or in other cloud platforms) using below command:

```
$ docker run -p 127.0.0.1:6379:6379/tcp --name vernissage-redis -e REDIS_ARGS="--requirepass secretpass" redis/redis-stack-server:latest
```