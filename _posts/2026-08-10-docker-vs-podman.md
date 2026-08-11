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

# Docker vs Podman

Your `docker run nginx -p 80:80` command works perfectly. Until the Docker daemon crashes, you lose all container management capabilities. That's it, a single point of failure.

Containerization powers modern development, but the landscape has evolved beyond Docker's early dominance. Teams now seek better security, simpler architecture, and native Kubernetes alignment. Podman has emerged as a serious alternative in this space. This post breaks down the architectural differences between Docker and Podman. You will learn why one is ideal for laptops while the other wins on Linux servers, and why Docker remains everywhere despite Podman's daemonless design.

You will learn how each runtime works under the hood, the security implications of running as root, and practical guidance on choosing between them based on your workflow.

---

## How Docker Works: The Daemon-First Architecture

The core difference between Docker and Podman lies in their architecture. When you run a `docker run` command, your command does not create the container directly. Instead, it sends a request to the Docker daemon. The Docker daemon is a background process that runs continuously on your machine and listens for commands.

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

The daemon then uses supporting tools (such as runc) to make system calls to the Linux kernel. These tools create isolated namespaces for network and file systems. This multi-layer approach ensures containers get their own view of resources, but it introduces a critical dependency: the daemon must be alive for everything to work.

*Practical Advice*: If your Docker daemon crashes, all container management stops. Existing containers may keep running but become unmanageable until the daemon recovers:

```bash
# Docker daemon is down — you are locked out of container management
$ docker ps
❌ docker: error while connecting to the daemon: connect: refuse connection
```

```bash
# You cannot even stop running containers
$ docker stop my-app
❌ docker: error while connecting to the daemon...
```
## How Podman Works: The Daemonless Alternative
Podman eliminates the daemon entirely. When you run a podman run command, Podman works directly with lower-level tools like crun (Container Runtime Universally). These tools interact straight with the Linux kernel to provision container resources.

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

Since there is no central daemon managing everything, Podman is called daemonless. This architectural shift has significant consequences.

| Aspect | Docker | Podman |
|---|---|---|
| Architecture | Daemon-first (client-server) | Daemonless (direct kernel calls) |
| Process Model | CLI → Daemon → Tools → Kernel | CLI → Tools → Kernel |
| Root Requirement | Requires root privileges for daemon | Runs as regular user |
| Single Point of Failure | Yes (daemon) | No |
| Socket Usage | Unix socket /var/run/docker.sock | No daemon socket required |

Analysis and Implications
The daemonless design means each container command operates independently. This improves resilience but requires understanding how the tools interact directly with kernel primitives like namespaces and cgroups.

<pre class="mermaid">
flowchart LR
    A[Docker Run] --> B{Daemon Alive?}
    B--No--> C[All operations fail]
    B--Yes--> D[runc allocates resources]
    D --> E[Container starts]

    F[Podman Run] --> G[crun allocates resources]
    G --> H[Container starts]
</pre>

## Security Differences: Root vs Non-Root Execution
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

## A Concrete Example of the Problem
Consider a compromised container running as root in Docker:

```bash
# Inside a compromised Docker container with root privileges
$ whoami
root
# This container can access the host filesystem
$ mount -t bind / /host/mount/
# This container can potentially escape to the host
$ nsenter --mount -t $$ -p /proc/1/ns/mnt
```

With Podman, the same compromised container is constrained by the regular user's permissions:

```bash
# Inside a compromised Podman container running as regular user
$ whoami
eduardo
# Attempting to mount host filesystem fails
$ mount -t bind / /host/mount/
❌ permission denied
```

Podman was explicitly designed to work as a regular non-root user. This reduces the blast radius significantly. If a container is compromised, the damage is limited to whatever privileges the regular user has.


## Kubernetes Alignment and Practical Migration
Podman also borrows the concept of pods from Kubernetes. You can group related containers so they share resources, making Podman a natural stepping stone if you are heading toward Kubernetes.

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

    Pod "1" *-- " *" Container
</pre>

The migration path is surprisingly smooth. docker run becomes podman run. docker build becomes podman build. Many users just create an alias so typing "Docker" actually runs Podman, preserving their muscle memory:

# Simple alias for seamless migration

```bash
echo 'alias docker=podman' >> ~/.zshrc
# Now your existing scripts work unchanged
$ docker run nginx:alpine
# Actually runs podman under the hood
```

Step-by-Step Migration Guide
1. Install Podman on your system
2. Test basic operations: podman run, podman ps, podman stop
3. Create a Docker alias if you want to preserve your typing habits
4. Configure project-specific settings: registry, volumes, network

```bash
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
```

## Conclusion
The choice between Docker and Podman is not about which is better. It is about what fits your workflow.

- Docker's daemon-based architecture provides a polished, familiar experience that is ideal for development on laptops and Macs.
- Podman's daemonless design offers better security, simpler architecture, and native Kubernetes alignment for Linux servers.
- The CLI compatibility between Docker and Podman enables near-zero friction migration.

And as always, happy coding!
