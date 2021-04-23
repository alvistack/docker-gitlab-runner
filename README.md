# Docker Image Packaging for GitLab Runner

[![GitLab pipeline status](https://img.shields.io/gitlab/pipeline/alvistack/docker-gitlab-runner/master)](https://gitlab.com/alvistack/docker-gitlab-runner/-/pipelines)
[![GitHub release](https://img.shields.io/github/release/alvistack/docker-gitlab-runner.svg)](https://github.com/alvistack/docker-gitlab-runner/releases)
[![GitHub license](https://img.shields.io/github/license/alvistack/docker-gitlab-runner.svg)](https://github.com/alvistack/docker-gitlab-runner/blob/master/LICENSE)
[![Docker Pulls](https://img.shields.io/docker/pulls/alvistack/gitlab-runner-13.11.svg)](https://hub.docker.com/r/alvistack/gitlab-runner-13.11)

GitLab is a complete DevOps platform, delivered as a single application. This makes GitLab unique and makes Concurrent DevOps possible, unlocking your organization from the constraints of a pieced together toolchain. Join us for a live Q\&A to learn how GitLab can give you unmatched visibility and higher levels of efficiency in a single application across the DevOps lifecycle.

Learn more about GitLab: <https://about.gitlab.com/>

## Supported Tags and Respective Packer Template Links

  - [`alvistack/gitlab-runner-13.11`](https://hub.docker.com/r/alvistack/gitlab-runner-13.11)
      - [`packer/docker-13.11/packer.json`](https://github.com/alvistack/docker-gitlab-runner/blob/master/packer/docker-13.11/packer.json)
  - [`alvistack/gitlab-runner-13.10`](https://hub.docker.com/r/alvistack/gitlab-runner-13.10)
      - [`packer/docker-13.10/packer.json`](https://github.com/alvistack/docker-gitlab-runner/blob/master/packer/docker-13.10/packer.json)

## Overview

This Docker container makes it easy to get an instance of GitLab Runner up and running.

Based on [Official Ubuntu Docker Image](https://hub.docker.com/_/ubuntu/) with some minor hack:

  - Packaging by Packer Docker builder and Ansible provisioner in single layer
  - Handle `ENTRYPOINT` with [catatonit](https://github.com/openSUSE/catatonit)

### Quick Start

For the `VOLUME` directory that is used to store the repository data (amongst other things) we recommend mounting a host directory as a [data volume](https://docs.docker.com/engine/tutorials/dockervolumes/#/data-volumes), or via a named volume if using a docker version \>= 1.9.

Configure GitLab Runner (`/etc/gitlab-runner/config.toml`):

    concurrent = 1
    check_interval = 0
    
    [session_server]
      session_timeout = 1800
    
    [[runners]]
      builds_dir = "/home/vagrant"
      cache_dir = "/var/cache/gitlab-runner"
      executor = "docker"
      name = "alvistack/gitlab-runner"
      token = "TOKEN"
      url = "https://gitlab.com/"
      [runners.docker]
        cpus = "2"
        disable_cache = false
        disable_entrypoint_overwrite = false
        image = "alvistack/gitlab-runner-13.11"
        memory = "8192m"
        memory_swap = "8192m"
        oom_kill_disable = false
        privileged = false
        shm_size = 0
        tls_verify = false
        volumes = [
          "/var/cache/gitlab-runner:/var/cache/gitlab-runner",
          "/var/run/docker.sock:/var/run/docker.sock",
        ]

Start GitLab Runner:

    # Pull latest image
    docker pull alvistack/gitlab-runner-13.11
    
    # Run as detach
    docker run \
        -itd \
        --name gitlab-runner \
        --volume /etc/gitlab-runner:/etc/gitlab-runner \
        --volume /var/cache/gitlab-runner:/var/cache/gitlab-runner \
        --volume /var/run/docker.sock:/var/run/docker.sock \
        alvistack/gitlab-runner-13.11

**Success**. GitLab Runner is now available.

## Upgrade

To upgrade to a more recent version of GitLab Runner you can simply stop the GitLab Runner
container and start a new one based on a more recent image:

    docker stop gitlab-runner
    docker rm gitlab-runner
    docker run ... (see above)

As your data is stored in the data volume directory on the host, it will still
be available after the upgrade.

Note: Please make sure that you don't accidentally remove the gitlab-runner container and its volumes using the -v option.

## Backup

For evaluations you can use the built-in database that will store its files in the GitLab Runner home directory. In that case it is sufficient to create a backup archive of the directory on the host that is used as a volume (`/var/opt/gitlab` in the example above).

## Versioning

### `YYYYMMDD.Y.Z`

Release tags could be find from [GitHub Release](https://github.com/alvistack/docker-gitlab-runner/releases) of this repository. Thus using these tags will ensure you are running the most up to date stable version of this image.

### `YYYYMMDD.0.0`

Version tags ended with `.0.0` are rolling release rebuild by [GitLab pipeline](https://gitlab.com/alvistack/docker-gitlab-runner/-/pipelines) in weekly basis. Thus using these tags will ensure you are running the latest packages provided by the base image project.

## License

  - Code released under [Apache License 2.0](LICENSE)
  - Docs released under [CC BY 4.0](http://creativecommons.org/licenses/by/4.0/)

## Author Information

  - Wong Hoi Sing Edison
      - <https://twitter.com/hswong3i>
      - <https://github.com/hswong3i>
