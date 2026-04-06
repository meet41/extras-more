# COMPLETE DOCKER LEARNING GUIDE
## From Beginner to Production-Ready Expert

---

# TABLE OF CONTENTS

1. Introduction to Docker
2. Docker Architecture & Components
3. Images & Containers Fundamentals
4. Docker Installation & Setup
5. Docker CLI Basics
6. Dockerfile: Building Your Own Images
7. Building Docker Images
8. Running Containers
9. Docker Volumes & Persistent Storage
10. Docker Networks
11. Environment Variables & Configuration
12. Port Mapping & Service Exposure
13. Docker Logs & Debugging
14. Container Lifecycle Management
15. Image Optimization & Multi-stage Builds
16. Docker Registry & Image Distribution
17. Docker Compose for Multi-container Apps
18. Docker Best Practices & Production Patterns
19. Project 1: Dockerizing a FastAPI Backend
20. Project 2: Multi-container App (Backend + MySQL + Redis)
21. Project 3: Background Job Processing with Celery
22. Real-world Project 1: Microservices Architecture
23. Real-world Project 2: Real-time Job Processing System
24. Deployment Strategies & CI/CD Integration
25. Container Orchestration Introduction (Kubernetes Basics)
26. Troubleshooting & Debugging Guide
27. Exercises & Mini Challenges

---

# SECTION 1: INTRODUCTION TO DOCKER

## 1.1 What is Containerization?

Containerization is a lightweight virtualization technique that packages an application along with all its dependencies (libraries, runtime, system tools) into a self-contained unit called a **container**.

### Think of it this way:
**Traditional Shipping:** Before containers existed, goods were shipped in different ways, causing inefficiency and delays. Now, everything is packed in standardized containers (20ft, 40ft) that can be transported consistently.

**Similarly in software:**
- Before containers: Each server had different setups, dependencies, versions
- With containers: Your app, libraries, and runtime travel together as one unit

### Key Characteristics:
- **Lightweight:** Only includes what's needed (MB vs GB)
- **Portable:** Works on any machine with container runtime
- **Isolated:** Each container has its own filesystem, processes, and networks
- **Fast:** Starts in seconds (vs minutes for VMs)

---

## 1.2 Containers vs Virtual Machines (VMs)

### Virtual Machines:
```
┌─────────────┐
│  Hardware   │
├─────────────┤
│ Hypervisor  │
├─────────────┤
│ OS | OS | OS│  ← Multiple full operating systems
├─────────────┤
│ App | App   │
└─────────────┘

Size: ~700 MB to 1 GB each
Boot time: 30-40 seconds
```

### Containers:
```
┌─────────────┐
│  Hardware   │
├─────────────┤
│ Host OS     │  ← Single shared OS kernel
├─────────────┤
│ Container Runtime (Docker Engine)
├─────────────┤
│App | App|App│  ← Lightweight isolated environments
└─────────────┘

Size: 10-100 MB each
Boot time: <1 second
```

### Comparison Table:

| Feature | Virtual Machine | Container |
|---------|-----------------|-----------|
| **Isolation** | Complete OS isolation | Process/Kernel isolation |
| **Size** | 700 MB - 1+ GB | 10-100 MB |
| **Boot Time** | 30-40 seconds | < 1 second |
| **Performance** | Overhead due to full OS | Near-native performance |
| **Density** | Run 5-10 VMs per host | Run 100s of containers |
| **Startup** | Slower due to OS boot | Instant startup |

---

## 1.3 Why Docker is Important in Modern Development

### Problem Docker Solves:
**"It works on my machine but not in production!"**

### Key Benefits:

#### 1. **Consistency Across Environments**
- Development = Testing = Production (same container)
- No more "works on my laptop" issues

#### 2. **Microservices Architecture**
- Break monolithic apps into smaller, independently deployable services
- Scale individual components as needed

#### 3. **Rapid Deployment**
- Package once, deploy everywhere
- Reduces deployment time from hours to minutes

#### 4. **Resource Efficiency**
- Run multiple containers on single host
- Better utilization than VMs

#### 5. **Scalability**
- Easily spin up/down containers based on demand
- Perfect for cloud-native systems

#### 6. **DevOps & CI/CD Integration**
- Automate builds, tests, and deployments
- Consistent pipeline from code to production

---

## 1.4 Real-World Use Cases

### Startup Scenario:
```
Day 1: Single FastAPI app + MySQL database
  - Docker: 2 containers (backend + DB)
  - Create docker-compose.yml, it works everywhere
  
Day 30: Need caching layer
  - Add Redis container
  - Update docker-compose.yml, done!

Day 60: Processing background jobs
  - Add Celery worker + message broker
  - All containers orchestrated together
```

### Production System Example:
```
E-commerce Platform Architecture:

┌─────────────────────────────────────────┐
│         Load Balancer                   │
└──────────────────┬──────────────────────┘
                   │
    ┌──────────────┼──────────────────┐
    │              │                  │
┌───▼──┐      ┌───▼──┐          ┌───▼──┐
│ API  │      │ API  │          │ API  │
│Cont-1│      │Cont-2│ .... │Cont-N│
└──────┘      └──────┘          └──────┘
    │              │                  │
    └──────────────┼──────────────────┘
                   │
        ┌──────────┼──────────┐
        │          │          │
    ┌──▼───┐  ┌──▼───┐  ┌──▼───┐
    │MySQL │  │Redis │  │Queue │
    │      │  │      │  │      │
    └──────┘  └──────┘  └──────┘
```

Multiple container instances for:
- Load balancing and high availability
- Horizontal scaling
- Fault tolerance
- Zero-downtime deployments

---

## 1.5 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ What containerization is and why it matters
- ✓ How containers differ from VMs
- ✓ Real-world benefits and use cases
- ✓ Why Docker is central to modern DevOps

---

# SECTION 2: DOCKER ARCHITECTURE & COMPONENTS

## 2.1 Docker Architecture Overview

```
┌──────────────────────────────────────────────────┐
│                 DOCKER CLIENT                     │
│  (Command: docker run, docker build, etc.)       │
└─────────────────────────┬──────────────────────────┘
                          │ (REST API)
                          │
┌─────────────────────────▼──────────────────────────┐
│             DOCKER DAEMON (dockerd)                │
│  Manages containers, images, volumes, networks     │
└─────────────────────────┬──────────────────────────┘
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    ┌───▼───┐      ┌─────▼─────┐     ┌────▼────┐
    │Images │      │ Containers │     │ Registry │
    │Storage│      │ Runtime    │     │ (Hub)    │
    └───────┘      └────────────┘     └──────────┘
```

## 2.2 Key Components

### 1. **Docker Client**
- Command-line interface you interact with
- Sends commands to Docker Daemon
- Examples: `docker run`, `docker build`

```bash
# Client sends request
docker run ubuntu:latest
      ↓ (via REST API)
    Daemon executes
```

### 2. **Docker Daemon (dockerd)**
- Background service that manages Docker objects
- Creates and runs containers
- Manages images, volumes, networks
- Continuously running on your machine

### 3. **Docker Engine**
- Complete package (Client + Daemon + Runtime)
- Installed when you install Docker

### 4. **Container Runtime**
- Low-level component executing containers
- containerd: Default container runtime
- Manages actual isolation using kernel features

### 5. **Docker Images**
- **Blueprint/Template** for containers
- Contains everything needed to run application:
  - Base OS
  - Runtime (Python, Node, Java, etc.)
  - Libraries and dependencies
  - Application code
  - Configuration

Think: Image = Class, Container = Object (instance)

### 6. **Docker Containers**
- **Running instance** of an image
- Isolated process environment
- Has its own:
  - Filesystem
  - Process namespace
  - Network namespace
  - Resource limits (memory, CPU)

### 7. **Docker Registry**
- **Storage and distribution** of images
- Docker Hub: Free, public registry
- Private registries: AWS ECR, Azure ACR, Artifactory

---

## 2.3 How Components Work Together

### Step-by-step Workflow:

```
1. You type command:
   $ docker run nginx:latest

2. Docker Client (CLI) receives command
   
3. Client sends request to Daemon:
   "Please run image: nginx:latest"
   
4. Daemon checks local image storage
   - Image found? Skip to 7
   - Image not found? Go to 5

5. Daemon contacts Registry (Docker Hub)
   - Downloads image nginx:latest
   - Stores in local image storage

6. Daemon unpacks image layers

7. Daemon uses Container Runtime to:
   - Create filesystem from image
   - Set up network
   - Allocate resources
   - Start process

8. Container is running!

9. You can interact via Client:
   docker ps    (list containers)
   docker logs  (view output)
   docker stop  (stop container)
```

---

## 2.4 Image Layers & How They Work

### Docker Image Structure:

Images are built in **layers**. Each instruction in Dockerfile creates a new layer.

```
Image: ubuntu:22.04 with Python app

┌─────────────────────────────────┐
│     Layer 5: COPY app.py        │ (your code)
├─────────────────────────────────┤
│     Layer 4: RUN pip install    │ (dependencies)
├─────────────────────────────────┤
│     Layer 3: RUN apt-get update │ (system setup)
├─────────────────────────────────┤
│     Layer 2: ENV variables      │ (configuration)
├─────────────────────────────────┤
│     Layer 1: FROM ubuntu:22.04  │ (base image)
└─────────────────────────────────┘
```

### Caching Mechanism:

Docker caches each layer. If you rebuild:
- Unchanged layers are reused (cached)
- Saves build time and bandwidth
- **Important:** Order matters! Put things that change least first.

