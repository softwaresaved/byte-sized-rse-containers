---
title: "Getting Started"
teaching: 5
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do I verify that Podman is installed and ready to use?
- What extra step is needed before using Podman on MacOS or Windows?
- Why does Podman run inside a virtual machine on MacOS and Windows?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Run a command to verify a Podman installation from the command line.
- Explain why MacOS and Windows require a Podman machine before containers can be run.
- Initialise and start a Podman machine on MacOS or Windows.
- Distinguish between how Podman operates on Linux versus MacOS and Windows.

::::::::::::::::::::::::::::::::::::::::::::::::

## Verify Your Podman Installation

In order for us to get started using Podman, we first need to open a command-line shell.

On macOS and most Linux distributions, open the **Terminal** application.
On Windows, the command-line shell you use depends on how you installed Podman.
This may be a Windows Subsystem for Linux (WSL) terminal, Command Prompt, or Git Bash.

Once your command-line shell is open, verify that Podman is installed by running:

```bash
podman --version
```

If Podman has been installed correctly, it will display its version number.
This confirms that the Podman executable is available from your command line for the tasks that follow.

::::::::::::::::::::::::::::: instructor

Who sees a version message displayed?

::::::::::::::::::::::::::::::::::::::::

## Starting the Podman Machine

If you are using Podman on a native Windows or macOS system, there is one additional step before you can begin using containers.
Unlike Linux, where Podman runs directly on the host operating system, Windows and macOS do not provide the Linux kernel that containers require.
Instead, Podman runs inside a lightweight Linux virtual machine that provides the environment needed to execute containers.

As mentioned during the installation process, this virtual machine must be initialised and started before Podman can be used.
If you are running Linux, or using Windows Subsystem for Linux (WSL), you can skip this section because Podman runs natively and is ready to use.

To initialise and start the virtual machine, run the following commands:

```bash
podman machine init
podman machine start
```

The first command creates and configures the virtual machine, while the second starts it.
During startup, you may see a message explaining that the Podman machine is running in **rootless** mode.
This is the expected configuration and simply means that Podman is operating without requiring administrator privileges,
which it's designed to do.

Although you interact with Podman using the same command-line interface on every platform, what happens behind the scenes differs slightly.
On Linux, the commands communicate directly with the local Podman installation.
On native Windows and macOS, the commands are forwarded to the Linux virtual machine,
which performs all container operations and transparently provides the same functionality to your host operating system.

If you would like a more detailed explanation of how this architecture works, refer to the installation guide linked in the course notes.
Again, you do not need to run `podman machine init` or `podman machine start` if you are using Linux or Windows Subsystem for Linux.


::::::::::::::::::::::::::::::::::::: keypoints 

- `podman --version` confirms that Podman is installed and accessible from the command line.
- On MacOS and Windows, Podman requires a lightweight Linux virtual machine, initialised with `podman machine init` and started with `podman machine start`.
- On Linux and Windows Subsystem for Linux (WSL), Podman runs natively and no virtual machine is needed.
- Podman operates in rootless mode by default, meaning it does not require administrator privileges.

::::::::::::::::::::::::::::::::::::::::::::::::
