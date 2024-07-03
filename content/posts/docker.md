---
title: 'Docker'
date: 2024-07-03T09:50:09-04:00
draft: false
tags:
  - notes
  - docker
categories: notes
keywords:
  - docker
---

## Docker

### Installation

We have two versions of Docker.

- Docker Community Version (Docker CE)
- Docker Enterprise (Docker EE)

**Instalation Guide**

- [Ubuntu](https://docs.docker.com/desktop/install/ubuntu/)
- [Debian](https://docs.docker.com/desktop/install/debian/)

### Storage

By default all files created inside a container are stored on a writable container layer. This means that:

- The data doesn't persist when that container no longer exists, and it can be difficult to get the data out of the container if another process needs it.
- A container's writable layer is tightly coupled to the host machine where the container is running. You can't easily move the data somewhere else.
- Writing into a container's writable layer requires a storage driver to manage the filesystem. The storage driver provides a union filesystem, using the Linux kernel. This extra abstraction reduces performance as compared to using data volumes, which write directly to the host filesystem.

#### Storage Drivers

The storage driver controls how images and containers are stored and managed on your Docker host.

There are multiple storage drivers supported by Docker:

- `overlay2`
- `fuse-overlays`
- `btrfs` and `zfs`
- `vfs`

### Running Images

```bash
docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG..]
```

Some commonly-used options:

- `-d`: Run the container in detached mode.
- `--name`: To give a descriptive name.
- `--restart`: Specify when the container should restart.
  - `no` (Default): Never restart.
  - `on-failure`: Only if the container fails.
  - `always`: always restart the container.
  - `unless-stopped`: unless manually stopped.
- `-p <host port>:<container port>`: Expose port
- `--rm`: Automatically remove the container when it exits. Cannot be used with `--restart`
- `--memory`: Hard limit on memory usage.
- `--memory-reservation`: A soft limit on memory usage. It will activate when the host is running low on memory.

```bash
docker run hello-world
docker run nginx:1.15.11 # specifying the tag
docker run busybox echo hello world! # sending command to run on container
docker run -d nginx:1.15.11 # run the container in detached mode
docker run -d --name nginx --restart always nginx:1.15.11
docker run -d --name nginx --restart unless-stopped -p 8080:80 --memory 500M --memory-reservation 256M nginx:1.15.11
```