```dockerfile
# Bad ordering (rebuilds everything if code changes):
COPY . /app
RUN pip install -r requirements.txt

# Good ordering (cache dependencies, only rebuild code if it changes):
RUN pip install -r requirements.txt
COPY . /app
```

---

## 2.5 Communication Between Components

### Internal Communication Flow:

```
┌─────────────────────────────────────────┐
│      User Interface (Terminal)           │
└────────────────┬────────────────────────┘
                 │ $ docker run python-app
                 │
┌────────────────▼────────────────────────┐
│        Docker Client (CLI)              │
│  Parses command                         │
└────────────────┬────────────────────────┘
                 │ REST API Call
                 │ (Default: Unix socket /var/run/docker.sock)
                 │
┌────────────────▼────────────────────────┐
│     Docker Daemon (dockerd)             │
│  - Checks image cache                   │
│  - Pulls from Registry if needed        │
│  - Creates container                    │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│    Container Runtime (containerd)       │
│  - Executes container                   │
│  - Manages isolation                    │
│  - Manages resources                    │
└────────────────┬────────────────────────┘
                 │
         ┌───────▼────────┐
         │ Running        │
         │ Container      │
         │ (Process)      │
         └────────────────┘
```

---

## 2.6 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Docker architecture and all major components
- ✓ Relationship between images and containers
- ✓ How Docker components communicate
- ✓ Image layers and caching mechanism
- ✓ How registry fits into the ecosystem

---

# SECTION 3: IMAGES & CONTAINERS FUNDAMENTALS

## 3.1 What is a Docker Image?

A Docker image is a **read-only template** that contains everything needed to run an application.

### Image Characteristics:
- **Immutable:** Once built, doesn't change
- **Layered:** Built from multiple layers stacked together
- **Portable:** Can run on any system with Docker
- **Reusable:** Share images across teams/organizations
- **Versioned:** Can tag images with versions

### What's Inside an Image?

```
Image ubuntu:22.04 contains:
│
├── Base Operating System
│   ├── Linux kernel references
│   ├── System libraries (/lib, /usr/lib)
│   ├── Package manager (apt)
│   └── Basic utilities
│
├── Runtimes (optional)
│   ├── Python 3.10
│   ├── npm/Node
│   └── Java JDK
│
├── Application Dependencies
│   ├── Flask/FastAPI
│   ├── Database drivers
│   ├── HTTP libraries
│   └── Other packages
│
├── Application Code
│   ├── Source files
│   ├── Configuration files
│   └── Assets
│
└── Metadata
    ├── Entry point
    ├── Environment variables
    ├── Exposed ports
    └── Working directory
```

---

## 3.2 What is a Docker Container?

A Docker container is a **running instance** of a Docker image.

### Container Characteristics:
- **Mutable:** Can write to container (changes apply to current instance)
- **Temporary:** By default, changes are lost when container stops
- **Isolated:** Own filesystem, processes, network
- **Lightweight:** Starts in milliseconds
- **Ephemeral:** Meant to be created and destroyed

### Analogy:
```
Image = Class (blueprint)
Container = Object (running instance)

class Dog:
    def __init__(self, name):
        self.name = name

Image: Dog class
Container 1: my_dog = Dog("Buddy")
Container 2: your_dog = Dog("Max")

Both are instances of the same class, but separate objects!
```

---

## 3.3 Key Differences Between Image and Container

| Aspect | Image | Container |
|--------|-------|-----------|
| **Definition** | Template/Blueprint | Running instance |
| **Mutable** | Immutable (read-only) | Mutable (can write) |
| **Storage** | Stored on disk | In memory + disk |
| **Lifetime** | Persistent | Ephemeral (typically) |
| **Size** | Larger (all layers) | Only changes layer added |
| **Persistence** | Data persists in image | Data lost when stopped (unless volume) |
| **Creation** | Built once | Multiple from same image |

### Visual Comparison:

```
Image Layer Structure:
┌─────────────────────────────────┐
│     Layer 3: Python files       │ (READ-ONLY)
├─────────────────────────────────┤
│     Layer 2: Dependencies       │ (READ-ONLY)
├─────────────────────────────────┤
│     Layer 1: Base Ubuntu        │ (READ-ONLY)
└─────────────────────────────────┘

Container (running instance):
┌─────────────────────────────────┐
│     Writable Layer              │ (NEW - just for this container)
├─────────────────────────────────┤
│     Layer 3: Python files       │ (from image)
├─────────────────────────────────┤
│     Layer 2: Dependencies       │ (from image)
├─────────────────────────────────┤
│     Layer 1: Base Ubuntu        │ (from image)
└─────────────────────────────────┘
```

---

## 3.4 Understanding Container Filesystem

### Copy-on-Write (CoW) Mechanism:

Containers use CoW to efficiently handle filesystem:

1. When container starts, it has read-only access to image layers
2. When you modify a file:
   - Copy from image layer to writable layer
   - Modify the copy
   - Original image remains unchanged
3. Other containers still see original files

```
Process:
1. Read file: Reads from image layer (fast, cached)
2. Write to file:
   ┌─────────────────────┐
   │  Check writable     │ ← File already here from image?
   │  layer              │
   │               NO    │
   └──────────┬──────────┘
              │
   ┌──────────▼──────────┐
   │  Copy file from     │ ← Copy from image layer
   │  image layer to     │
   │  writable layer     │
   └──────────┬──────────┘
              │
   ┌──────────▼──────────┐
   │  Modify in          │ ← Now modify the copy
   │  writable layer     │
   └─────────────────────┘
```

### Why This Matters:
- **Storage efficient:** Multiple containers share image layers
- **Performance:** Reading image files is fast (not copied)
- **Isolation:** Changes in one container don't affect others
- **Clean state:** Remove container = remove changes

---

## 3.5 Image Versioning & Tagging

### Image Naming Convention:

```
[REGISTRY]/[NAMESPACE]/[REPOSITORY]:[TAG]

Examples:
docker.io/library/python:3.11
└─ Registry: docker.io (Docker Hub)
└─ Namespace: library (official images)
└─ Repository: python
└─ Tag: 3.11 (version)

docker.io/mycompany/backend:latest
└─ Registry: docker.io
└─ Namespace: mycompany
└─ Repository: backend
└─ Tag: latest
```

### Best Practices for Versioning:

```bash
# Semantic versioning (MAJOR.MINOR.PATCH)
docker tag myapp:latest myapp:1.0.0
docker tag myapp:latest myapp:v1.0
docker tag myapp:latest myapp:1.0

# Build from Dockerfile, automatically tag:
docker build -t myapp:1.0.0 .
docker build -t myapp:latest .

# Tag for registry push:
docker tag myapp:1.0.0 myregistry.com/myapp:1.0.0

# Date-based tagging:
docker build -t myapp:2024-04-06 .

# Git commit-based tagging:
docker build -t myapp:$(git rev-parse --short HEAD) .
```

### Tagging Strategy Example:

```bash
# After building:
$ docker build -t backend .

# Tag with latest
$ docker tag backend:latest backend:latest

# Tag with semantic version
$ docker tag backend:latest backend:v1.5.3

# Tag for production registry
$ docker tag backend:v1.5.3 registry.company.com/backend:v1.5.3

# Now you have 3 tags pointing to same image:
$ docker images
backend       latest        abc123def
backend       v1.5.3        abc123def
registry..    v1.5.3        abc123def

All have same IMAGE ID = same actual image!
```

---

## 3.6 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ What a Docker image is and contains
- ✓ What a Docker container is and how it runs
- ✓ Key differences between images and containers
- ✓ How container filesystem works (Copy-on-Write)
- ✓ Image versioning and tagging strategies

---

# SECTION 4: DOCKER INSTALLATION & SETUP

## 4.1 Installing Docker on Windows

### Prerequisites:
- Windows 10/11 (Home, Pro, Enterprise)
- 4GB RAM minimum (8GB recommended)
- Virtualization enabled in BIOS

### Installation Steps:

#### Step 1: Enable WSL 2 (Windows Subsystem for Linux 2)
```powershell
# Open PowerShell as Administrator

# Enable WSL 2
wsl --install

# Or if WSL is already installed:
wsl --set-default-version 2

# Verify WSL 2
wsl --list --verbose
# Should show: * Ubuntu    Running    2
```

#### Step 2: Download Docker Desktop
1. Visit: https://www.docker.com/products/docker-desktop
2. Click "Download for Windows"
3. Run the installer (Docker Desktop Installer.exe)

#### Step 3: Complete Installation
1. Choose "Install required Windows components for WSL 2"
2. Choose installation location
3. Click "Install"
4. Restart when prompted

#### Step 4: Verify Installation
```powershell
# After restart and Docker startup
docker --version
# Output: Docker version 24.0.x, build xxxx

docker run hello-world
# Should output: Hello from Docker!
```

### Starting Docker Desktop:
- Search "Docker Desktop" in Windows menu
- Or Docker starts automatically on Windows startup (configurable)

---

## 4.2 Installing Docker on macOS

### Prerequisites:
- macOS 11 (Big Sur) or newer
- Apple Silicon (M1/M2/M3) or Intel processor
- 4GB RAM minimum (8GB recommended)

### Installation Steps:

#### For Apple Silicon (M1/M2/M3):
```bash
# Using Homebrew (recommended)
brew install docker

# Or download: https://docs.docker.com/desktop/install/mac-install/
# Choose "Apple Silicon" version
```

#### For Intel Macs:
```bash
# Using Homebrew
brew install docker

# Or download Intel version from Docker website
```

#### Start Docker Desktop:
```bash
# Navigate to Applications folder
# Double-click "Docker.app"

# Or via terminal:
open /Applications/Docker.app

# Verify installation:
docker --version
docker run hello-world
```

---

