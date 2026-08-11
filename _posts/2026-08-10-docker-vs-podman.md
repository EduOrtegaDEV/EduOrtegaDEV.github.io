---
title: "Docker vs. Podman"
date: 2026-08-10
categories:
  - containers
  - devops
tags:
  - docker
  - podman
  - kubernetes
  - containerization
---
<script src="https://cdn.jsdelivr.net/npm/mermaid@11.16.1/dist/mermaid.min.js"></script>

Your `docker run nginx -p 80:80` command works perfectly. Until the Docker daemon crashes, you lose all container management capabilities. That's it — a single point of failure.

Containerization powers modern development, but the landscape has moved beyond Docker's early dominance. Teams now want better security, simpler architecture, and native Kubernetes alignment. Podman has emerged as a serious alternative.

This post breaks down the architectural differences between Docker and Podman, why one is ideal for laptops while the other wins on Linux servers, and how each runtime works under the hood.

## How Docker Works: The Daemon-First Architecture

The core difference between Docker and Podman is their architecture. When you run a `docker run` command, your command doesn't create the container directly. Instead, it sends a request to the Docker daemon — a background process that runs continuously on your machine and listens for commands.

<pre class="mermaid">
sequenceDiagram
    participant CLI as $ docker run nginx
    participant Client as Docker CLI
    participant Daemon as Docker Daemon (socket)
    participant Tools as runc, libcontainer
    participant Kernel as Linux Kernel

    CLI->>Client: "docker run nginx"
    Client->>Daemon: POST /containers/create (via socket)
    Daemon->>Tools: "allocate resources"
    Tools-->>Daemon: "resources ready"
    Daemon->>Kernel: namespace + cgroup setup
    Kernel-->>Tools: "containers created"
    Tools-->>Daemon: "container started"
    Daemon-->>Client: 201 Created
</pre>

The daemon then uses supporting tools (runc, libcontainer) to make system calls to the Linux kernel. These tools create isolated namespaces for network and file systems, so containers get their own view of resources.

The catch: the daemon must be alive for everything to work. If it crashes, container management stops dead.

</pre>bash
# Docker daemon is down — you are locked out of container management
$ docker ps
❌ docker: error while connecting to the daemon: connect: refuse connection

# You cannot even stop running containers
$ docker stop my-app
❌ docker: error while connecting to the daemon...
</pre>

## How Podman Works: The Daemonless Alternative

Podman eliminates the daemon entirely. When you run a `podman run` command, Podman works directly with lower-level tools like crun (Container Runtime Universally). These tools interact straight with the Linux kernel to provision container resources.

<pre class="mermaid">
graph TD
    subgraph Docker Architecture
        A[User] --> B[Docker CLI]
        B --> C{Daemon}
        C --> D[runc / libcontainer]
        D --> E[Linux Kernel]
    end

    subgraph Podman Architecture
        F[User] --> G[Podman CLI]
        G --> H[crun / runc]
        H --> I[Linux Kernel]
    end

    style B fill:#e1f5fe,stroke:#01579b
    style F fill:#e1f5fe,stroke:#01579b
</pre>

No central daemon managing everything. Each container command operates independently, which improves resilience but requires understanding how the tools interact directly with kernel primitives like namespaces and cgroups.

| Aspect | Docker | Podman |
|---|---|---|
| Architecture | Daemon-first (client-server) | Daemonless (direct kernel calls) |
| Process Model | CLI → Daemon → Tools → Kernel | CLI → Tools → Kernel |
| Root Requirement | Requires root privileges for daemon | Runs as regular user |
| Single Point of Failure | Yes (daemon) | No |
| Socket Usage | Unix socket /var/run/docker.sock | No daemon socket required |

The architecture change means each container command works independently.

