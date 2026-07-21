---
title: "(Optional) Container Registries"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do I check which container registries Podman is configured to search?
- How do I configure Podman to search a registry on Linux?
- How do I search for container images from the command line?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Recall the command used to display Podman's configuration, including configured search registries.
- Explain why registry search may need to be configured manually on Linux but not on macOS or Windows.
- Create a `registries.conf` file to configure a search registry on Linux.
- Use `podman search` to find container images from the command line.

::::::::::::::::::::::::::::::::::::::::::::::::

## Searching Container Registries

The final topic we'll cover is searching container registries from the command line.

Earlier we browsed the `quay.io` registry using a web browser.
Podman also allows us to search registries directly from the command line using the `search` command.

However, depending on your operating system, this feature may not be configured by default.
On Windows and MacOS, Podman is typically configured to search the Docker Hub (`docker.io`) automatically.
On many Linux installations, however, you'll first need to specify which registries Podman should search.

### Checking Your Registry Configuration

To inspect your Podman configuration, run:

```bash
podman info
```

This command displays a large amount of information about your Podman installation.

Look for the **registries** section. If registry searching has already been configured,
you should see something similar to:

```output
search:
  - docker.io
```

If no search registries are listed, the `podman search` command will not return any results until a registry has been configured.

### Configuring Registry Search (Linux)

If you're using Linux and no search registries are configured,
you'll need to create a simple configuration file.

Podman looks for user-specific configuration inside the `.config` directory within your home directory.

First, inspect the contents of that directory:

```bash
ls ~/.config/
```

If a `containers` directory already exists, you can use it.
Otherwise, create it:

```bash
cd ~/.config
mkdir containers
cd containers
```

If a file named `registries.conf` already exists, you can edit it.
Otherwise, create a new one:

```bash
nano registries.conf
```

Add the following configuration:

```toml
unqualified-search-registries = ["docker.io"]

[[registry]]
location = "docker.io"
```

Save the file using `Ctrl+O` and exit Nano wih `Ctrl+X`.


## Searching for Images

With the registry configured, you can search for container images directly from the command line.

For example:

```bash
podman search webserver
```

Podman searches the configured registry and returns a list of images whose names or descriptions match the search term.

You are not limited to Docker Hub.
If you regularly use other registries, such as `quay.io`,
you can add them to your configuration so that Podman searches multiple registries automatically.


::::::::::::::::::::::::::::::::::::: keypoints 

- `podman info` displays Podman's full configuration, including which registries are configured for searching.
- On macOS and Windows, Docker Hub is configured as a search registry by default; on Linux this typically requires manual configuration.
- Registry search is configured by editing `~/.config/containers/registries.conf`.
- `podman search <term>` returns images from all configured registries whose names or descriptions match the search term.

::::::::::::::::::::::::::::::::::::::::::::::::