## 4.3 Installing Docker on Linux

### Ubuntu/Debian Installation:

```bash
# Update package manager
sudo apt-get update
sudo apt-get upgrade -y

# Install Docker
sudo apt-get install -y docker.io docker-compose

# Add user to docker group (optional, allows running without sudo)
sudo usermod -aG docker $USER

# Activate group changes
newgrp docker

# Verify installation
docker --version
docker run hello-world

# Enable Docker to start on boot
sudo systemctl enable docker
sudo systemctl start docker
```

### RHEL/CentOS Installation:

```bash
# Update package manager
sudo yum update -y

# Install Docker
sudo yum install -y docker docker-compose

# Verify
docker --version

# Start Docker
sudo systemctl start docker
sudo systemctl enable docker

# Add user to docker group (optional)
sudo usermod -aG docker $USER
newgrp docker
```

---

## 4.4 Understanding Docker Desktop

### Components:
```
Docker Desktop consists of:
├── Docker Engine (Daemon)
├── Docker CLI (Client)
├── Docker Compose
├── Docker Extensions
└── GUI Dashboard

Runs inside:
├── WSL 2 Virtual Machine (Windows)
├── Hypervisor (macOS)
└── Native Linux (Linux)
```

### Key Features:
- **GUI Dashboard:** Visual container management
- **Resource Control:** Set CPU, memory, disk limits
- **File Sharing:** Mount local directories
- **Settings Optimization:** Configure Docker behavior

### Typical Settings:
```
Resources:
  - CPUs: 4 (default, adjust as needed)
  - Memory: 4GB (increase for heavy workloads)
  - Swap: 1GB
  - Disk: 50GB (for image storage)

File Sharing:
  - Mount C:\ or entire home directory
  - Allows containers to access local files
```

---

## 4.5 Verifying Your Installation

### Complete Verification Checklist:

```bash
#!/bin/bash
echo "=== Docker Installation Verification ==="

# 1. Check Docker version
echo "1. Docker Version:"
docker --version

# 2. Check Docker client and server
echo -e "\n2. Docker Info:"
docker info | head -20

# 3. Test with hello-world
echo -e "\n3. Running hello-world container:"
docker run hello-world

# 4. Check images
echo -e "\n4. Local Images:"
docker images

# 5. Check running containers
echo -e "\n5. Running Containers:"
docker ps

# 6. Check Docker Compose
echo -e "\n6. Docker Compose:"
docker-compose --version

# 7. Test volume mounting
echo -e "\n7. Testing volume mount:"
docker run -v /tmp:/data ubuntu ls /data

echo -e "\n✓ Installation verified successfully!"
```

---

## 4.6 Troubleshooting Installation

### Issue: "docker: command not found"

**Solution:**
```bash
# macOS/Linux: Verify PATH
echo $PATH

# Add Docker to PATH if needed:
# Add this to ~/.bashrc or ~/.zshrc:
export PATH="/usr/local/bin:$PATH"

# Windows: Docker may not be in PATH
# Restart terminal or computer after installation
```

### Issue: "Cannot connect to Docker daemon"

**Solution (Windows/macOS):**
- Open Docker Desktop application
- Wait for it to fully start (icon should be visible in taskbar)
- Retry docker command

**Solution (Linux):**
```bash
# Start Docker service
sudo systemctl start docker

# Or if using Ubuntu/Debian:
systemctl start docker
sudo docker ps  # Use sudo if not in docker group
```

### Issue: "Permission denied while trying to connect to Docker daemon"

**Solution:**
```bash
# Add current user to docker group (Linux/macOS)
sudo usermod -aG docker $USER
newgrp docker

# Verify
docker ps  # Should work without sudo now
```

---

## 4.7 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Installation on Windows, macOS, and Linux
- ✓ Docker Desktop components and usage
- ✓ Verification of successful installation
- ✓ Common troubleshooting steps

---

# SECTION 5: DOCKER CLI BASICS

## 5.1 Essential Docker Commands

### Command Structure:
```
docker [COMMAND] [OPTIONS] [ARGUMENTS]

Examples:
docker run -d -p 8000:8000 myapp
└─ run: command
└─ -d: detached mode (option)
└─ -p 8000:8000: port mapping (option with value)
└─ myapp: image name (argument)
```

### Getting Help:
```bash
# See all commands
docker --help

# Help for specific command
docker run --help

# Check Docker version
docker --version

# Check system info
docker info
```

---

## 5.2 Image Management Commands

### 5.2.1 docker pull - Download Images

```bash
# Pull from Docker Hub
docker pull ubuntu:22.04

# Pull image, explicitly specify registry
docker pull docker.io/library/ubuntu:22.04

# Pull latest tag (default)
docker pull python
# Equivalent to:
docker pull python:latest

# Check what was downloaded
docker images
```

### 5.2.2 docker images - List Local Images

```bash
# List all images
docker images

# Output format:
# REPOSITORY    TAG       IMAGE ID      CREATED      SIZE
# ubuntu        22.04     6461a62f2e2f  2 weeks ago  77.8MB
# python        3.11      abc123def456  1 week ago   879MB

# List only specific image
docker images ubuntu

# Show image digests (unique identifiers)
docker images --digests

# List with human-readable sizes
docker images --human-readable

# List only image IDs
docker images -q

# Filter images
docker images --filter "dangling=true"  # Unused intermediate images
docker images --filter "before=ubuntu"  # Images created before ubuntu

# Format output as JSON
docker images --format "{{.Repository}}:{{.Tag}}"
```

### 5.2.3 docker rmi - Remove Images

```bash
# Remove image by name
docker rmi ubuntu:22.04

# Remove image by ID
docker rmi abc123def456

# Remove multiple images
docker rmi ubuntu:22.04 python:3.11

# Remove unused images (dangling)
docker image prune

# Remove all unused images (even if has tags)
docker image prune -a

# Force remove (even if container running)
docker rmi -f myapp:latest

# Remove image and all child images
docker rmi -f $(docker images -q)  # Use carefully!
```

### 5.2.4 docker build - Build Images

```bash
# Build image from Dockerfile
docker build -t myapp:1.0 .
# -t: tag the image
# .: build context (current directory)

# Build with custom Dockerfile path
docker build -f Dockerfile.prod -t myapp:prod .

# Build with build arguments
docker build --build-arg PYTHON_VERSION=3.11 -t myapp .

# Build without cache (rebuild all layers)
docker build --no-cache -t myapp:latest .

# Build with multiple tags
docker build -t myapp:latest -t myapp:v1.0.0 .

# Show build progress (default)
docker build -t myapp .

# More detailed build info
docker build --progress=plain -t myapp .

# Set labels
docker build --label version=1.0 -t myapp .
```

### 5.2.5 docker tag - Tag Existing Images

```bash
# Tag existing image with new name
docker tag myapp:latest myapp:v1.0.0

# Tag for pushing to registry
docker tag myapp:latest myregistry.azurecr.io/myapp:latest

# Multiple tags pointing to same image
docker tag myapp:latest myapp:production
docker tag myapp:latest myapp:v1.0

# Verify tags
docker images myapp
# Shows: myapp  latest
#        myapp  v1.0.0
#        Both with same IMAGE ID
```

---

## 5.3 Container Runtime Commands

### 5.3.1 docker run - Create and Start Containers

```bash
# Basic syntax
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

# Simple example
docker run ubuntu:22.04 echo "Hello from container!"

# Run interactively
docker run -it ubuntu:22.04 /bin/bash
# -i: keep STDIN open
# -t: allocate pseudo-terminal
# /bin/bash: command to run

# Run in background (detached)
docker run -d nginx:latest
# Returns container ID and exits

# Name the container
docker run -d --name my-nginx nginx:latest

# Publish ports
docker run -d -p 8000:80 nginx:latest
# -p HOST_PORT:CONTAINER_PORT

# Set environment variables
docker run -d -e DATABASE_URL="mysql://localhost" myapp

# Mount volumes
docker run -d -v /host/path:/container/path myapp
docker run -d -v my_volume:/data myapp

# Full example
docker run -d \
  --name backend \
  -p 8000:8000 \
  -e DEBUG=true \
  -e DATABASE_URL="postgresql://db:5432/mydb" \
  -v ./app:/app \
  -v postgres_data:/var/lib/postgresql/data \
  myapp:1.0
```

### 5.3.2 docker ps - List Containers

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List container IDs only
docker ps -q

# List with specific format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"

# Filter containers
docker ps -a --filter "status=exited"
docker ps -a --filter "name=nginx"
docker ps -a --filter "ancestor=ubuntu:22.04"

# Show all containers (limit to 5)
docker ps -a -n 5

# Show size of containers
docker ps -a -s

# JSON output
docker ps --format json
```

### 5.3.3 docker start/stop/restart

```bash
# Stop running container
docker stop mycontainer

# Stop with timeout (15 seconds default)
docker stop --time=10 mycontainer

# Start stopped container
docker start mycontainer

# Restart container
docker restart mycontainer

# Restart multiple
docker restart container1 container2

# Stop all running containers
docker stop $(docker ps -q)

# Kill container (force stop)
docker kill mycontainer
```

### 5.3.4 docker rm - Remove Containers

```bash
# Remove stopped container
docker rm mycontainer

# Remove multiple containers
docker rm container1 container2

# Remove running container (force)
docker rm -f mycontainer

# Remove all stopped containers
docker container prune

# Remove with custom filter
docker rm $(docker ps -a -q)

# Remove container and its volumes
docker rm -v mycontainer
```

### 5.3.5 docker logs - View Container Output

```bash
# View logs
docker logs mycontainer

# Follow logs (tail -f style)
docker logs -f mycontainer

