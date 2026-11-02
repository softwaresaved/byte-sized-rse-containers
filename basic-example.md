---
title: "A Basic Container Example"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do I run my first container with Podman?
- What is a container registry and how is one specified in a Podman command?
- What steps does `podman run` actually perform?
- How can I find help for Podman commands?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Recall the general structure of Podman CLI commands.
- Explain what a container registry is and how it is referenced in an image name.
- Run a container image using `podman run` and interpret its output.
- Distinguish between `podman run` and `podman image pull` and explain when each is appropriate.
- Use the `--help` flag to locate documentation for any Podman command.

::::::::::::::::::::::::::::::::::::::::::::::::

## Inspecting Images and Containers

Now that Podman is installed and running, we can inspect its current state.
Podman keeps a local collection of downloaded container images,
as well as a record of containers that are currently running or have previously been created.

To view the images stored on your system, run:

```bash
podman image list
```

If this is your first time using Podman, the list will probably be empty because no images have yet been downloaded.
If you have experimented with Podman previously, you may already see one or more images listed.

Similarly, you can inspect your containers with:

```bash
podman container list
```

Again, a fresh installation will typically report no containers.
Existing containers are perfectly fine if you have already explored Podman.

These commands also illustrate a common structure used throughout the Podman command-line interface.
Most commands begin with `podman`, followed by the type of resource being managed, such as `image` or `container`, and then an action to perform, such as `list`.
Many commands also accept optional flags and arguments that modify their behaviour or provide additional information.
Although not every Podman command follows this exact pattern,
it is common enough that learning it makes the command-line interface much easier to understand.

::::::::::::::::::::::::::::: instructor

Who managed to run the podman image list and podman container list commands?

::::::::::::::::::::::::::::::::::::::::

## Running Your First Container

Now that we have confirmed Podman is working, let's run our first container.
The simplest way to do this is with the `podman run` command.
In its most basic form, the only information we need to provide is the image we want to execute.

Enter the following command, but do not press `Enter` immediately:

```bash
podman run quay.io/podman/hello
```

The argument supplied to `podman run` identifies the image we want to use.
The first part, `quay.io`, is the address of a **container registry**.
A registry is an online repository that stores container images, allowing users to download and share them.
The second part, `podman/hello`, identifies the specific image within that registry.

Container registries can also be explored using a web browser.
For example, visiting <https://quay.io> allows you to search for available images.
Searching for **hello** will locate the `podman/hello` image, where you can view information about the image, its documentation, and available versions.
Browsing registries in this way is often useful when looking for software packaged as containers.

Now press `Enter` to execute the command.

If everything is configured correctly, Podman will download the image, create a container from it, and immediately run it.
After a short download, you should see a "Hello Podman World" message together with some ASCII artwork:

```output
Trying to pull quay.io/podman/hello:latest...
Getting image source signatures
Copying blob sha256:1ff9adeff4443b503b304e7aa4c37bb90762947125f4a522b370162a7492ff47
Copying config sha256:83fc7ce1224f5ed3885f6aaec0bb001c0bbb2a308e3250d7408804a720c72a32
Writing manifest to image destination
!... Hello Podman World ...!

         .--"--.           
       / -     - \         
      / (O)   (O) \        
   ~~~| -=(,Y,)=- |         
    .---. /`  \   |~~      
 ~/  o  o \~~~~.----. ~~   
  | =(X)= |~  / (O (O) \   
   ~~~~~~~  ~| =(Y_)=-  |   
  ~~~~    ~~~|   U      |~~ 

Project:   https://github.com/containers/podman
Website:   https://podman.io
Desktop:   https://podman-desktop.io
Documents: https://docs.podman.io
YouTube:   https://youtube.com/@Podman
X/Twitter: @Podman_io
Mastodon:  @Podman_io@fosstodon.org
```

::::::::::::::::::::::::::::: instructor

Who's managed to download and run the hello world container?

::::::::::::::::::::::::::::::::::::::::


## What `podman run` Actually Does

Although `podman run` appears to perform a single seamless task, it is actually carrying out two separate operations in sequence:

- Podman checks whether the required image already exists on your machine. If it does not, the image is downloaded from the specified registry and stored locally.
- Podman creates a new container from that image and starts it.

These steps can also be performed independently. For example, you could download an image in advance using:

```bash
podman image pull quay.io/podman/hello
```

```output
Trying to pull quay.io/podman/hello:latest...
Getting image source signatures
Copying blob sha256:1ff9adeff4443b503b304e7aa4c37bb90762947125f4a522b370162a7492ff47
Copying config sha256:83fc7ce1224f5ed3885f6aaec0bb001c0bbb2a308e3250d7408804a720c72a32
Writing manifest to image destination
83fc7ce1224f5ed3885f6aaec0bb001c0bbb2a308e3250d7408804a720c72a32
```

And later run it using the run command as before:

```bash
podman run quay.io/podman/hello
```

For convenience, however, `podman run` combines both operations whenever the image is not already available locally.

## Using the Built-in Help

As you begin working with Podman, it is worth remembering that comprehensive documentation is available directly from the command line.

Running:

```bash
podman --help
```

This displays an overview of the available commands, while appending `--help` to any specific command provides detailed information about its syntax, options, and arguments.
For example:

```bash
podman container rm --help
```

Will shows the complete documentation for removing containers.
Essentially, when building a command you can add ``--help`` at any stage to obtain assistance at that point.
Many commands support a large number of options, so the output can appear extensive.
However, it serves as a very helpful reference when you need to check the available functionality or remind yourself of a command's syntax.


::::::::::::::::::::::::::::::::::::: keypoints 

- Most Podman commands follow the pattern `podman <resource> <action>`, such as `podman image list` or `podman container list`.
- A container image name includes the registry address, repository, and image name, for example `quay.io/podman/hello`.
- `podman run` automatically pulls an image if it is not already stored locally, then creates and starts a new container from it.
- `podman image pull` downloads an image without running it, which can be useful to prepare images in advance.
- Appending `--help` to any Podman command displays documentation and options for it.

::::::::::::::::::::::::::::::::::::::::::::::::
