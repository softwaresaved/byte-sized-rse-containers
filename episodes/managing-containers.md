---
title: "Managing Containers"
teaching: 20
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do I view all containers on my system, including those that have stopped?
- How do I run a container in the background and still access its output?
- How can I execute commands or open a shell inside a running container?
- How do I share files between my host machine and a container?
- How do I remove containers and images when they are no longer needed?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Use podman commands for listing, starting, stopping, and removing containers and images.
- Explain the difference between `podman run` and `podman container start`.
- Interpret container exit codes and explain what they indicate.
- Run a long-lived service container in detached mode with a published port.
- Use `podman exec` to run commands and open an interactive shell inside a running container.
- Mount a host directory into a container using the `-v` option to share files.
- Determine the correct order of operations for removing containers and their associated images.

::::::::::::::::::::::::::::::::::::::::::::::::


## Inspecting Images and Containers

Now that we've downloaded and run our first container, we can inspect the images and containers stored on our system.

To see the images currently available, run:

```bash
podman image list
```

```output
REPOSITORY            TAG         IMAGE ID      CREATED      SIZE
quay.io/podman/hello  latest      83fc7ce1224f  2 years ago  580 kB
```

This shows the `hello` image that Podman downloaded when we ran the container.
Notice the `CREATED` column.
This refers to when the image was created and published by its developer,
and not when it was downloaded onto your computer.

If we now list our running containers:

```bash
podman container list
```

```output
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES
```

It may seem odd that the output is empty.
Although we just ran a container, this command only displays currently running containers.
Our `hello` container simply started, printed its message, and then exited immediately.
This highlights an important characteristic of containers:
they do not need to be long-running services.
Many containers are designed as one-shot programs that perform a task and then terminate.

To view *all* containers, including those that have already stopped,
use the `--all` option:

```bash
podman container list --all
```

Or equivalently:

```bash
podman container list -a
```

```output
CONTAINER ID  IMAGE                        COMMAND               CREATED        STATUS                    PORTS       NAMES
2a2af201f219  quay.io/podman/hello:latest  /usr/local/bin/po...  4 minutes ago  Exited (0) 4 minutes ago              confident_solomon
```

This time you'll see the container that was created a few moments ago.
Notice that its status is shown as `Exited (0)`.
The value in parentheses is the container's exit code:

- An exit code of `0` indicates that the container completed successfully..
- An exit code of `1` or otherwise would indicate the container exited prematurely due to an error.

The name `confident_solomon` name is randomly assigned as a human-readable identifier for the container.


## Naming Containers

Another useful column is `NAMES`.
By default, Podman assigns each new container a randomly generated name consisting of two words.
This gives us a convenient way to refer to the container later, but remembering randomly assigned names can quickly become inconvenient.

A more predictable approach is to assign your own name when creating the container by using the `--name` option:

```bash
podman run --name hello_world quay.io/podman/hello
```

This time you'll notice that Podman does **not** download the image again,
because it is already stored locally.
You can confirm this by running:

```bash
podman image list
```

There is still only a single `hello` image.
However, if you list all containers again:

```bash
podman container list -a
```

```output
CONTAINER ID  IMAGE                        COMMAND               CREATED        STATUS                    PORTS       NAMES
2a2af201f219  quay.io/podman/hello:latest  /usr/local/bin/po...  6 minutes ago  Exited (0) 6 minutes ago              confident_solomon
15731f65ecfe  quay.io/podman/hello:latest  /usr/local/bin/po...  7 seconds ago  Exited (0) 7 seconds ago              hello_world
```

You'll now see two containers.
Although both were created from the same image, each execution of `podman run` creates a new container.
The second container uses the name we explicitly assigned, i.e. `hello_world`.

::::::::::::::::::::::::::::: instructor

Who has successfully run another `hello` container using a specified name?

::::::::::::::::::::::::::::::::::::::::


## Restarting an Existing Container

Rather than creating a new container every time, we can start an existing one again using `podman container start`:

```bash
podman container start hello_world
```

```output
hello_world
```

The container does run, but only the container name appears on the terminal.
This is because `podman container start` starts the container without attaching its standard input and output to the current terminal.

When we originally used `podman run`, the terminal was attached automatically, allowing us to see the program's output.
To achieve the same behaviour when restarting an existing container, add the `--attach` option:

```bash
podman container start --attach hello_world
```

This reruns the existing container and displays its output in the terminal.

Importantly, using `podman run` again would *create* another new container,
whereas `podman container start` allows us to *rerun* one that already exists.

::::::::::::::::::::::::::::::::::::::::::  callout

## What's the Difference between `run` and `start` in terms of Data Persistence?