# Show last 100 lines
docker logs --tail 100 mycontainer

# Show timestamps
docker logs -t mycontainer

# Show since specific time
docker logs --since 2024-04-06T10:00:00 mycontainer

# Show between two times
docker logs --since 10m --until 5m mycontainer

# Show logs from specific point
docker logs --tail 0 -f mycontainer  # Follow from now
```

### 5.3.6 docker exec - Execute Commands in Running Container

```bash
# Run command in running container
docker exec mycontainer ls /app

# Interactive shell
docker exec -it mycontainer /bin/bash

# Run as different user
docker exec -u postgres mycontainer psql -V

# Set environment variables
docker exec -e MY_VAR=value mycontainer echo $MY_VAR

# Working directory
docker exec -w /app mycontainer pwd
```

### 5.3.7 docker inspect - Get Detailed Information

```bash
# Inspect container
docker inspect mycontainer

# Get specific information
docker inspect -f '{{.State.Status}}' mycontainer
docker inspect -f '{{.Config.Image}}' mycontainer
docker inspect -f '{{.NetworkSettings.IPAddress}}' mycontainer

# Format as JSON
docker inspect --format=json mycontainer

# Inspect image
docker inspect myimage:latest
```

---

## 5.4 Volume & Network Commands

### 5.4.1 docker volume

```bash
# List volumes
docker volume ls

# Create named volume
docker volume create mydata

# Inspect volume
docker volume inspect mydata

# Remove volume
docker volume rm mydata

# Remove unused volumes
docker volume prune
```

### 5.4.2 docker network

```bash
# List networks
docker network ls

# Create custom network
docker network create mynetwork

# Connect container to network
docker network connect mynetwork mycontainer

# Disconnect container
docker network disconnect mynetwork mycontainer

# Inspect network
docker network inspect mynetwork

# Remove network
docker network rm mynetwork
```

---

## 5.5 Common CLI Flags Explained

### Port & Networking:
```bash
-p HOST_PORT:CONTAINER_PORT    Forward container port to host
-P                             Publish all exposed ports
--network host                 Use host network
--network mynet                Connect to custom network
--hostname myhost              Set container hostname
```

### Volumes & Storage:
```bash
-v /host:/container           Bind mount (host → container)
-v myvolume:/data             Named volume mount
--mount type=bind,src=.../    Advanced mount syntax
-v /container                 Anonymous volume
```

### Environment & Configuration:
```bash
-e KEY=VALUE                  Set environment variable
--env-file .env               Load from file
-w /path                      Working directory
-u user                       Run as user
```

### Resource Limits:
```bash
--memory 512m                 Max memory limit
--memory-swap 1g              Total memory+swap
--cpus 0.5                    CPU limit (0.5 = 50% of one core)
--cpuset-cpus 0,1             Specific CPUs to use
```

### Restart Policy:
```bash
--restart no                  No restart (default)
--restart always              Always restart
--restart on-failure          Restart on failure
--restart unless-stopped      Restart unless explicitly stopped
```

### Interactive & TTY:
```bash
-i, --interactive             Keep STDIN open
-t, --tty                     Allocate terminal
-it                           Interactive terminal (combined)
-d, --detach                  Run in background
```

---

## 5.6 Practical CLI Examples

### Example 1: Run Database

```bash
# Start MySQL container
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=rootpass \
  -e MYSQL_DATABASE=myapp \
  -p 3306:3306 \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0

# Verify running
docker ps

# Check logs
docker logs mysql-db

# Execute command in container
docker exec -it mysql-db mysql -u root -p -e "SHOW DATABASES;"

# Stop container
docker stop mysql-db

# Remove (with data loss)
docker rm mysql-db

# Remove with volume cleanup
docker rm -v mysql-db
```

### Example 2: Run Web Server

```bash
docker run -d \
  --name my-app \
  -p 8000:80 \
  -v ./html:/usr/share/nginx/html:ro \
  nginx:alpine

# Web server now available at http://localhost:8000
# -v with :ro makes volume read-only
```

### Example 3: Temporary Container for Task

```bash
# Run container, execute task, auto-remove
docker run --rm \
  -v $(pwd):/data \
  python:3.11 \
  python /data/script.py

# Container automatically removed after completion
# --rm flag prevents orphaned containers
```

---

## 5.7 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ All essential Docker CLI commands
- ✓ Image management (pull, images, rmi, build, tag)
- ✓ Container lifecycle (run, ps, start, stop, rm)
- ✓ Logs, exec, and inspection commands
- ✓ Common flags and their purposes
- ✓ Practical command examples

---

# SECTION 6: DOCKERFILE - BUILDING YOUR OWN IMAGES

## 6.1 What is a Dockerfile?

A Dockerfile is a **text file** containing a set of instructions to build a Docker image.

### Why You Need It:
- Package your application
- Define dependencies and environment
- Automate image creation
- Enable repeatable, consistent builds
- Share with team members

### Analogy:
```
Dockerfile = Recipe
Docker Image = Finished dish
Docker Container = Eating the dish

Recipe tells how to make it
Image is the result
Container is using it
```

---

## 6.2 Dockerfile Structure

### Basic Template:

```dockerfile
# Start from a base image
FROM ubuntu:22.04

# Metadata (optional)
LABEL version="1.0" \
      description="My Python Application"

# Set working directory
WORKDIR /app

# Copy files from build context
COPY requirements.txt .

# Install dependencies
RUN apt-get update && \
    apt-get install -y python3 python3-pip && \
    pip install -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 8000

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    DEBUG=false

# Define entry point
CMD ["python3", "app.py"]
```

### Line-by-line Explanation:

| Instruction | Purpose | Example |
|-------------|---------|---------|
| FROM | Base image to build on | FROM python:3.11 |
| WORKDIR | Working directory in container | WORKDIR /app |
| COPY | Copy files from host to image | COPY . /app |
| RUN | Execute command during build | RUN pip install flask |
| EXPOSE | Document exposed ports | EXPOSE 8000 |
| ENV | Set environment variables | ENV DEBUG=false |
| CMD | Default command to run | CMD ["python", "app.py"] |
| ENTRYPOINT | Configure container as executable | ENTRYPOINT ["python"] |
| ARG | Build-time variable | ARG PYTHON_VERSION=3.11 |
| VOLUME | Define mount points | VOLUME /data |
| USER | Run as specific user | USER appuser |
| HEALTHCHECK | Container health status | HEALTHCHECK CMD curl localhost |

---

## 6.3 Key Dockerfile Instructions (In-Depth)

### 1. FROM - Choose Base Image

```dockerfile
# Official Python runtime
FROM python:3.11-slim

# Ubuntu with nothing else
FROM ubuntu:22.04

# Alpine Linux (very small, ~5MB)
FROM alpine:3.19

# Official Node.js
FROM node:20-alpine

# Multi-stage: FROM used multiple times
FROM python:3.11 as builder
...
FROM python:3.11-slim as runtime
...
```

**Base Image Selection Tips:**
- `python:3.11-slim` for Python apps (300MB, minimal)
- `python:3.11-alpine` for tiny Python apps (50MB, fewer system tools)
- `alpine` for any app (5MB, minimal dependencies)
- `ubuntu:22.04` for flexibility but larger
- Use specific versions, not `latest`

---

### 2. WORKDIR - Set Working Directory

```dockerfile
WORKDIR /app

# Now all commands run in /app
COPY requirements.txt .      # Copies to /app/requirements.txt
RUN pip install -r requirements.txt  # Runs in /app
COPY . .                     # Copies to /app

# Multiple WORKDIR commands
WORKDIR /app/src
WORKDIR ../config           # Relative paths work

# Creates directory if it doesn't exist
WORKDIR /var/log/myapp      # Creates /var/log/myapp
```

---

### 3. COPY vs ADD

```dockerfile
# COPY - Simple, recommended for most cases
COPY requirements.txt /app/
COPY . /app
COPY ./src ./dest ./
COPY --chown=appuser:appgroup . /app  # Set ownership

# ADD - More powerful, use for special cases
ADD https://example.com/file.tar.gz /tmp/  # Download from URL
ADD file.tar.gz /opt/                       # Auto-extracts archives

# Best practice: Use COPY for normal files, ADD only when needed
```

---

### 4. RUN - Execute Commands During Build

```dockerfile
# Single command
RUN apt-get update

# Multiple commands (shell form)
RUN pip install flask && \
    pip install requests && \
    pip install gunicorn

# JSON/exec form (preferred for best practices)
RUN ["apt-get", "update"]

# With environment variables
RUN export MYVAR=value && echo $MYVAR

# Best practice: Combine commands to reduce layers
RUN apt-get update && \
    apt-get install -y python3 pip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

---

### 5. EXPOSE - Document Ports

```dockerfile
# Expose port 8000
EXPOSE 8000

# Multiple ports
EXPOSE 8000 5432 6379
EXPOSE 8000/tcp 8000/udp

# Note: EXPOSE doesn't map ports, only documents
# Must use -p flag when running:
# docker run -p 8000:8000 myimage
```

---

### 6. ENV - Environment Variables

```dockerfile
# Set environment variable
ENV API_KEY=secret
ENV DEBUG=true

# Multiple variables
ENV KEY1=value1 \
    KEY2=value2

# Use in subsequent commands
ENV APPDIR=/app
WORKDIR $APPDIR

# Can be overridden at runtime
# docker run -e DEBUG=false myimage
```

---

### 7. CMD - Default Command

```dockerfile
# Exec form (recommended)
CMD ["python", "app.py"]

# Shell form
CMD python app.py

# Used as default if no command provided
# docker run myimage          # Runs: python app.py
# docker run myimage sleep 10 # Overrides CMD, runs: sleep 10
```

