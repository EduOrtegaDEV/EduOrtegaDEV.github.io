---
title: "Developing with Dev Containers in VSCode — Using Podman instead of Docker"
date: 2025-10-20
categories:
  - development
tags:
  - vscode
  - devcontainers
---

One of the handiest features in Visual Studio Code is Dev Containers. They allow you to define a complete development environment using containers so that you can have consistency across teams and machines. There is one limitation, though: official support is limited to Docker. Microsoft does mention that other Docker-compatible CLIs should work, but they aren't officially supported.

That said, I prefer Podman for several reasons, mainly because it's daemonless and rootless. So, yes, you can use Podman with Dev Containers. Here's how to set it up and code in containers without Docker.

## Configuring Dev Containers with Podman

### Step 1: Install the Dev Containers Extension
Get the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) from the VSCode Marketplace.

### Step 2: Configure the Extension to Use Podman
- Click on the extension settings gear icon.
- Search for Docker Path and Docker Compose Path.
- Set the paths to your Podman and Podman Compose binaries. For example:

```
Docker Path: /usr/bin/podman
Docker Compose Path: /usr/bin/podman-compose
```

### Step 3: Add Dev Container Configuration Files
- Open the Command Palette (F1) and run: `Dev Containers: Add Dev Container Configuration Files`.
- Choose the stack or environment you require (Node.js, Python, .NET, etc.).

### Step 4: Personalize Your Dev Container Configuration
This command creates a .devcontainer folder with a devcontainer.json file. You can use the default image or build your own. Here is how you can build a custom image:
- Create a Dockerfile. Example base image:
```dockerfile
FROM mcr.microsoft.com/devcontainers/dotnet:1-8.0
```
- Update your devcontainer.json by replacing the `image` attribute for the `build` attribute:
```json
"build": {
  "dockerfile": "Dockerfile"
}
```

### Step 5: Rebuild and Reopen in Container

Use the Command Palette again:
`Dev Containers: Rebuild and Reopen in Container`

Or push the green `><` button in the bottom left and select `Open Container Configuration File`.

### Step 6: Install Extensions Inside the Container
You can install VSCode extensions within the container. This keeps your host clean and your container self-contained.

### Step 6: Start Coding!
Your code is on your host machine but runs inside the container. You get all the benefits of containerized development—isolated dependencies, reproducible environments, and easy Git integration.

### Bonus: Multi-Service Development with Docker Compose
Need more than one service? No problem. You can add a docker-compose.yml file to your project. Dev Containers will automatically boot all the defined services when you open the workspace.

## Conclusion
Although the use of Podman with Dev Containers is not officially supported, it works beautifully with a little setup. If you are building microservices, experimenting with new stacks, or you just like a fresh dev environment, this combination offers flexibility and power.

Happy coding!
