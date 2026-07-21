---
site: sandpaper::sandpaper_site
---

This is a Byte-Sized RSE lesson on containers using the Podman open source container engine, part of the [Byte-Sized RSE Series](https://github.com/carpentries-incubator/byte-sized-rse-overview/tree/main).

Containers have become an essential tool for modern research software development, enabling reproducible computational environments that run consistently across laptops, servers, and high-performance computing systems.
In this lesson, you will learn what containers are and how they differ from virtual machines,
before gaining hands-on experience with Podman: a free, open source, daemonless container engine.
Starting from verifying your installation, you will progress through pulling images from public registries,
running and managing containers, working with long-lived services, executing commands inside running containers,
and sharing data between your host machine and a container.


## Learning Objectives

At the end of this lesson, learners should be able to:

- Explain what containers are, how they work, and why they are useful for reproducible research software.
- Describe the key differences between containers and virtual machines.
- Verify a Podman installation and start a Podman machine on macOS or Windows.
- Pull container images from public registries and run containers using the Podman command-line interface.
- Manage the lifecycle of containers, including naming, starting, stopping, and removing them.
- Run a service container in detached mode, publish ports, and inspect its logs.
- Execute commands and open interactive shells inside running containers.
- Mount host directories into containers to share data between the host and the container.
- Search container registries from the command line and configure registry search on Linux.