---

### 8. ENTRYPOINT - Configure Executable

```dockerfile
# ENTRYPOINT makes container like an executable
ENTRYPOINT ["python", "app.py"]

# Cannot be overridden by docker run command
# docker run myimage arg1 arg2  # Runs: python app.py arg1 arg2

# ENTRYPOINT + CMD pattern (best practice)
ENTRYPOINT ["python"]           # Fixed executable
CMD ["app.py"]                  # Default argument

# Usage:
# docker run myimage              # Runs: python app.py
# docker run myimage script.py    # Runs: python script.py
```

---

### 9. ARG - Build-Time Variables

```dockerfile
# Define argument
ARG PYTHON_VERSION=3.11

# Use in FROM
FROM python:${PYTHON_VERSION}

# Use in RUN
RUN echo "Building with Python ${PYTHON_VERSION}"

# Build with argument
# docker build --build-arg PYTHON_VERSION=3.9 .
```

---

### 10. VOLUME - Define Mount Points

```dockerfile
# Define volume mount point
VOLUME /data

# Multiple volumes
VOLUME ["/data", "/logs"]

# Tells Docker this directory should be mounted
# docker run -v /your/path:/data myimage
```

---

### 11. USER - Run as Non-root

```dockerfile
# Create user
RUN useradd -m -u 1000 appuser

# Switch to user
USER appuser

# All subsequent commands run as appuser
RUN apt-get install something  # Would fail (no permission)

# Best practice: Don't run as root in production
```

---

### 12. HEALTHCHECK - Container Health

```dockerfile
# Simple health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD curl -f http://localhost:8000/health || exit 1

# Health check states:
# - healthy: health check passed
# - unhealthy: health check failed
# - starting: health check not yet completed

# Custom health check
HEALTHCHECK CMD ["python", "-c", "import requests; requests.get('http://localhost:8000')"]
```

---

## 6.4 Writing a Production Dockerfile

### Example: Flask Application

```dockerfile
# Multi-stage build for small size and security

# Stage 1: Builder
FROM python:3.11-slim as builder

WORKDIR /app

# Install system dependencies (only ones needed)
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements
COPY requirements.txt .

# Install Python dependencies
RUN pip install --user --no-cache-dir -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

# Add metadata
LABEL version="1.0" \
      maintainer="your-email@example.com" \
      description="Flask application"

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    APP_HOME=/app

# Create non-root user
RUN groupadd -r appuser && useradd -r -g appuser appuser

# Set working directory
WORKDIR $APP_HOME

# Copy Python dependencies from builder
COPY --from=builder /root/.local /home/appuser/.local

# Copy application code
COPY --chown=appuser:appuser . .

# Set PATH for Python packages
ENV PATH=/home/appuser/.local/bin:$PATH

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 5000

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD python -c "import requests; requests.get('http://localhost:5000/health')" || exit 1

# Run application with gunicorn
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "4", "app:app"]
```

---

## 6.5 Best Practices for Dockerfiles

### 1. Use Specific Base Image Versions
```dockerfile
# Bad - could break in future
FROM python:latest

# Good - pinned version
FROM python:3.11.8-slim
```

### 2. Minimize Layers
```dockerfile
# Bad - 4 layers
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get clean

# Good - 1 layer
RUN apt-get update && \
    apt-get install -y curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

### 3. Leverage Layer Caching
```dockerfile
# Bad - invalidates cache for dependencies on code change
COPY . /app
RUN pip install -r requirements.txt

# Good - dependencies cached separately
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . /app
```

### 4. Use .dockerignore
```
# .dockerignore file
__pycache__
*.pyc
.git
.gitignore
README.md
.env
.venv
node_modules
```

### 5. Don't Run as Root
```dockerfile
# Create and use non-root user
RUN groupadd -r appuser && useradd -r -g appuser appuser
USER appuser
```

### 6. Keep Images Small
```dockerfile
# Use slim or alpine variants
FROM python:3.11-slim    # ~300MB
FROM python:3.11-alpine  # ~50MB

# Remove unnecessary files
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
```

### 7. Use Multi-stage Builds
```dockerfile
# Separate build and runtime stages
# Resulting image only contains runtime artifacts
FROM python:3.11 as builder
... build stuff ...

FROM python:3.11-slim
COPY --from=builder /build/output /app
```

---

## 6.6 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ What Dockerfile is and why it's needed
- ✓ All major Dockerfile instructions
- ✓ How to write production-ready Dockerfiles
- ✓ Best practices for optimization
- ✓ Multi-stage builds and layer caching

---

# SECTION 7: BUILDING DOCKER IMAGES

## 7.1 docker build Command

### Basic Syntax:
```bash
docker build [OPTIONS] PATH | URL | -

# Most common form:
docker build -t imagename:tag .
# -t: tag the image
# .: build context (Dockerfile location)
```

### Detailed Options:

```bash
# Basic build
docker build -t myapp:1.0 .

# Build in current directory (implicit .)
docker build -t myapp .

# Specify Dockerfile path
docker build -f ./docker/Dockerfile -t myapp .

# Build with arguments
docker build --build-arg PYTHON_VERSION=3.9 -t myapp .

# Build from git repository
docker build https://github.com/username/repo.git
docker build https://github.com/username/repo.git#main:dockerfile

# Build without using cache
docker build --no-cache -t myapp .

# Quiet mode (less output)
docker build -q -t myapp .

# Tag with multiple names
docker build -t myapp:1.0 -t myapp:latest -t myapp:prod .

# Set labels (metadata)
docker build --label version=1.0 --label env=prod -t myapp .

# Add labels
docker build -t myapp --label git-sha=$(git rev-parse HEAD) .

# Build progress output
docker build --progress=plain -t myapp .

# Secret/sensitive data (build secrets)
docker build --secret id=secret_name,src=/path/to/secret -t myapp .

# SSH forwarding (for git cloning in build)
docker build --ssh default -t myapp .
```

---

## 7.2 Build Context

### What is Build Context?

Build context is the directory Docker uses for building:
- All files in context available to Dockerfile
- Sent to Docker daemon
- Affects build size and speed

```dockerfile
# From Dockerfile in /project/app/Dockerfile:

COPY . /app          # Copies entire build context
COPY src/ /app/src   # Copies specific subdirectory

# If build context is /project:
# . = everything in /project
# If build context is /project/app:
# . = everything in /project/app
```

### Specifying Build Context:

```bash
# Current directory as context (most common)
docker build -t myapp .

# Specific directory as context
docker build -t myapp /path/to/context

# Parent directory as context
docker build -f app/Dockerfile -t myapp .
# Dockerfile: app/Dockerfile
# Context: . (current)
# So can COPY from parent directory files

# From Git repository (context = repo)
docker build https://github.com/user/repo.git#branch:subdirectory

# From stdin (no context)
docker build -t myapp - < Dockerfile
```

### .dockerignore for Excluding Files:

```
# .dockerignore file (like .gitignore)

# Ignore version control
.git
.gitignore

# Ignore docs and README
README.md
docs/

# Ignore development files
.venv
venv
node_modules
__pycache__
*.pyc
.pytest_cache

# Ignore environment files
.env
.env.local

# Ignore large files
*.iso
*.tar.gz

# Ignore unnecessary directories
.vscode
.idea
.DS_Store

# Ignore test coverage
htmlcov/
.coverage

# Ignore lock files (if dependencies in container)
package-lock.json
poetry.lock
```

### Best Practices:
```bash
# Always include .dockerignore
# This reduces context size and build time

# Example comparison:
# Without .dockerignore: Context = 500 MB (includes node_modules, .git, .venv)
# With .dockerignore: Context = 5 MB (only needed files)
```

---

## 7.3 Build Process and Layer Caching

### Build Layers:

Each Dockerfile instruction creates a layer:

```dockerfile
FROM python:3.11                    # Layer 1 (base image)
WORKDIR /app                        # Layer 2
COPY requirements.txt .             # Layer 3
RUN pip install -r requirements.txt # Layer 4
COPY . .                            # Layer 5
CMD ["python", "app.py"]            # Layer 6 (metadata, no filesystem layer)
```

### Build Output:

```bash
$ docker build -t myapp:1.0 .

Step 1/6 : FROM python:3.11
 ---> abc123def456 (pulling from registry)
 
Step 2/6 : WORKDIR /app
 ---> Running in temp_container_1
 ---> def456ghi789
 
Step 3/6 : COPY requirements.txt .
 ---> Running in temp_container_2
 ---> ghi789jkl012
 
Step 4/6 : RUN pip install -r requirements.txt
 ---> Running in temp_container_3
 ---> jkl012mno345
 
Step 5/6 : COPY . .
 ---> Running in temp_container_4
 ---> mno345pqr678
 
Step 6/6 : CMD ["python", "app.py"]
 ---> Running in temp_container_5
 ---> pqr678stu901

Successfully built pqr678stu901
```

### Layer Caching:

Docker caches each layer. On rebuild:
- Unchanged layers are reused (fast)
- Changed layers are rebuilt

```bash
# First build: 45 seconds (all layers built)
$ docker build -t myapp .
Successfully built in 45.2s

# Modify only app.py (not requirements.txt)
$ docker build -t myapp .
Step 1/6 : FROM python:3.11
 ---> abc123def456 (cached)
Step 2/6 : WORKDIR /app
 ---> def456ghi789 (cached)
Step 3/6 : COPY requirements.txt .
 ---> ghi789jkl012 (cached)
Step 4/6 : RUN pip install -r requirements.txt
 ---> jkl012mno345 (cached)
