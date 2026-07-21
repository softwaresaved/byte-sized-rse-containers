---
title: Setup
---

## Setup

::: prereq

Skill prerequisites for the practical activity:

- Ability to navigate filesystem and run commands from within a shell or terminal application

Software prerequisites:

- Shell or terminal application
- Podman, for running and managing containers
- A text editor you are familiar with

:::

### Shell

On macOS and Linux, some version of a shell (e.g. `bash`) with Git will be available by default and no installation is needed.

If you do not have a bash shell installed on your system and require assistance with the installation, you can take a look at [the instructions provided by Software Carpentry](https://swcarpentry.github.io/python-novice-inflammation/#install-python)
for installing [shell](https://carpentries.github.io/workshop-template/install_instructions/#the-bash-shell) and [Git](https://carpentries.github.io/workshop-template/install_instructions/#git-1).
You'll also need a [GitHub.com](https://github.com/) account.

### Podman

There are detailed [instructions about installing podman](https://podman.io/docs/installation) on the Podman website,
but there is further information on installing Podman on Linux, MacOS or Windows at <https://www.universe-hpc.ac.uk/resources/byte-sized-rse/podman-info>.

We've chosen Podman instead of Docker for this training because it is free, open source,
and avoids the licensing and subscription considerations associated with Docker Desktop (which is the easiest way to install Docker) in organisational environments, including universities.
Additionally, Podman's command-line interface remains largely compatible with Docker, ensuring that the skills learned transfer easily to the Docker platform.

*Please note that Podman and Podman Desktop are two different tools.*
**For this course, we'll focus on the main podman container engine and its command line interface, not Podman Desktop.**

Podman Desktop provides a graphical user interface (GUI) for controlling and managing podman containers,
but it can also enable you to manage and work with containers provided by a range of other container platforms via a series of easy to install extensions or plugins.
If you install Podman Desktop, this should also install, as part of the installation process, podman itself.
However, we recommend just installing podman since that is the only tool required for this session.