<pre class="mermaid">
flowchart LR
    A[Docker Run] --> B{Daemon Alive?}
    B--No--> C[All operations fail]
    B--Yes--> D[runc allocates resources]
    D --> E[Container starts]

    F[Podman Run] --> G[crun allocates resources]
    G --> H[Container starts]
</pre>

## Security: Root vs Non-Root Execution

The security implications of these architectural choices are substantial. The Docker daemon traditionally runs with root privileges. Anyone who can control the daemon has potentially powerful access to the host system.

<pre class="mermaid">
graph TD
    subgraph Docker Privilege Chain
        A[Container Compromised] --> B{Root Access to Host?}
        B--Yes--> C[Full Host Compromise]
    end

    subgraph Podman Privilege Chain
        D[Container Compromised] --> E[Regular User Access]
        E --> F[Limited Host Impact]
    end

    style C fill:#ff6b6b,stroke:#ff4757
    style F fill:#2ecc71,stroke:#27ae60
</pre>

Here's what that actually looks like in practice:

</pre>bash
# Inside a compromised Docker container with root privileges
$ whoami
root

# This container can access the host filesystem
$ mount -t bind / /host/mount/

# This container can potentially escape to the host
$ nsenter --mount -t $$ -p /proc/1/ns/mnt
</pre>

Now try the same in Podman:

</pre>bash
# Inside a compromised Podman container running as regular user
$ whoami
eduardo

# Attempting to mount host filesystem fails
$ mount -t bind / /host/mount/
❌ permission denied
</pre>

Podman was explicitly designed to work as a non-root user. If a container is compromised, the damage is limited to whatever privileges the regular user has.

## Kubernetes Alignment and Practical Migration

Podman borrows the concept of pods from Kubernetes. You can group related containers so they share resources, making Podman a natural stepping stone if you're heading toward Kubernetes.

<pre class="mermaid">
classDiagram
    class Container {
        -int id
        +String name
        +String image
        +List resources
        +start()
        +stop()
    }

    class Pod {
        -List containerRefs
        -String name
        +SharedNetworkSpace network
        +SharedVolumeStore volumes
        
        Container* containers
        +startAll()
        +stopAll()
    }

    Pod "1" *-- "*" Container
</pre>

The migration path is smooth. `docker run` becomes `podman run`. `docker build` becomes `podman build`. Many users just create an alias so typing "Docker" actually runs Podman, preserving their muscle memory:

</pre>bash
# Simple alias for seamless migration

echo 'alias docker=podman' >> ~/.zshrc
# Now your existing scripts work unchanged
$ docker run nginx:alpine
# Actually runs podman under the hood
</pre>

**Migration checklist:**

1. Install Podman on your system
2. Test basic operations: `podman run`, `podman ps`, `podman stop`
3. Create a Docker alias if you want to preserve your typing habits
4. Configure project-specific settings: registry, volumes, network

</pre>bash
# Verify Podman is installed and running
$ podman --version
podman version 4.6, build abc123, 2026-08-09

# Test a basic container run
$ podman run -d --name test-nginx nginx:alpine
# Container started successfully

$ podman ps
NAME   IMAGE      STATUS    UP    PORTS
test-nginx  nginx:alpine  Up     0s   80/tcp

$ podman stop test-nginx
# Container stopped successfully
</pre>

## So Which One Should You Use?

It's not about which is objectively better. It's about what fits your workflow.

- **Docker** — the daemon-based architecture provides a polished, familiar experience that is ideal for development on laptops and Macs.
- **Podman** — the daemonless design offers better security, simpler architecture, and native Kubernetes alignment for Linux servers.
- **The CLI compatibility** between Docker and Podman enables near-zero friction migration if you want to switch.

Both tools do the same job — run containers. Docker got there first and built a polished platform around it. Podman arrived later with a cleaner architecture that better reflects how containers should work on Linux in the first place.

If you're on a Mac with Docker Desktop, stick with Docker for now. If you're on Linux and want something simpler to manage, Podman is worth the switch.