Step 5/6 : COPY . .
 ---> mno345pqr678 (NEW - app.py changed)
Step 6/6 : CMD ["python", "app.py"]
 ---> pqr678stu901

Successfully built in 3.5s (only layer 5 rebuilt)
```

### Cache Invalidation:

Cache is invalidated (layer rebuilt) when:
1. Base image instruction fails or changes
2. Source files in COPY/ADD change
3. RUN command changes
4. Previous layer is invalidated

### Optimize Caching:

```dockerfile
# Bad - changes app code invalidates dependency cache
COPY . /app
RUN pip install -r requirements.txt

# Good - dependencies cached separately
RUN pip install -r requirements.txt
COPY . /app
```

---

## 7.4 Image Tagging Strategy

### Semantic Versioning:

```bash
# MAJOR.MINOR.PATCH version
docker build -t myapp:1.5.3 .    # v1.5.3 release
docker build -t myapp:1.5 .      # Latest in v1.5.x
docker build -t myapp:1 .        # Latest in v1.x
docker build -t myapp:latest .   # Latest overall

# After tagging, reuse tag names:
docker tag myapp:1.5.3 myapp:latest
docker tag myapp:1.5.3 myapp:1.5
docker tag myapp:1.5.3 myapp:1

# All point to same image:
$ docker images myapp
myapp  latest    abc123def456
myapp  1         abc123def456
myapp  1.5       abc123def456
myapp  1.5.3     abc123def456
```

### Date-based Versioning:

```bash
# ISO date format
docker build -t myapp:2024-04-06 .

# Can combine with time
docker build -t myapp:2024-04-06-1430 .

# Also tag with latest
docker build -t myapp:2024-04-06 -t myapp:latest .
```

### Git-based Versioning:

```bash
# Use git commit hash
docker build -t myapp:$(git rev-parse --short HEAD) .

# Use git branch and commit
docker build -t myapp:$(git rev-parse --abbrev-ref HEAD)-$(git rev-parse --short HEAD) .

# Use git tag
docker build -t myapp:$(git describe --tags --always) .

# Full example:
#!/bin/bash
GIT_COMMIT=$(git rev-parse --short HEAD)
GIT_TAG=$(git describe --tags --always)
docker build \
  -t myapp:$GIT_COMMIT \
  -t myapp:$GIT_TAG \
  -t myapp:latest \
  .
```

### Registry-specific Tagging:

```bash
# For Docker Hub
docker build -t myusername/myapp:1.0.0 .

# For private registry
docker build -t registry.company.com/myapp:1.0.0 .

# For AWS ECR
docker build -t 123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:1.0.0 .

# Multiple registry tags:
docker build \
  -t myapp:1.0.0 \
  -t docker.io/myusername/myapp:1.0.0 \
  -t registry.company.com/myapp:1.0.0 \
  .
```

---

## 7.5 Practical Build Examples

### Example 1: Python Application

```bash
# Build with specific Python version (build arg)
docker build \
  --build-arg PYTHON_VERSION=3.11 \
  --build-arg BASE_IMAGE=slim \
  -t myapp:1.0.0 \
  -t myapp:latest \
  .

# Check image
docker image inspect myapp:1.0.0

# Check image size
docker images myapp:1.0.0
# Output: myapp  1.0.0  abc123def  2 weeks ago  156MB
```

### Example 2: Node.js Application

```dockerfile
# Dockerfile for Node app

FROM node:20-alpine as builder
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

```bash
# Build
docker build -t nodeapp:1.0.0 .

# Build without using cache (fresh build)
docker build --no-cache -t nodeapp:1.0.0 .

# Build with verbose progress
docker build --progress=plain -t nodeapp:1.0.0 .
```

### Example 3: Multi-app Monorepo

```bash
# Build different services from same context
# Dockerfile.api, Dockerfile.worker, Dockerfile.web

# Build API service
docker build -f Dockerfile.api -t mycompany/api:1.0 .

# Build worker service
docker build -f Dockerfile.worker -t mycompany/worker:1.0 .

# Build web service
docker build -f Dockerfile.web -t mycompany/web:1.0 .
```

---

## 7.6 Debugging Build Problems

### Common Issues:

```bash
# Issue: Layer caching not working as expected
# Solution: Use --no-cache
docker build --no-cache -t myapp .

# Issue: Image very large (bigger than expected)
# Solution: Check for unnecessary files in .dockerignore
docker build --progress=plain -t myapp . 2>&1 | grep -i "copying\|adding"

# Issue: Build fails at specific layer
# Solution: Run intermediate container and debug
docker run -it $(image_id_of_previous_layer) /bin/bash

# Issue: RUN command fails (network timeout, etc)
# Solution: Check Dockerfile and try build without cache
docker build --no-cache -t myapp .

# Issue: Want to debug specific layer
# Solution: Build up to that layer and inspect
# Temporarily add RUN command:
RUN some_diagnostic_command
docker build -t myapp-debug .
```

---

## 7.7 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ docker build command and options
- ✓ Build context and .dockerignore
- ✓ Layer caching and optimization
- ✓ Image tagging strategies
- ✓ Practical build examples
- ✓ Debugging build issues

---

# SECTION 8: RUNNING CONTAINERS

## 8.1 Container Execution Modes

### Interactive Mode (-it)

```bash
# Run container with interactive terminal
docker run -it ubuntu:22.04 /bin/bash

# You can type commands:
root@container:/# ls /
root@container:/# echo "Hello from container"
root@container:/# exit

# Flags:
# -i (--interactive): Keep STDIN open
# -t (--tty): Allocate pseudo-terminal
```

**Use Case:** Debugging, exploration, one-off commands

### Detached Mode (-d)

```bash
# Run container in background
docker run -d --name myapp nginx:latest

# Returns immediately with container ID
# a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6

# Container continues running in background
docker ps  # See running containers

# Attach to container later:
docker attach myapp

# Or execute commands:
docker exec -it myapp /bin/bash

# View logs:
docker logs myapp
```

**Use Case:** Services, servers, long-running processes

### Foreground Mode (Default)

```bash
# Run container in foreground (can still interact)
docker run nginx:latest

# Containerprocess output shows in terminal
# Press Ctrl+C to stop

# If you want container to continue after Ctrl+C:
docker run -d nginx:latest
```

---

## 8.2 Container Naming and Identification

### Naming Convention:

```bash
# Name must be unique
docker run -d --name my-backend python:3.11 python app.py

# Automatically generated names (if not specified)
docker run -d python:3.11 python app.py
# Might get: compassionate_edison, affectionate_morse, etc.

# Operations using container name
docker start my-backend
docker stop my-backend
docker logs my-backend
docker exec -it my-backend /bin/bash
docker rm my-backend

# Operations using container ID
docker ps           # Shows container ID
docker start a1b2c3d4
docker stop a1b2c3d4e5f6

# Use container ID prefix (must be unique prefix)
docker stop a1b2c3  # If unique, works
```

### Naming Best Practices:

```bash
# Clear service names
docker run -d --name mysql-primary mysql:8.0
docker run -d --name redis-cache redis:7
docker run -d --name api-backend python:3.11

# Include version/environment
docker run -d --name api-backend-v1.5 python:3.11
docker run -d --name web-prod nginx:1.25

# Include environment
docker run -d --name api-dev python:3.11
docker run -d --name api-staging python:3.11
docker run -d --name api-prod python:3.11
```

---

## 8.3 Port Mapping and Service Access

### Port Mapping Basics:

```bash
# Map single port
docker run -d -p 8000:80 nginx:latest
# -p HOST_PORT:CONTAINER_PORT
# Host port 8000 → Container port 80

# Access container service:
# http://localhost:8000

# Map multiple ports
docker run -d \
  -p 8000:80 \
  -p 8443:443 \
  -p 3000:3000 \
  nginx:latest

# Specify IP address
docker run -d -p 127.0.0.1:8000:80 nginx:latest
# Only accessible on localhost, not from other machines

# Map to any IP on host
docker run -d -p 0.0.0.0:8000:80 nginx:latest
# Accessible from anywhere

# Expose (document, but don't map)
docker run -d -p 8000:80 nginx:latest
```

### Port Mapping with Dynamic Ports:

```bash
# Let Docker choose available host port
docker run -d -p :80 nginx:latest

# Docker assigns random port from 32768+
# Check with:
docker port container_name
# Output: 80/tcp -> 0.0.0.0:32769

# Access at:
http://localhost:32769
```

### Container-to-Container Communication:

```bash
# Without explicit network, containers can talk via container name
# Create custom network
docker network create mynet

# Run containers on same network
docker run -d --name db --network mynet mysql:8.0
docker run -d --name api --network mynet myapp:latest

# From api container, can reach db:
# mysql://db:3306
# db is the container's IP (Docker DNS)

# Within api container:
docker exec -it api /bin/bash
# Inside: mysql -h db -u root -p
```

---

## 8.4 Environment Variables

### Setting Environment Variables:

```bash
# Single variable
docker run -d -e DEBUG=true myapp:latest

# Multiple variables
docker run -d \
  -e DEBUG=true \
  -e LOG_LEVEL=INFO \
  -e DATABASE_URL="postgresql://db:5432/mydb" \
  myapp:latest

# From file
docker run -d --env-file .env myapp:latest
# .env file format:
# DEBUG=true
# LOG_LEVEL=INFO
# DATABASE_URL=postgresql://db:5432/mydb
```

### Accessing Variables in Application:

**Python Example:**
```python
import os

DEBUG = os.getenv("DEBUG", "false").lower() == "true"
LOG_LEVEL = os.getenv("LOG_LEVEL", "INFO")
DATABASE_URL = os.getenv("DATABASE_URL", "sqlite:///app.db")

print(f"Debug: {DEBUG}")
print(f"Log Level: {LOG_LEVEL}")
print(f"Database: {DATABASE_URL}")
```