`run` creates and starts a new container from an image, giving it its own writable filesystem
in which changes can be made and persisted for the lifetime of that container.
Each time `run` is executed, a new container is created with a fresh writable layer for files,
erasing any previously stored state in a writable layer.

In contrast, `start` restarts an existing, stopped container, preserving its writable filesystem,
including files created, modified or deleted during previous executions.
This means that changes made within a container persist across stops and starts,
but are lost when the container is removed.
For this reason, if you need the filesystem data to persistent within a container,
it is strongly recommended you use a method of storing data to an external source,
such as data volumes or bind mounts.
We'll look at bind mounts shortly.

:::::::::::::::::::::::::::::::::::::::::::::::::::


## Command Shortcuts

Typing `podman container` before every command quickly becomes repetitive,
so Podman provides convenient shortcuts for many common operations.

For example, instead of:

```bash
podman container start --attach hello_world
```

you can simply write:

```bash
podman start --attach hello_world
```

Likewise, to stop a long-running container (which we'll encounter later),
you can use:

```bash
podman stop <container-name>
```

instead of:

```bash
podman container stop <container-name>
```

Similar shortcuts are available for several other frequently used Podman commands.

::::::::::::::::::::::::::::: instructor

Who's managed to restart their container and attach a terminal?

::::::::::::::::::::::::::::::::::::::::


## Removing Containers and Images

As we continue working with containers, we'll gradually accumulate images and containers on our system.
Some of these may no longer be useful, so it's important to know how to remove them and reclaim disk space.

Let's start by looking at the images we currently have:

```bash
podman image list
```

Suppose we decide to remove the `hello` image.
We can do that using the `image rm` command, where `rm` is short for *remove*:

```bash
podman image rm quay.io/podman/hello
```

However, this doesn't succeed.
Podman reports an error because the image is still being used by one or more containers.
Before an image can be deleted, every container based on that image must first be removed.

To see the containers currently on our system, including those that have already stopped, run:

```bash
podman container list -a
```

Our first container was given a randomly generated name by Podman.
Remove it using the name shown on your own system. For example:

```bash
podman container rm confident_solomon
```

Your container will almost certainly have a different name.
If the command succeeds, Podman prints the container's unique identifier,
confirming that it has been removed.

We can then remove the second container, which we explicitly named `hello_world`:

```bash
podman container rm hello_world
```

Verify that both containers have been removed:

```bash
podman container list -a
```

With no remaining containers using the image, we can now remove the image itself:

```bash
podman image rm quay.io/podman/hello
```

Podman confirms that the image has been deleted.
We can verify this by listing the remaining images:

```bash
podman image list
```

At this point we've removed all of the containers and images created
during the earlier exercises.

::::::::::::::::::::::::::::: instructor

Who has successfully removed their containers and images?

::::::::::::::::::::::::::::::::::::::::


## Running a Web Server Container

So far we've only run a simple "Hello World" container.
Let's now run something a little more useful by starting a web server inside a container.

Type in the following command, but do not yet press `Enter`:

```bash
podman run --name webserver -p 8080:80/tcp docker.io/library/httpd
```

Several things are happening here:

- `--name webserver` gives the container a meaningful name, which we've seen before.
- `docker.io/library/httpd` specifies the image to download and run.
  In this case the image comes from the Docker Hub (`docker.io`) registry,
  demonstrating that Podman can use Docker images directly.
- The image contains an *Apache HTTP Server*, `httpd`.
- The `-p` option publishes a network port, allowing software running
  inside the container to be accessed from the host machine.

The port mapping is written as `host-port:container-port/protocol`. In this example:

- The web server listens on *port 80* inside the container
- That port is mapped to *port 8080* on the host
- The connection uses the *TCP* protocol

Web servers typically listen on port 80, but using port 8080 on the host
avoids conflicts with any web server already running on your machine and
avoids the elevated privileges often required for low-numbered ports.

Now press `Enter` to download and start the container.

Unlike our `hello` example, this container continues running.
The terminal remains attached to the container,
displaying the Apache log output as requests are received.

### Accessing the Web Server

Once the container is running, open a web browser and visit http://localhost:8080

You should see a page displaying the message `It works!`.

If you watch the terminal running the container, you'll also see Apache log the request.
This is exactly what we'd expect from a normal web server.

You can also retrieve the page directly from another terminal.
Start another terminal, but be careful not to resize the terminal with the running webserver within it,
since this may send `SIGWINCH` (signal of window change) to that terminal,
and cause the container to exit.

On macOS, Windows, or Linux with `curl` installed, run:

```bash
curl http://localhost:8080
```

The HTML source of the page is displayed in the terminal:

```output
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<title>It works! Apache httpd</title>
</head>
<body>
<p>It works!</p>
</body>
</html>
```

If `curl` is not available, Linux users can use `wget` instead:

```bash
wget -nv -O - http://localhost:8080
```

The additional options tell `wget` to:

- Suppress most status output (`-nv`)
- Write the downloaded page to standard output instead of a file (`-O -`)

The result is essentially the same HTML page, together with a brief
status message.

::::::::::::::::::::::::::::: instructor

Who has successfully run the web server container and viewed or downloaded the web page?

::::::::::::::::::::::::::::::::::::::::


### Running Containers in the Background

Having the web server occupy an entire terminal isn't always convenient.
Fortunately, containers can also run in **detached mode**, allowing them
to continue running in the background.

First, stop the existing container from another terminal:

```bash
podman stop webserver
```

This uses the shortcut form of `podman container stop`. The terminal
running the web server should display log messages indicating that Apache
is shutting down, after which the container exits.

We can then remove the stopped container:

```bash
podman rm webserver
```

Now from within the original terminal that we used to start the container,
rerun the web server in detached mode:

```bash
podman run --name webserver -dt -p 8080:80/tcp docker.io/library/httpd
```

The additional `-dt` options change how the container runs:

- `-d` starts the container in the background, in a *detached* state
- `-t` allocates a pseudo-terminal so that the container's output is retained internally for logging purposes

This time the command immediately returns to the shell while the web server continues running in the background,
leaving the terminal free for other work.

::::::::::::::::::::::::::::: instructor

Who's been able to run the webserver container in detached mode?

::::::::::::::::::::::::::::::::::::::::


### Viewing Container Logs

When a container is running in detached mode, it no longer writes its output directly to the terminal.
While this keeps the terminal free for other work,
it also means we can no longer see what the application inside the container is doing.

Fortunately, Podman stores the container's output and allows us to view it at any time using the `logs` command:

```bash
podman logs webserver
```

This displays the web server's log output,
allowing us to inspect recent activity without needing to restart or reattach to the container.

### Running Commands Inside a Running Container

Viewing logs is useful, but sometimes we need much greater access to a running container.
For example, we may want to inspect its filesystem, check configuration files, or run diagnostic commands.

Podman provides the `exec` command, which executes a command inside an
already running container.

For example, to list the contents of the root directory of the container, run:

```bash
podman exec -ti webserver /bin/ls /
```

```output
bin   dev  home  media	opt   root  sbin  sys  usr
boot  etc  lib	 mnt	proc  run   srv   tmp  var
```

Here:

- `exec` executes a command inside the container
- `-t` allocates a terminal
- `-i` keeps standard input open, allowing interactive commands

We can also see the container's current working directory:

```bash
podman exec -ti webserver /bin/pwd
```

```output
/usr/local/apache2
```

This shows that commands execute from the Apache web server's working
directory.

### Opening an Interactive Shell

Running individual commands with `podman exec` is useful,
but repeatedly typing long commands soon becomes tedious.

Instead, we can start an interactive shell inside the container:

```bash
podman exec -ti webserver /bin/bash
```

```output
root@e7af92db948c:/usr/local/apache2# 
```

This gives us a shell prompt inside the running container,
allowing us to explore it much as we would a remote machine accessed via SSH.

Notice that we are logged in as the **root** user within the container,
so we have full administrative privileges over the container's filesystem.

For example, we can locate the web page that Apache is serving:

```bash
ls
```

```output
bin  build  cgi-bin  conf  error  htdocs  icons  include  logs	modules
```

```bash
cd htdocs
```

We can also look at the HTML document that was served to us earlier:

```bash
cat index.html
```

```output
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<title>It works! Apache httpd</title>
</head>
<body>
<p>It works!</p>
</body>
</html>
```

When you've finished exploring, exit the shell as normal:

```bash
exit
```

You'll be returned to your host machine's command prompt.

::::::::::::::::::::::::::::: instructor

Who has successfully opened an interactive shell, explored the container, run a few commands, and exited again?

::::::::::::::::::::::::::::::::::::::::

### Stopping and Removing the Container

Because the web server is running in detached mode, we can stop it from the same terminal:

```bash
podman stop webserver
```

Once it has stopped, remove it as before:

```bash
podman container rm webserver
```

You can verify that it has been removed by listing all containers:

```bash
podman container list -a
```

The `webserver` container should no longer appear in the list.


## Sharing Files Between the Host and a Container

So far we've treated containers as self-contained environments.
However, real-world applications almost always need to exchange data with
their host system.

For example, suppose we're running a scientific analysis inside a
container because it provides the correct software environment. We'll
need a way to provide the analysis with input data and retrieve the
results afterwards.

Fortunately, Podman makes this straightforward.

Just as we previously mapped a host network port to a container port,
we can also mount a directory from the host machine inside the container.
This allows the host and container to access the same files.
This is known as a *bind mount*.

Let's demonstrate this by serving our own web page from the host machine using the Apache web server container.

### Create a Web Content Directory on the Host

First, create a directory to hold our web pages:

```bash
mkdir webdocs
```

Next, create a simple HTML page inside that directory using your preferred text editor.

For example, using Nano:

```bash
nano webdocs/index.html
```

Add a very simple page:

```html
<html><body>Hello there!</body></html>
```

Feel free to replace the text with your own message.

If you're using Nano, press `Ctrl+O` to save the file and `Ctrl+X` to exit.
On Windows, you can use Notepad or any other text editor to create the same file.

::::::::::::::::::::::::::::: instructor

Who's created a webdocs directory and put a short html file in it?

::::::::::::::::::::::::::::::::::::::::

### Mounting a Host Directory

We can now start a new web server container,
this time giving it access to the `webdocs` directory we created on our host machine.

The command differs slightly depending on your operating system,
because we'll use an environment variable to refer to our current working directory.
Windows and Linux/macOS use different syntax for environment variables.

If you're using Windows with the Command Prompt, use:

```bash
podman run --name webserver -v %CD%/webdocs:/usr/local/apache2/htdocs -dt -p 8080:80/tcp docker.io/library/httpd
```

If you're using Linux, MacOS, or Windows with Git Bash, use:

```bash
podman run --name webserver -v $PWD/webdocs:/usr/local/apache2/htdocs -dt -p 8080:80/tcp docker.io/library/httpd
```

Don't press `Enter` immediately, let's first examine the new part of the command.

The command is almost identical to the one we used previously,
except for the addition of the `-v` option.
This option creates a *volume mount*, allowing a directory on the host machine to be accessed from within the container.

The general form is: `-v <host-directory>:<container-directory>`

The first path specifies the directory on the host,
while the second specifies where that directory will appear inside the container.

For the host directory, we need to provide its full path.
Rather than typing this manually, we use an environment variable representing the current directory:

- `$PWD` on Linux, MacOS, and Git Bash
- `%CD%` on Windows Command Prompt

Appending `/webdocs` gives the full path to the directory we created earlier.

The second path `/usr/local/apache2/htdocs` is where the Apache web server stores the web pages it serves.
By mounting our `webdocs` directory here, the container sees our local files instead of its own default web pages.

In effect, whenever Apache reads files from its `htdocs` directory,
it is actually reading the contents of the `webdocs` directory on the host machine.

Now press `Enter` to start the container.

As before, Podman immediately returns the container's unique identifier,
indicating that the container has started successfully in detached mode.

### Verifying the Mounted Files

To confirm that the container is serving our own web page, visit `http://localhost:8080` in a web browser.

Alternatively, you can retrieve the page from the command line using `curl`:

```bash
curl http://localhost:8080
```

This time, instead of the default Apache "It works!" page,
you should see the HTML that you created in `webdocs/index.html`.

This demonstrates one of the most useful features of containers:
applications running inside a container can work directly with files on the host machine,
making it easy to edit data locally while software inside the container uses it immediately.

::::::::::::::::::::::::::::::::::::::::::  callout

## An Alternative: Data Volumes

Volumes provide another way to persist data beyond the lifetime of a container.
Unlike bind mounts, which link a directory on your computer to a directory inside a container,
volumes are created and managed by the container engine.

This means you don't need to specify or manage a location on your computer's filesystem,
and volumes can be shared between containers and retain their data even when the containers using them are removed.
They are particularly useful for storing application data, such as databases,
that needs to persist independently of individual containers.

:::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: keypoints 

- `podman container list -a` shows all containers including stopped ones whilst `podman container list` shows only running containers.
- An exit code of `0` means a container completed successfully, whereas a non-zero exit code indicates an error.
- `podman run` always creates a new container, and `podman container start` restarts an existing stopped container.
- The `-p host-port:container-port/protocol` option maps a port inside a container to a port on the host, enabling access to services running in the container.
- The `-d` flag runs a container in detached (background) mode.
- `podman logs <name>` retrieves the log output from a running (background) container.
- `podman exec -ti <container> /bin/bash` opens an interactive shell inside a running container.
- The `-v <host-dir>:<container-dir>` option mounts a host directory into the container, allowing both to access the same files.
- All containers based on an image must be removed before that image can be deleted.

::::::::::::::::::::::::::::::::::::::::::::::::