**Node.js Example:**
```javascript
const DEBUG = process.env.DEBUG === "true";
const LOG_LEVEL = process.env.LOG_LEVEL || "INFO";
const DATABASE_URL = process.env.DATABASE_URL || "sqlite:///app.db";

console.log(`Debug: ${DEBUG}`);
console.log(`Log Level: ${LOG_LEVEL}`);
console.log(`Database: ${DATABASE_URL}`);
```

---

## 8.5 Restart Policies

### Restart Policy Options:

```bash
# no: Don't restart (default)
docker run -d --restart=no myapp:latest

# always: Always restart, even if exited  cleanly
docker run -d --restart=always myapp:latest
# Good for long-running services

# unless-stopped: Always restart unless explicitly stopped
docker run -d --restart=unless-stopped myapp:latest

# on-failure: Restart only if exits with non-zero code
docker run -d --restart=on-failure:5 myapp:latest
# Max 5 attempts, then stop

# Restart with delay
docker run -d --restart=on-failure:3 --restart=unless-stopped myapp:latest
```

### Restart Policy Use Cases:

```bash
# Web service (should always run)
docker run -d --restart=always --name web nginx:latest

# Long-running worker (should recover from crashes)
docker run -d --restart=on-failure:10 --name worker celery-app

# Development service (don't auto-restart)
docker run -d --restart=no --name dev-api myapp:latest
```

---

## 8.6 Resource Limits

### Memory Limits:

```bash
# Set max memory
docker run -d --memory 512m myapp:latest

# Set max memory and swap
docker run -d --memory 512m --memory-swap 1g myapp:latest

# Check memory usage
docker stats myapp
```

### CPU Limits:

```bash
# Set CPU limit (fraction of CPU)
docker run -d --cpus 1.5 myapp:latest
# 1.5 = 1.5 CPUs

# Set specific CPUs
docker run -d --cpuset-cpus "0,1" myapp:latest

# Check CPU usage
docker stats myapp
```

### Combined Resource Example:

```bash
docker run -d \
  --memory 512m \
  --memory-swap 1g \
  --cpus 1.0 \
  --cpuset-cpus "0-3" \
  myapp:latest
```

---

## 8.7 Volumes and Mounts

### Bind Mounts (Directory Binding):

```bash
# Map host directory to container
docker run -d \
  -v /host/path:/container/path \
  myapp:latest

# Example: Mount source code for development
docker run -d \
  -v $(pwd):/app \
  -w /app \
  python:3.11 \
  python app.py

# Read-only mount
docker run -d \
  -v /host/path:/container/path:ro \
  myapp:latest

# Relative paths
docker run -d \
  -v ./app:/app \
  python:3.11 \
  python app.py
```

### Named Volumes:

```bash
# Create named volume
docker volume create mydata

# Use in container
docker run -d \
  -v mydata:/data \
  myapp:latest

# Data persists across container stops
docker stop myapp
docker start myapp
# Data in /data still there!

# Remove volume
docker volume rm mydata
```

### Tmpfs Mounts (Memory Storage):

```bash
# Temporary file storage (cleared on container stop)
docker run -d \
  --tmpfs /tmpfs:size=1g \
  myapp:latest
```

---

## 8.8 Practical Container Examples

### Example 1: Web Server

```bash
docker run -d \
  --name my-nginx \
  -p 8080:80 \
  -v ./html:/usr/share/nginx/html:ro \
  --restart=always \
  nginx:alpine

# Access at http://localhost:8080
# Files from ./html served as read-only
```

### Example 2: Database

```bash
docker run -d \
  --name postgres-db \
  -e POSTGRES_PASSWORD=mypassword \
  -e POSTGRES_DB=myapp \
  -p 5432:5432 \
  -v postgres-data:/var/lib/postgresql/data \
  --restart=unless-stopped \
  postgres:15-alpine
```

### Example 3: Development Environment

```bash
docker run -it \
  --name dev-env \
  -v $(pwd):/project \
  -w /project \
  -p 8000:8000 \
  python:3.11

# Inside container:
# root@container:/project# pip install -r requirements.txt
# root@container:/project# python app.py
```

### Example 4: One-off Task

```bash
# Run container, execute task, auto-remove
docker run --rm \
  -v $(pwd):/data \
  python:3.11 \
  python /data/script.py

# Container automatically removed after completion
```

---

## 8.9 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Interactive vs detached modes
- ✓ Container naming and identification
- ✓ Port mapping and service access
- ✓ Environment variables
- ✓ Restart policies
- ✓ Resource limits (memory, CPU)
- ✓ Volumes and bind mounts
- ✓ Practical container examples

---

*[Document continues with remaining 16 sections covering Docker Volumes, Networks, Environment Variables, Port Mapping, Docker Logs, Container Lifecycle, Image Optimization, Docker Registry, Docker Compose, Multi-container Applications, Best Practices, and 3 comprehensive real-world projects with complete code examples]*

# SECTION 9: DOCKER VOLUMES & PERSISTENT STORAGE

## 9.1 Why Volumes Are Needed

### The Problem:

```
Default Container Behavior:
┌─────────────────────────────┐
│     Container File Data     │
├─────────────────────────────┤
│  Exists only while running   │
│   LOST when container stops  │
└─────────────────────────────┘

Issues:
- Database files lost after stop
- Logs disappear
- Uploaded files deleted
- Configuration changes reverted
```

### The Solution - Volumes:

```
Container with Volume:
┌─────────────────────────────┐
│     Container File Data     │
├─────────────────────────────┤
│     Volume Mount            │
│  (Host storage)             │
├─────────────────────────────┤
│  Data PERSISTS after stop   │
└─────────────────────────────┘

Benefits:
- Data survives container stop
- Data survives container deletion
- Can share data between containers
- Can back up data easily
```

---

## 9.2 Volume Types

### 1. Named Volumes

Named volumes are managed by Docker:

```bash
# Create named volume
docker volume create mydata

# Use in container
docker run -d \
  -v mydata:/data \
  myapp:latest

# Data stored at Docker location
# Linux: /var/lib/docker/volumes/mydata/_data
# Windows/Mac: Inside Docker VM

# List volumes
docker volume ls

# Inspect volume
docker volume inspect mydata

# Remove volume
docker volume rm mydata

# Backup volume
docker run --rm -v mydata:/data -v $(pwd):/backup \
  alpine tar czf /backup/mydata.tar.gz -C /data .

# Restore volume
docker run --rm -v mydata:/data -v $(pwd):/backup \
  alpine tar xzf /backup/mydata.tar.gz -C /data
```

**Use Cases:**
- Database persistence
- Important data
- Multi-container sharing
- Data that needs backup

### 2. Bind Mounts

Bind mounts link to host directories:

```bash
# Relative path
docker run -d -v ./app:/app myapp:latest

# Absolute path
docker run -d -v /home/user/app:/app myapp:latest

# Windows path
docker run -d -v "C:\Users\user\app:C:\app" myapp:latest

# Read-only
docker run -d -v ./config:/config:ro myapp:latest

# Read-write (default)
docker run -d -v ./app:/app:rw myapp:latest
```

**Use Cases:**
- Development (hot reload code changes)
- Configuration files
- Logs viewing
- Testing

### 3. Tmpfs Mounts

Temporary filesystem (RAM-based):

```bash
docker run -d \
  --tmpfs /tmpfs:size=1g \
  -tmpfs /temp:noexec \
  myapp:latest

# Data cleared on container stop
# Useful for temporary files
# Faster than disk
```

**Use Cases:**
- Temporary files
- Cache
- Sensitive data (cleared after use)

---

## 9.3 Volume Mounting Modes

### Read-Write Mode (Default):

```bash
docker run -d \
  -v myvolume:/data \
  mysql:8.0
# Container can read and write to /data
```

### Read-Only Mode:

```bash
docker run -d \
  -v config:/etc/app:ro \
  myapp:latest
# Container can only read /etc/app
# Cannot modify configuration
```

### Propagation Modes:

```bash
# rprivate (default): Changes isolated
docker run -d -v /host:/container myapp

# shared: Changes shared with host
docker run -d -v /host:/container:shared myapp

# rslave: Read-only shared from host
docker run -d -v /host:/container:rslave myapp

# rshared: Shared with host and other containers
docker run -d -v /host:/container:rshared myapp
```

---

## 9.4 Real-World Example: Database Persistence

### MySQL with Volume Persistence:

```bash
# Create volume for database
docker volume create mysql-data

# Run MySQL container
docker run -d \
  --name mysql-primary \
  -e MYSQL_ROOT_PASSWORD=rootpass123 \
  -e MYSQL_DATABASE=myapp \
  -e MYSQL_USER=appuser \
  -e MYSQL_PASSWORD=apppass \
  -p 3306:3306 \
  -v mysql-data:/var/lib/mysql \
  --restart=unless-stopped \
  mysql:8.0

# Verify running
docker ps
# Output: mysql-primary ... Status: Up ...

# View database files
docker exec mysql-primary ls -la /var/lib/mysql

# Create some data
docker exec -it mysql-primary mysql -u root -p -e "
CREATE TABLE users (id INT, name VARCHAR(100));
INSERT INTO users VALUES (1, 'Alice'), (2, 'Bob');
SELECT * FROM users;
"

# Stop container
docker stop mysql-primary

# Data still exists in volume!
# Restart container
docker start mysql-primary

# Data is still there!
docker exec -it mysql-primary mysql -u root -p -e "SELECT * FROM users;"
# Output: id=1, name=Alice ... (data persisted!)

# Remove container but keep data
docker rm mysql-primary

# Create new container with same volume
docker run -d \
  --name mysql-restored \
  -e MYSQL_ROOT_PASSWORD=rootpass123 \
  -p 3306:3306 \
  -v mysql-data:/var/lib/mysql \
  mysql:8.0

# All data recovered!
docker exec mysql-restored mysql -u root -p -e "SELECT * FROM users;"
```

---

## 9.5 Multi-Container Volume Sharing

### Sharing Data Between Containers:

```bash
# Create shared volume
docker volume create shared-data

# Container 1: Write data
docker run -d \
  --name writer \
  -v shared-data:/data \
  python:3.11 \
  python -c "
import time, os
for i in range(5):
    with open('/data/output.txt', 'a') as f:
        f.write(f'Line {i}\n')
    time.sleep(1)
"

# Container 2: Read data
docker run -it \
  --name reader \
  -v shared-data:/data \
  python:3.11 \
  tail -f /data/output.txt

# Output shows data written by other container in real-time!
```

### Database + Application Example:

```bash
# Database container
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=pass \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15

# Application container with database access
docker run -d \
  --name myapp \
  -link postgres:db \
  -e DATABASE_URL=postgresql://postgres:pass@db/postgres \
  myapp:latest

# Share config volume
docker volume create app-config

# App 1
docker run -d \
  --name app1 \
  -v app-config:/etc/config:ro \
  myapp:latest

# App 2 (same config)
docker run -d \
  --name app2 \
  -v app-config:/etc/config:ro \
  myapp:latest
```

---

## 9.6 Volume Cleanup and Management

### Listing Volumes:

```bash
# List all volumes
docker volume ls

# List only volume names
docker volume ls -q

# List dangling volumes (unused)
docker volume ls -f dangling=true
```

### Removing Volumes:

```bash
# Remove unused volume
docker volume rm myvolume

# Remove dangling volumes
docker volume prune

# Remove with confirmation
docker volume prune --force

# View what will be removed first
docker volume prune --dry-run
```

### Important: Volume Deletion Warning

```bash
# DANGEROUS: This deletes data!
docker volume rm myvolume
# Now data is permanently gone!

# Always backup critical data first:
docker run --rm \
  -v myvolume:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/backup.tar.gz -C /data .
```

---

## 9.7 Advanced Volume Scenarios

### Volume Plugin Example (NFS):

```bash
# Create NFS volume
docker volume create \
  --driver local \
  --opt type=nfs \
  --opt o=addr=nfs-server.com,vers=4,soft \
  --opt device=:/export/data \
  nfs-volume

# Use NFS volume
docker run -d \
  -v nfs-volume:/data \
  myapp:latest
```

### Logs with Volumes:

```bash
# Create logs volume
docker volume create app-logs

# Run application with logs
docker run -d \
  --name myapp \
  -v app-logs:/app/logs \
  myapp:latest

# View logs from host
cat /var/lib/docker/volumes/app-logs/_data/app.log

# Or from container
docker exec myapp cat /app/logs/app.log
```

---

## 9.8 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Why volumes are needed (data persistence)
- ✓ Named volumes vs bind mounts vs tmpfs
- ✓ Volume mounting modes (read/write, read-only)
- ✓ Real-world database persistence example
- ✓ Multi-container volume sharing
- ✓ Volume management and cleanup
- ✓ Advanced volume scenarios

---

# SECTION 10: DOCKER NETWORKS

## 10.1 Default Networks

Docker provides three default networks:

```bash
# List networks
docker network ls

# Output:
# NETWORK ID     NAME      DRIVER    SCOPE
# f7c1f5b2e1a2   bridge    bridge    local
# 8d9e4c3b1f0e   host      host      local
# 2a7b6d9c4e5f   none      null      local
```

### 1. Bridge Network (Default)

```
Host Machine
┌─────────────────────────────────┐
│  Docker Engine                  │
│  ┌──────────────────────────┐   │
│  │ Docker Virtual Bridge    │   │
│  │ (172.17.0.1)            │   │
│  ├──────────────────────────┤   │
│  │                          │   │
│  │ Container 1  Container 2 │   │
│  │ 172.17.0.2   172.17.0.3  │   │
│  │                          │   │
│  └──────────────────────────┘   │
└─────────────────────────────────┘
```

```bash
# Default when running container without --network
docker run -d myapp:latest

# Containers on bridge can reach each other by IP
# But NOT by container name (unless custom network)

# Bridge network details
docker network inspect bridge
```

### 2. Host Network

Container uses host's network directly:

```bash
docker run -d \
  --network host \
  nginx:latest

# Container shares host's network namespace
# Ports are directly on host (no mapping needed)
# Better performance, less isolation
```

### 3. None Network

Container has no network:

```bash
docker run -d \
  --network none \
  myapp:latest

# Container has only loopback interface (localhost)
# No external connectivity
# For isolated/security-critical tasks
```

---

## 10.2 Custom Networks (Recommended)

Create networks for better container communication:

```bash
# Create custom bridge network
docker network create myapp-network

# Create custom overlay network (swarm/Kubernetes)
docker network create --driver overlay myapp-overlay

# Run containers on custom network
docker run -d \
  --name backend \
  --network myapp-network \
  myapp:latest

docker run -d \
  --name db \
  --network myapp-network \
  mysql:8.0

# Containers can reach each other by name!
docker exec backend ping db
# Works! (default bridge can't do this)
```

### Why Custom Networks?

```
Default Bridge:
- Containers reach each other by IP only
- No service discovery by name
- Poor container-to-container communication

Custom Network:
- Containers reach each other by container name
- Built-in DNS service discovery
- Isolated from other containers
- Better for multi-container apps
```

---

## 10.3 Container-to-Container Communication

### Example: Backend + Database

```bash
# Create network
docker network create backend-network

# Start database
docker run -d \
  --name postgres-db \
  --network backend-network \
  -e POSTGRES_PASSWORD=dbpass \
  postgres:15

# Start backend (can reach DB by name)
docker run -d \
  --name api-server \
  --network backend-network \
  -e DATABASE_URL="postgresql://postgres:dbpass@postgres-db:5432/mydb" \
  myapi:latest

# Inside api-server, can connect to postgres-db:5432
# Docker DNS resolves "postgres-db" to container's IP automatically
```

### Accessing Multiple Networks:

```bash
# Create two networks
docker network create frontend-net
docker network create backend-net

# Container on both networks
docker run -d \
  --name api \
  --network frontend-net \
  myapi:latest

# Connect to second network
docker network connect backend-net api

# Now api can reach containers on either network!
docker network inspect backend-net
# Shows api as connected
```

---

## 10.4 Port Publishing vs Network Isolation

### Not Publishing Ports (Internal Only):

```bash
# Database (no port publish, internal only)
docker run -d \
  --name db \
  --network backend-net \
  mysql:8.0
# Not accessible from host or external

# API container can still access it internally
docker run -d \
  --name api \
  --network backend-net \
  myapi:latest
```

### Publishing Ports (External Access):

```bash
# API exposed to host
docker run -d \
  --name api \
  --network backend-net \
  -p 8000:8000 \
  myapi:latest
# Accessible from host at localhost:8000

# Database still internal-only
docker run -d \
  --name db \
  --network backend-net \
  mysql:8.0
# Only accessible from api container, not from host
```

---

## 10.5 Network Isolation & Security

### Containers on Different Networks Can't Communicate:

```bash
# Network 1
docker network create app1-net
docker run -d --name app1-db --network app1-net mysql:8.0

# Network 2
docker network create app2-net
docker run -d --name app2-db --network app2-net mysql:8.0

# These are isolated:
# app1-db CANNOT reach app2-db
# Even though both are MySQL

# Provides security isolation
```

---

## 10.6 Networking Best Practices

### 1. Always Use Custom Networks

```bash
# Bad: Using default bridge
docker run -d myapp:latest
docker run -d mysql:8.0
# Containers can only reach each other by IP

# Good: Custom network with DNS
docker network create mynet
docker run -d --network mynet --name app myapp:latest
docker run -d --network mynet --name db mysql:8.0
# Containers reach each other by name
```

### 2. Explicit Port Publishing

```bash
# Bad: Publishing all ports
docker run -d -P myapp:latest

# Good: Explicit ports
docker run -d -p 8000:8000 myapp:latest
# Clear what's exposed
```

### 3. Environment Variables for Service Discovery

```dockerfile
# Dockerfile
ENV DB_HOST=db
ENV DB_PORT=5432
ENV REDIS_HOST=cache
ENV REDIS_PORT=6379
```

```bash
# Docker Compose (next section) makes this easier
# But for now, good practice:
docker run -d \
  --name app \
  --network mynet \
  -e DB_HOST=postgres \
  -e REDIS_HOST=redis \
  myapp:latest
```

---

## 10.7 Network Commands Summary

```bash
# List networks
docker network ls

# Create network
docker network create mynet
docker network create --driver overlay myoverlay

# Inspect network
docker network inspect mynet

# Connect container to network
docker network connect mynet container_name

# Disconnect container from network
docker network disconnect mynet container_name

# Remove network
docker network rm mynet

# Remove unused networks
docker network prune
```

---

## 10.8 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Default networks (bridge, host, none)
- ✓ Custom networks and service discovery
- ✓ Container-to-container communication
- ✓ Port publishing and exposure
- ✓ Network isolation for security
- ✓ Best practices for networking

---

# CONTINUING...

[Due to length constraints, I'll create this as a comprehensive markdown file. Let me save it to disk and continue with remaining sections]

