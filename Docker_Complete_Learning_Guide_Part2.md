# SECTION 11: ENVIRONMENT VARIABLES & CONFIGURATION

## 11.1 Passing Environment Variables

### Via Command Line:

```bash
# Single variable
docker run -d -e API_KEY=secret123 myapp:latest

# Multiple variables
docker run -d \
  -e API_KEY=secret123 \
  -e DATABASE_URL="postgresql://localhost/mydb" \
  -e LOG_LEVEL=DEBUG \
  -e ENVIRONMENT=production \
  myapp:latest

# Verify variables in container
docker exec myapp env | grep API_KEY
# Output: API_KEY=secret123
```

### Via Environment File:

Create `.env` file:
```
API_KEY=secret123
DATABASE_URL=postgresql://localhost/mydb
LOG_LEVEL=DEBUG
ENVIRONMENT=production
```

```bash
# Load from file
docker run -d --env-file .env myapp:latest

# Multiple files
docker run -d \
  --env-file .env.common \
  --env-file .env.prod \
  myapp:latest

# Override with command line (takes precedence)
docker run -d \
  --env-file .env \
  -e LOG_LEVEL=INFO \
  myapp:latest
# LOG_LEVEL will be INFO (overrides file)
```

---

## 11.2 Environment Variables in Dockerfile

### Setting Defaults in  Image:

```dockerfile
# Set default values (can be overridden at runtime)
ENV ENVIRONMENT=development
ENV LOG_LEVEL=INFO
ENV DEBUG=false

# Multiple variables
ENV APP_NAME=myapp \
    APP_VERSION=1.0.0 \
    APP_PORT=8000

# Use in subsequent instructions
ENV WORKDIR=/app
WORKDIR $WORKDIR

# Use in RUN
RUN echo "Building app for $ENVIRONMENT"
```

### Runtime Override:

```bash
# Image has ENV ENVIRONMENT=development
docker run -d myapp:latest
# Container has: ENVIRONMENT=development

# Override at runtime
docker run -d -e ENVIRONMENT=production myapp:latest
# Container has: ENVIRONMENT=production
```

---

## 11.3 Managing Secrets Securely

### Problem: Hardcoding Secrets

```dockerfile
# DON'T DO THIS!
ENV API_KEY=hardcoded_secret_123
ENV DATABASE_PASSWORD=mypassword
# Visible in container, logs, and image layers!
```

### Solution 1: Use Secret Management Tools

```bash
# Docker Secrets (for Swarm mode)
echo "my_secret_value" | docker secret create my_secret -

# Use in service
docker service create \
  --secret my_secret \
  -e SECRET_FILE=/run/secrets/my_secret \
  myapp:latest
```

### Solution 2: Environment Files at Runtime

```bash
# Create secure .env file (gitignored)
# .env (not in version control)
DATABASE_PASSWORD=secure_password
API_KEY=secure_key

# .env.example (commit to repo)
DATABASE_PASSWORD=CHANGE_THIS
API_KEY=CHANGE_THIS

# Run with env file
docker run -d --env-file .env myapp:latest

# .gitignore
.env
.env.local
*.key
*.pem
```

### Solution 3: Build Arguments for Non-Secrets

```dockerfile
ARG PYTHON_VERSION=3.11
FROM python:${PYTHON_VERSION}

ARG APP_VERSION=1.0.0
ENV APP_VERSION=${APP_VERSION}

# Only non-secret values as build args
```

```bash
docker build --build-arg PYTHON_VERSION=3.9 .
docker build --build-arg APP_VERSION=2.0.0 .
```

### Solution 4: External Configuration

```bash
# Mount configuration file at runtime
docker run -d \
  -v ./config/app.env:/app/config/app.env:ro \
  myapp:latest

# Application reads from mounted file
```

**Python Example - Reading from File:**
```python
from pathlib import Path
from dotenv import load_dotenv
import os

# Load from mounted file
load_dotenv('/app/config/app.env')

api_key = os.getenv('API_KEY')
db_password = os.getenv('DATABASE_PASSWORD')
```

---

## 11.4 Configuration Management Patterns

### Pattern 1: Environment Variables for Different Stages

```bash
# Development
docker run -d --env-file .env.dev myapp:latest

# Staging
docker run -d --env-file .env.staging myapp:latest

# Production
docker run -d --env-file .env.prod myapp:latest

# Each .env file contains different values
```

**Files:**
```
.env.dev
DATABASE_URL=sqlite:///dev.db
DEBUG=true
LOG_LEVEL=DEBUG

.env.staging
DATABASE_URL=postgresql://staging-db/mydb
DEBUG=false
LOG_LEVEL=INFO

.env.prod
DATABASE_URL  postgresql://prod-db/mydb
DEBUG=false
LOG_LEVEL=WARN
SENTRY_DSN=https://...
```

### Pattern 2: Default + Override

```dockerfile
# Image has sensible defaults
FROM python:3.11
ENV DATABASE_URL="sqlite:///app.db"
ENV LOG_LEVEL="INFO"
ENV DEBUG="false"
ENV API_PORT="8000"
```

```bash
# Development: override as needed
docker run -d \
  -e DATABASE_URL="postgresql://localhost/dev" \
  -e DEBUG=true \
  -e LOG_LEVEL=DEBUG \
  myapp:latest

# Production: most values from env file
docker run -d --env-file .env.prod myapp:latest
```

---

## 11.5 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Passing environment variables via CLI
- ✓ Loading from environment files
- ✓ Setting defaults in Dockerfile
- ✓ Runtime overrides
- ✓ Secure secret management
- ✓ Configuration patterns for different environments

---

# SECTION 12: PORT MAPPING & SERVICE EXPOSURE

## 12.1 Understanding Ports

### Container vs Host Ports:

```
Application listens on port 8000 inside container:
app.listen(8000)

But host also has ports (0-65535)

Port Mapping bridges them:
Host Port → Container Port
8000:8000 means:
  access host:8000 → reaches container:8000
```

---

## 12.2 Port Mapping Syntax

### Basic Syntax:

```bash
# Single port mapping
docker run -d -p 8000:80 nginx:latest
# HOST_PORT:CONTAINER_PORT

# Publish on all interfaces
docker run -d -p 0.0.0.0:8000:80 nginx:latest

# Publish on localhost only
docker run -d -p 127.0.0.1:8000:80 nginx:latest

# Map multiple ports
docker run -d \
  -p 8000:80 \
  -p 8443:443 \
  -p 3000:3000 \
  myapp:latest

# Different host and container ports
docker run -d -p 9000:8000 myapp:latest
# Host 9000 → Container 8000

# Let Docker assign dynamic port
docker run -d -p :80 nginx:latest
# Docker chooses random port from 32768+

# UDP port
docker run -d -p 8053:53/udp dnsmasq:latest

# Both TCP and UDP
docker run -d \
  -p 8000:8000/tcp \
  -p 8000:8000/udp \
  myapp:latest
```

---

## 12.3 Checking Port Mappings

### List Port Mappings:

```bash
# See port mappings for container
docker port myapp

# Output:
# 8000/tcp -> 0.0.0.0:8000
# 8443/tcp -> 0.0.0.0:8443

# Get detailed info
docker inspect myapp | grep -A 5 "Ports"

# Get as JSON
docker inspect -f '{{json .NetworkSettings.Ports}}' myapp
```

---

## 12.4 Network Scenarios

### Scenario 1: Expose Only Internal Ports

```bash
# Database (no port mapping - internal only)
docker run -d \
  --name db \
  --network backend-net \
  mysql:8.0
# Not accessible from host

# API (port exposed)
docker run -d \
  --name api \
  --network backend-net \
  -p 8000:8000 \
  myapi:latest
# Accessible from host at localhost:8000
# But can still reach db via container name
```

### Scenario 2: Expose All Services

```bash
# Database (exposed for local use)
docker run -d \
  --name db \
  -p 3306:3306 \
  mysql:8.0

# Cache (exposed for local use)
docker run -d \
  --name redis \
  -p 6379:6379 \
  redis:7

# API (exposed to clients)
docker run -d \
  --name api \
  -p 8000:8000 \
  myapi:latest

# All accessible from host
```

---

## 12.5 Production Considerations

### Load Balancing:

```
Host (8080 is exposed)
│
├─ Container 1 (port :8000/tcp)
├─ Container 2 (port :8000/tcp)
└─ Container 3 (port :8000/tcp)

Users access: localhost:8080
Load balancer (nginx, haproxy) forwards to:
  - :8000 (random container)
  - :8000 (random container)
  - :8000 (random container)
```

Implement with Nginx:
```bash
# Multiple app containers (no port publish)
docker run -d --name app1 --network prod-net myapp:latest
docker run -d --name app2 --network prod-net myapp:latest
docker run -d --name app3 --network prod-net myapp:latest

# Nginx load balancer (port publish)
docker run -d \
  --name nginx \
  --network prod-net \
  -p 80:80 \
  -v ./nginx.conf:/etc/nginx/nginx.conf:ro \
  nginx:alpine
```

---

## 12.6 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Container vs host port mapping
- ✓ Port mapping syntax and options
- ✓ Checking port mappings
- ✓ Exposing services to host
- ✓ Internal-only services
- ✓ Production load balancing patterns

---

# SECTION 13: DOCKER LOGS & DEBUGGING

## 13.1 Accessing Container Logs

### Basic docker logs Command:

```bash
# View all logs
docker logs myapp

# Follow logs (like tail -f)
docker logs -f myapp

# Last 100 lines
docker logs --tail 100 myapp

# With timestamps
docker logs -t myapp

# Since specific time
docker logs --since 2024-04-06T10:00:00 myapp

# Since duration
docker logs --since 10m myapp

# Until specific time
docker logs --until 5m myapp

# Combine timestamp with follow
docker logs -tf --tail 50 myapp
```

---

## 13.2 Logging Best Practices

### 1. Log to STDOUT/STDERR:

**Don't (writing to files inside container):**
```python
import logging
logging.basicConfig(filename="/app/logs/app.log")
```

**Do (write to stdout):**
```python
import logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
# Logs to stdout, Docker captures automatically
```

### 2. Structured Logging:

```python
import json
import logging

# Structured logs (JSON format)
def log_event(event, **kwargs):
    log_entry = {"event": event, **kwargs}
    print(json.dumps(log_entry))

log_event("user_login", user_id=123, timestamp="2024-04-06T10:22:00Z")
# Output: {"event": "user_login", "user_id": 123, "timestamp": "..."}

# Easy to parse and analyze
```

### 3. Log Levels:

```python
import logging

logger = logging.getLogger(__name__)

# Use appropriate levels
logger.debug("DEBUG: Detailed diagnostic info")       # Dev only
logger.info("INFO: General informational message")     # Always
logger.warning("WARNING: Something unexpected")        # Alerts
logger.error("ERROR: Something failed")                # Alerts
logger.critical("CRITICAL: System may not be usable")  # Alerts

# Configure level
logging.getLogger().setLevel(logging.INFO)
# Only INFO and above logged, DEBUG suppressed
```

---

## 13.3 Debugging Running Containers

### 1. Execute Command in Running Container:

```bash
# Open interactive shell
docker exec -it myapp /bin/bash

# Inside container:
root@container:/app# ls
root@container:/app# ps aux
root@container:/app# netstat -tlnp  # See listening ports
root@container:/app# env  # See all environment variables
root@container:/app# cat /var/log/app.log
```

### 2. Inspect Container Details:

```bash
# Full container information (JSON)
docker inspect myapp

# Specific information:
docker inspect -f '{{.State.Status}}' myapp       # running/stopped
docker inspect -f '{{.Config.Image}}' myapp       # Image used
docker inspect -f '{{.NetworkSettings.IPAddress}}' myapp  # IP address
docker inspect -f '{{.Config.Env}}' myapp         # Environment variables
docker inspect -f '{{.HostConfig.Memory}}' myapp  # Memory limit
```

### 3. Debugging Network Issues:

```bash
# From container, test connectivity
docker exec myapp ping db
docker exec myapp curl https://api.example.com
docker exec myapp nslookup redis  # DNS resolution
docker exec myapp telnet db 5432  # Port connectivity

# From host, check container network
docker network inspect backend-net

# Check container IP
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' myapp
```

### 4. Resource Usage:

```bash
# Real-time resource stats
docker stats
# Continuous output of CPU, memory, network

# Stats for specific container
docker stats myapp

# One-time snapshot
docker stats --no-stream myapp

# Output format:
# CONTAINER     CPU %     MEM USAGE / LIMIT     MEM %     NET I/O
# myapp         0.25%     128 MB / 512 MB       25%       1.2 MB / 890 KB
```

---

## 13.4 Common Debugging Scenarios

### Scenario 1: Container Crashes Immediately

```bash
# Check logs for error
docker logs myapp

# Check container state
docker ps -a  # Should show exited status

# Inspect container
docker inspect myapp | grep -A 5 "State"

# Common causes:
# 1. Application exited cleanly (normal)
# 2. Application crashed (error in logs)
# 3. Wrong entry point
# 4. Missing dependencies

# Solution: Run with interactive terminal to debug
docker run -it myapp:latest /bin/bash
# Try running main command manually
```

### Scenario 2: Port Not Accessible

```bash
# Check if container is running
docker ps  # myapp should be there

# Check port mapping
docker port myapp

# If not listed, the port wasn't mapped!
# Restart with port mapping:
docker run -d -p 8000:8000 myapp:latest

# Check if port is listening on host
netstat -tlnp | grep 8000
curl localhost:8000  # Should work now
```

### Scenario 3: Container Out of Memory

```bash
# Check resource usage
docker stats myapp

# Check if killed by OOM
docker inspect myapp | grep -i oom
# If OomKilled is true, container hit memory limit

# Increase memory limit
docker run -d \
  --memory 1g \
  myapp:latest

# Set memory reservation (soft limit)
docker run -d \
  --memory-reservation 512m \
  myapp:latest
```

---

## 13.5 Logging Drivers

### JSON File (Default):

```bash
# Docker stores logs as JSON files
# Located at: /var/lib/docker/containers/[container-id]/

# Configure JSON driver
docker run -d \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  myapp:latest
```

### Syslog Driver:

```bash
# Send logs to syslog
docker run -d \
  --log-driver syslog \
  --log-opt syslog-address=udp://localhost:514 \
  myapp:latest
```

### Splunk Driver:

```bash
docker run -d \
  --log-driver splunk \
  --log-opt splunk-token=123456 \
  --log-opt splunk-url=https://splunk.example.com:8088 \
  myapp:latest
```

---

## 13.6 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Accessing container logs
- ✓ Logging best practices (stdout, structured)
- ✓ Log levels and configuration
- ✓ Debugging running containers
- ✓ Resource monitoring and stats
- ✓ Common debugging scenarios
- ✓ Different logging drivers

---

# SECTION 14: CONTAINER LIFECYCLE MANAGEMENT

## 14.1 Container States

```
States and Transitions:

Created
  ↓
Started (Running)
  ↓
Stopped / Exited
  ↓
Removed (deleted from system)

Other states:
- Paused: Container running but suspended
- Restarting: Container is restarting
- Dead: Container failed to start
- OOMKilled: Killed due to memory limit
```

---

## 14.2 Container Lifecycle Commands

### 1. Create Container (without starting):

```bash
# Create but don't start
docker create --name myapp myapp:latest

# Check state
docker ps -a
# Status: Created

# Start it later
docker start myapp

# Still same container
docker ps
# Status: Up ...
```

### 2. Start Container:

```bash
# Start stopped container
docker start myapp

# Start multiple containers
docker start container1 container2 container3

# Start with output in foreground
docker start -a myapp

# Attach to running container
docker start -ai myapp  # Attach and interactive
```

### 3. Stop Container:

```bash
# Stop gracefully (allows cleanup)
docker stop myapp
# Sends SIGTERM, waits up to 10 seconds

# Stop with timeout
docker stop --time=30 myapp
# Waits 30 seconds before force killing

# Force stop immediately
docker kill myapp
# Sends SIGKILL immediately
```

### 4. Pause/Unpause:

```bash
# Pause container (suspend process)
docker pause myapp

# Container still running in system, but frozen
docker ps
# Status: Up ... (Paused)

# Resume
docker unpause myapp

# Status: Up ...
```

### 5. Restart Container:

```bash
# Restart (stop then start)
docker restart myapp

# With timeout
docker restart --time=30 myapp

# Restart multiple
docker restart container1 container2
```

### 6. Remove Container:

```bash
# Remove stopped container
docker rm myapp

# Force remove running container
docker rm -f myapp

# Remove with volumes
docker rm -v myapp  # Also deletes anonymous volumes

# Remove multiple
docker rm container1 container2 container3

# Remove all stopped containers
docker container prune

# Remove with confirmation
docker container prune --force
```

---

## 14.3 Graceful Shutdown

### Container Entry Point Matters:

```dockerfile
# Bad: Shell form
CMD python app.py
# SIGTERM not passed to app.py, kills shell instead

# Good: Exec form
CMD ["python", "app.py"]
# SIGTERM directly passed to app.py
```

### Handling Signals in Application:

**Python Example:**
```python
import signal
import sys

def signal_handler(sig, frame):
    print("Gracefully shutting down...")
    # Cleanup: close DB connections, etc.
    sys.exit(0)

signal.signal(signal.SIGTERM, signal_handler)

# Application code
while True:
    # Do work
    pass
```

### Docker Graceful Shutdown:

```bash
# Stop gracefully (SIGTERM → wait 10s → SIGKILL)
docker stop myapp

# Customize wait time
docker stop --time=30 myapp

# Immediate force stop
docker kill myapp
```

---

## 14.4 Container Inspection

### Basic Inspection:

```bash
# JSON output of all container details
docker inspect myapp

# Pretty print
docker inspect myapp | jq .

# Specific fields:
docker inspect -f '{{.Name}}' myapp
docker inspect -f '{{.State.Pid}}' myapp  # Process ID
docker inspect -f '{{.Created}}' myapp
docker inspect -f '{{.Image}}' myapp
```

### Detailed Information:

```bash
docker inspect myapp | jq '.[] | {
  Name: .Name,
  Image: .Config.Image,
  Status: .State.Status,
  StartedAt: .State.StartedAt,
  FinishedAt: .State.FinishedAt,
  IP: .NetworkSettings.IPAddress,
  Memory: .HostConfig.Memory,
  CPU_Limit: .HostConfig.CpuShares
}'
```

---

## 14.5 Process Management

### List Processes in Container:

```bash
# See processes running inside container
docker exec myapp ps aux

# Using top (if available)
docker top myapp

# Get specific process info
docker exec myapp ps -ef | grep python
```

### Send Signals:

```bash
# SIGTERM (graceful shutdown)
docker kill --signal=SIGTERM myapp

# SIGKILL (force)
docker kill --signal=SIGKILL myapp

# Other signals
docker kill --signal=SIGUSR1 myapp
```

---

## 14.6 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Container states and transitions
- ✓ Lifecycle commands (create, start, stop, rm)
- ✓ Graceful shutdown handling
- ✓ Container inspection and checking status
- ✓ Process management inside containers
- ✓ Signal handling and cleanup

---

# SECTION 15: IMAGE OPTIMIZATION & MULTI-STAGE BUILDS

## 15.1 Why Image Size Matters

### Large Images:
- Slow to push/pull from registry
- Waste storage on servers
- Slow deployment
- More security surface area

### Image Size Comparison:
```
Large Image:      2 GB (ubuntu + bloat)
Optimized Image:  150 MB (slim + essentials)
Tiny Image:       50 MB (alpine)

10 containers:
Large:   20 GB total
Tiny:    500 MB total
```

---

## 15.2 Reducing Image Size

### 1. Choose Slim/Alpine Base Images:

```dockerfile
# Bad - 1.2 GB
FROM ubuntu:22.04
RUN apt-get update && apt-get install python3

# Better - 900 MB
FROM python:3.11

# Best - 200 MB
FROM python:3.11-alpine
```

### 2. Minimize Layers & Cleanup:

```dockerfile
# Bad - 2 layers, 800 MB
RUN apt-get update
RUN apt-get install -y wget curl git

# Good - 1 layer, 100 MB
RUN apt-get update && \
    apt-get install --no-install-recommends -y wget curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

### 3. .dockerignore:

```
# Exclude unnecessary files from build context
__pycache__
*.pyc
*.pyo
.git
.gitignore
.venv
venv
node_modules
.pytest_cache
.coverage
htmlcov/
dist/
build/
*.egg-info/
```

### 4. Separate Build Dependencies:

```dockerfile
# Bad - includes build tools in final image (2 GB)
RUN apt-get install build-essential
RUN apt-get install python3-dev
RUN pip install -r requirements.txt
# All build tools still in final image!

# Good - remove after use (200 MB)
RUN apt-get install build-essential python3-dev && \
    pip install -r requirements.txt && \
    apt-get remove -y build-essential python3-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/*
```

---

## 15.3 Multi-Stage Builds

Multi-stage builds use multiple FROM statements to keep image small.

### Concept:

```
Stage 1 (Builder):
- Start with large base image
- Install build tools
- Build/compile application
- Size: Large (doesn't matter)

Stage 2 (Runtime):
- Start with small base image
- Copy only built artifacts from Stage 1
- Size: Small (what users get)
```

### Example 1: Python Application

```dockerfile
# Stage 1: Builder
FROM python:3.11 as builder

WORKDIR /build

# Copy requirements
COPY requirements.txt .

# Install dependencies
RUN pip install --user --no-cache-dir -r requirements.txt
# --user installs to ~/.local/

# Stage 2: Runtime (small)
FROM python:3.11-slim

# Set environment
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy only Python packages from builder
COPY --from=builder /root/.local /root/.local

# Set PATH
ENV PATH=/root/.local/bin:$PATH

# Copy application (small layer)
COPY app.py .

# Run
CMD ["python", "app.py"]

# Result:
# Stage 1 image: 1 GB (discarded after build)
# Stage 2 image: 200 MB (final image)
# User gets: 200 MB
```

### Example 2: Node.js Application

```dockerfile
# Stage 1: Builder
FROM node:20 as builder

WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
# Builds /app/dist/

# Stage 2: Runtime
FROM node:20-alpine

WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package.json .

EXPOSE 3000
CMD ["node", "dist/index.js"]

# Result: Final image ~150 MB
```

### Example 3: Go Application

```dockerfile
# Stage 1: Builder
FROM golang:1.21 as builder

WORKDIR /app
COPY . .
RUN CGO_ENABLED=0 go build -o myapp main.go

# Stage 2: Runtime (minimal)
FROM alpine:3.19

RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/myapp .

EXPOSE 8000
CMD ["./myapp"]

# Result: Final image ~15 MB (Alpine + small binary)!
```

---

## 15.4 .dockerignore Best Practices

```
# Version control
.git
.gitignore
.gitattributes

# IDE
.vscode
.idea
*.swp
*.swo

# Python
__pycache__
*.pyc
*.pyo
*.egg-info
.venv
venv
.tox

# Node
node_modules
npm-debug.log
yarn-error.log

# Tests
.pytest_cache
.coverage
htmlcov

# Build artifacts
dist/
build/
*.egg

# Documentation
docs/
README.md

# Environment files
.env
.env.local
.env.*.local

# OS files
.DS_Store
Thumbs.db

# Runtime files
*.log
tmp/
```

---

## 15.5 Image Optimization Checklist

```-checklist
[ ] Using slim or alpine base image
[ ] Combining RUN commands with &&
[ ] Removing package manager cache
[ ] Deleting build dependencies after use
[ ] Using .dockerignore to exclude files
[ ] Using multi-stage builds
[ ] Not running as root (non-root user)
[ ] Not copying unnecessary files
[ ] COPY before RUN for layer caching
[ ] Using EXPOSE instead of publishing in Dockerfile
```

---

## 15.6 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Why image size matters
- ✓ Techniques to reduce image size
- ✓ .dockerignore usage
- ✓ Multi-stage builds concept and benefits
- ✓ Multi-stage build examples (Python, Node, Go)
- ✓ Image optimization best practices

---

# SECTION 16: DOCKER REGISTRY & IMAGE DISTRIBUTION

## 16.1 Docker Hub (Public Registry)

### Registering and Logging In:

```bash
# Create account on https://hub.docker.com/

# Login to Docker Hub
docker login
# Prompts for username and password
# Credentials saved to ~/.docker/config.json

# Logout
docker logout
```

### Pushing Images:

```bash
# Tag image for Docker Hub
docker tag myapp:latest myusername/myapp:latest

# Or during build
docker build -t myusername/myapp:1.0.0 .

# Push to Docker Hub
docker push myusername/myapp:latest

# Push specific tag
docker push myusername/myapp:1.0.0

# Multiple tags
docker push myusername/myapp:1.0.0
docker push myusername/myapp:latest

# View what will be pushed
docker image inspect myusername/myapp:latest

# Push progress
# Pushing layers to registry...
# Layer size: 50 MB
# Overall progress: ████████░░ 80%
```

### Pulling Images:

```bash
# Pull from Docker Hub
docker pull myusername/myapp:latest

# Full reference (explicit)
docker pull docker.io/myusername/myapp:latest

# Default: latest tag
docker pull myusername/myapp
# Equivalent to: docker pull myusername/myapp:latest

# Specific version
docker pull myusername/myapp:1.0.0

# Pull only if not already local
docker pull myusername/myapp:latest
# If already local, uses cached version
```

---

## 16.2 Image Tags and Versioning Strategy

### Semantic Versioning:

```bash
# Build and tag with version
docker build -t myusername/myapp:1.5.3 .

# Also tag as latest
docker tag myusername/myapp:1.5.3 myusername/myapp:latest

# Also tag minor version
docker tag myusername/myapp:1.5.3 myusername/myapp:1.5

# Also tag major version
docker tag myusername/myapp:1.5.3 myusername/myapp:1

# Push all tags
docker push myusername/myapp:1.5.3
docker push myusername/myapp:1.5
docker push myusername/myapp:1
docker push myusername/myapp:latest

# Now on Docker Hub:
# - Users can pull myusername/myapp:1.5.3 (specific)
# - Users can pull myusername/myapp:1.5 (minor updates)
# - Users can pull myusername/myapp:1 (major updates)
# - Users can pull myusername/myapp:latest (bleeding edge)
```

### Date-based Versioning:

```bash
docker build -t myusername/myapp:2024-04-06 .
docker push myusername/myapp:2024-04-06

# Useful for CI/CD timestamps
docker build -t myusername/myapp:$(date +%Y-%m-%d_%H:%M) .
```

### Git-based Versioning:

```bash
# Use commit hash
docker build -t myusername/myapp:$(git rev-parse --short HEAD) .

# Use git tag
docker build -t myusername/myapp:$(git describe --tags) .

# Automated in CI/CD
```

---

## 16.3 Private Docker Registries

### AWS Elastic Container Registry (ECR):

```bash
# Login to AWS ECR
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin 123456789.dkr.ecr.us-east-1.amazonaws.com

# Tag image
docker tag myapp:latest 123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:latest

# Push
docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:latest

# Pull
docker pull 123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:latest
```

### Azure Container Registry:

```bash
# Login
az acr login --name myregistry

# Tag image
docker tag myapp:latest myregistry.azurecr.io/myapp:latest

# Push
docker push myregistry.azurecr.io/myapp:latest

# Pull
docker pull myregistry.azurecr.io/myapp:latest
```

### Google Container Registry:

```bash
# Configure auth
gcloud auth configure-docker

# Tag image
docker tag myapp:latest gcr.io/my-project/myapp:latest

# Push
docker push gcr.io/my-project/myapp:latest

# Pull
docker pull gcr.io/my-project/myapp:latest
```

### Self-Hosted Registry (Docker Registry):

```bash
# Run registry container
docker run -d \
  --name registry \
  -p 5000:5000 \
  -v registry-data:/var/lib/registry \
  registry:2

# Tag image for local registry
docker tag myapp:latest localhost:5000/myapp:latest

# Push to local registry
docker push localhost:5000/myapp:latest

# Pull from local registry
docker pull localhost:5000/myapp:latest
```

---

## 16.4 Best Practices for Registry

### 1. Never Use 'latest' in Production:

```bash
# Bad - ambiguous
docker run myregistry.io/myapp:latest

# Good - explicit version
docker run myregistry.io/myapp:1.5.3
```

### 2. Use Private Registries for Proprietary Code:

```bash
docker push myregistry.io/internal/proprietary-app:1.0.0
# Only accessible with credentials
```

### 3. Clean Up Old Images:

```bash
# Delete old image from registry
# Method varies by registry (Docker Hub UI, AWS CLI, etc.)

# Locally:
docker rmi myusername/myapp:0.9.0
```

### 4. Sign Images for Security:

```bash
# Docker Content Trust (DCT) signs images
export DOCKER_CONTENT_TRUST=1
docker push myusername/myapp:1.0.0
# Requires key passphrase
```

---

## 16.5 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Docker Hub and public registries
- ✓ Pushing and pulling images
- ✓ Image tagging strategies
- ✓ Private registries (AWS, Azure, Google, self-hosted)
- ✓ Image versioning best practices
- ✓ Registry security and authentication

---

# SECTION 17: DOCKER COMPOSE FOR MULTI-CONTAINER APPS

## 17.1 What is Docker Compose?

Docker Compose is a tool for defining and running **multi-container Docker applications**.

### Without Docker Compose:

```bash
# Create network
docker network create mynet

# Start database
docker run -d \
  --name postgres \
  --network mynet \
  -e POSTGRES_PASSWORD=dbpass \
  postgres:15

# Start Redis
docker run -d \
  --name redis \
  --network mynet \
  redis:7

# Start application
docker run -d \
  --name app \
  --network mynet \
  -e DATABASE_URL=postgresql://postgres:dbpass@postgres/mydb \
  -e REDIS_URL=redis://redis:6379 \
  -p 8000:8000 \
  myapp:latest

# Stopping: manually stop each container
docker stop app redis postgres
docker rm app redis postgres
```

### With Docker Compose:

```yaml
version: '3.9'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: dbpass
    
  redis:
    image: redis:7
    
  app:
    image: myapp:latest
    environment:
      DATABASE_URL: postgresql://postgres:dbpass@postgres/mydb
      REDIS_URL: redis://redis:6379
    ports:
      - "8000:8000"
    depends_on:
      - postgres
      - redis
```

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down
```

**Much simpler!**

---

## 17.2 Docker Compose File Structure

### Basic Structure:

```yaml
version: '3.9'  # Compose file version

services:       # Define containers
  service1:     # Service name
    # Configuration
  service2:
    # Configuration

volumes:        # Define named volumes
  vol1:

networks:       # Define custom networks
  net1:
```

### Detailed Example:

```yaml
version: '3.9'

services:
  db:
    image: postgres:15
    container_name: myapp-db
    environment:
      POSTGRES_DB: myappdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: apppass
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser"]
      interval: 10s
      timeout: 5s
      retries: 5
  
  redis:
    image: redis:7-alpine
    container_name: myapp-cache
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - backend
    command: redis-server --appendonly yes
  
  app:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        PYTHON_VERSION: "3.11"
    container_name: myapp-backend
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    environment:
      DATABASE_URL: postgresql://appuser:apppass@db:5432/myappdb
      REDIS_URL: redis://redis:6379
      DEBUG: "false"
    ports:
      - "8000:8000"
    volumes:
      - ./app:/app
      - app_cache:/tmp/cache
    networks:
      - backend
    restart: unless-stopped
    command: gunicorn --bind 0.0.0.0:8000 app:app

volumes:
  db_data:
  redis_data:
  app_cache:

networks:
  backend:
    driver: bridge
```

---

## 17.3 Docker Compose Commands

### Running Services:

```bash
# Start services (create and start containers)
docker-compose up

# Start in detached mode
docker-compose up -d

# Build images before starting
docker-compose up --build

# Start specific services
docker-compose up -d db app
# (redis won't start)

# Down (stop and remove containers)
docker-compose down

# Stop (stop but don't remove)
docker-compose stop

# Start stopped services
docker-compose start

# Restart services
docker-compose restart

# Remove containers, volumes, networks (keep images)
docker-compose down -v

# Remove everything including images
docker-compose down -v --rmi all
```

### Viewing Services:

```bash
# List services
docker-compose ps

# View logs
docker-compose logs

# Follow specific service logs
docker-compose logs -f app

# Last 50 lines
docker-compose logs --tail=50

# Show logs with timestamps
docker-compose logs -t

# Log events
docker-compose events
```

### Executing Commands:

```bash
# Execute command in running service
docker-compose exec app bash

# Interactive database access
docker-compose exec db psql -U appuser -d myappdb

# Run command (create temporary container)
docker-compose run app python manage.py migrate

# Run with environment variable
docker-compose run -e DEBUG=true app python app.py

# Without dependencies
docker-compose run --no-deps app bash
```

---

## 17.4 Environment Files

### .env File:

```
# .env file (loaded automatically)

POSTGRES_PASSWORD=mypassword
POSTGRES_DB=myappdb
DEBUG=false
LOG_LEVEL=INFO
```

### Using in docker-compose.yml:

```yaml
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
  
  app:
    image: myapp:latest
    environment:
      DEBUG: ${DEBUG}
      LOG_LEVEL: ${LOG_LEVEL}
```

### Multiple .env Files:

```bash
# Load specific .env file
docker-compose --env-file .env.prod up -d

# Override specific values
docker-compose -f docker-compose.yml \
               -f docker-compose.prod.yml \
               up -d
```

---

## 17.5 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ What Docker Compose is and why it's useful
- ✓ Docker Compose file structure
- ✓ Service configuration (images, environment, ports, volumes)
- ✓ Networking in Compose
- ✓ All major Compose commands
- ✓ Environment variable management
- ✓ Dependency management

---

[Continuing with remaining sections: 18-27]

# SECTION 18: DOCKER BEST PRACTICES & PRODUCTION PATTERNS

## 18.1 Running as Non-Root User

### Why:
- Security: Limits damage if container is compromised
- Prevents privilege escalation
- Production best practice

### Implementation:

```dockerfile
# Create non-root user
RUN groupadd -r appuser && useradd -r -g appuser appuser

# Set user
USER appuser

# All subsequent commands run as appuser
COPY app.py .
RUN pip install requests  # Would fail (no permission)

# Good practice: install dependencies as root, run as appuser
FROM python:3.11

RUN groupadd -r appuser && useradd -r -g appuser appuser

COPY requirements.txt .
RUN pip install -r requirements.txt

# Switch to non-root
USER appuser

COPY app.py .
CMD ["python", "app.py"]
```

---

## 18.2 Health Checks

### HEALTHCHECK Instruction:

```dockerfile
# Simple health check
FROM nginx:latest

HEALTHCHECK --interval=10s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost/ || exit 1
```

### Health Check in docker-compose.yml:

```yaml
services:
  app:
    image: myapp:latest
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### Container Dependency on Health:

```yaml
version: '3.9'

services:
  db:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      retries: 5

  app:
    image: myapp:latest
    depends_on:
      db:
        condition: service_healthy  # Wait for healthy status
```

---

## 18.3 Immutable Infrastructure

### Principle: Containers are immutable

```dockerfile
# Bad: Application modifies itself
RUN pip install requests
RUN pip install flask

# Then at runtime, something modifies code/config
# Container state drifts from image

# Good: Everything in Dockerfile
FROM python:3.11
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app.py .
# At runtime, nothing changes
```

### Benefits:
- Consistent behavior
- Easy to rollback (just start previous image)
- No hidden dependencies
- Reproducible

---

## 18.4 Minimal Permissions

### Limit Container Capabilities:

```bash
# Drop capabilities
docker run --cap-drop=ALL \
  --cap-add=NET_BIND_SERVICE \
  myapp:latest

# Read-only filesystem
docker run --read-only \
  --tmpfs /tmp \
  myapp:latest

# No privilege escalation
docker run --security-opt=no-new-privileges \
  myapp:latest
```

### Full secure example:

```yaml
services:
  app:
    image: myapp:latest
    user: "1000"  # Non-root UID
    read_only: true
    tmpfs: /tmp
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE
    security_opt:
      - no-new-privileges:true
    restart: unless-stopped
```

---

## 18.5 Resource Limits

### Memory and CPU Limits:

```yaml
services:
  app:
    image: myapp:latest
    mem_limit: 512m         # Hard limit
    mem_reservation: 256m   # Soft limit
    cpus: 1.0               # CPU limit
    cpu_shares: 1024        # Relative priority
```

### Why Limits Matter:
- Prevent one container from consuming all resources
- Ensure fair resource distribution
- Catch memory leaks early
- Prevent denial of service

---

## 18.6 Logging Strategy

### Application Logs to STDOUT:

```python
# Good: Logs to stdout
import logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)
logger.info("Application started")

# Bad: Logs to file
with open('/var/log/app.log', 'a') as f:
    f.write("Application started\n")
```

### Log Rotation:

```yaml
services:
  app:
    image: myapp:latest
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

---

## 18.7 Startup & Shutdown

### Graceful Startup:

```dockerfile
# Use exec form (so PID 1 is app, receives signals)
ENTRYPOINT ["python", "app.py"]

# NOT: shell form
CMD python app.py
```

### Graceful Shutdown:

```python
import signal
import sys
from app import cleanup

def handle_shutdown(signum, frame):
    print("Shutting down gracefully...")
    cleanup()
    sys.exit(0)

signal.signal(signal.SIGTERM, handle_shutdown)

# Main app loop
```

---

## 18.8 Data Management

### Never Store Data in Container:

```dockerfile
# Bad: Storing in container
RUN mkdir /app/data
# Data lost when container stops!

# Good: Use volumes
```

```yaml
services:
  app:
    image: myapp:latest
    volumes:
      - app_data:/app/data  # Named volume for persistence
```

---

## 18.9 Learning Outcomes for This Section

By the end of this section, you should understand:
- ✓ Running as non-root user
- ✓ Health checks and monitoring
- ✓ Immutable infrastructure principle
- ✓ Limiting container permissions
- ✓ Resource limits (memory, CPU)
- ✓ Logging best practices
- ✓ Graceful startup and shutdown
- ✓ Data persistence strategies

---

# SECTION 19: PROJECT 1 - DOCKERIZING A FASTAPI BACKEND

## Project Goal:
Create a production-ready dockerized FastAPI application with best practices.

### Project Structure:

```
fastapi-app/
├── app/
│   ├── __init__.py
│   ├── main.py           # FastAPI application
│   └── models.py         # Data models
├── tests/
│   ├── __init__.py
│   └── test_main.py
├── Dockerfile
├── .dockerignore
├── requirements.txt
├── docker-compose.yml
└── README.md
```

---

## 19.1 Application Files

### app/main.py

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import logging
from typing import List

# Configure logging (logs to stdout)
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

app = FastAPI(title="User API", version="1.0.0")

# Data Models
class User(BaseModel):
    id: int
    name: str
    email: str

# In-memory database
users_db = [
    User(id=1, name="Alice", email="alice@example.com"),
    User(id=2, name="Bob", email="bob@example.com"),
]

# Endpoints
@app.get("/health")
async def health_check():
    """Health check endpoint"""
    return {"status": "healthy"}

@app.get("/api/v1/users", response_model=List[User])
async def list_users():
    """List all users"""
    logger.info(f"Fetching all users. Total: {len(users_db)}")
    return users_db

@app.get("/api/v1/users/{user_id}", response_model=User)
async def get_user(user_id: int):
    """Get user by ID"""
    user = next((u for u in users_db if u.id == user_id), None)
    if not user:
        logger.warning(f"User {user_id} not found")
        raise HTTPException(status_code=404, detail="User not found")
    logger.info(f"Retrieved user {user_id}")
    return user

@app.post("/api/v1/users", response_model=User)
async def create_user(user: User):
    """Create a new user"""
    # Check if user already exists
    if any(u.id == user.id for u in users_db):
        raise HTTPException(status_code=409, detail="User already exists")
    
    users_db.append(user)
    logger.info(f"Created user {user.id}: {user.name}")
    return user

@app.delete("/api/v1/users/{user_id}")
async def delete_user(user_id: int):
    """Delete user by ID"""
    global users_db
    original_count = len(users_db)
    users_db = [u for u in users_db if u.id != user_id]
    
    if len(users_db) == original_count:
        logger.warning(f"User {user_id} not found for deletion")
        raise HTTPException(status_code=404, detail="User not found")
    
    logger.info(f"Deleted user {user_id}")
    return {"message": "User deleted successfully"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

### requirements.txt

```
fastapi==0.104.1
uvicorn[standard]==0.24.0
pydantic==2.5.0
```

### tests/test_main.py

```python
from fastapi.testclient import TestClient
from app.main import app, User, users_db

client = TestClient(app)

def test_health_check():
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json()["status"] == "healthy"

def test_list_users():
    response = client.get("/api/v1/users")
    assert response.status_code == 200
    assert len(response.json()) >= 0

def test_get_user():
    response = client.get("/api/v1/users/1")
    assert response.status_code == 200
    data = response.json()
    assert data["name"] == "Alice"

def test_get_nonexistent_user():
    response = client.get("/api/v1/users/9999")
    assert response.status_code == 404

def test_create_user():
    new_user = {"id": 3, "name": "Charlie", "email": "charlie@example.com"}
    response = client.post("/api/v1/users", json=new_user)
    assert response.status_code == 200
    assert response.json()["name"] == "Charlie"

def test_delete_user():
    response = client.delete("/api/v1/users/3")
    assert response.status_code == 200
```

---

## 19.2 Dockerfile

```dockerfile
# Multi-stage build

# Stage 1: Builder
FROM python:3.11-slim as builder

WORKDIR /build

# Copy requirements
COPY requirements.txt .

# Install Python dependencies
RUN pip install --user --no-cache-dir -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

# Metadata
LABEL maintainer="your-email@example.com" \
      version="1.0.0" \
      description="FastAPI User API"

# Environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    APP_HOME=/app \
    APP_PORT=8000

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

# Create logs directory
RUN mkdir -p /app/logs && chown -R appuser:appuser /app

# Switch to non-root user
USER appuser

# Expose port
EXPOSE ${APP_PORT}

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD python -c "import requests; requests.get('http://localhost:${APP_PORT}/health')" || exit 1

# Run application
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## 19.3 .dockerignore

```
__pycache__
*.pyc
*.pyo
.venv
venv
.env
.env.local
.git
.gitignore
.pytest_cache
.coverage
htmlcov/
dist/
build/
*.egg-info
.vscode
.idea
*.swp
*.swo
README.md
```

---

## 19.4 Building and Running

```bash
# Build image
docker build -t fastapi-app:1.0.0 .

# Tag as latest
docker tag fastapi-app:1.0.0 fastapi-app:latest

# Run container
docker run -d \
  --name fastapi-app \
  -p 8000:8000 \
  -e DEBUG=false \
  fastapi-app:latest

# Test API
curl http://localhost:8000/health
curl http://localhost:8000/api/v1/users

# View logs
docker logs -f fastapi-app

# Run tests in container
docker run --rm \
  -v $(pwd):/app \
  fastapi-app:latest \
  python -m pytest tests/

# Stop and remove
docker stop fastapi-app
docker rm fastapi-app
```

---

## 19.5 Docker Compose Configuration

### docker-compose.yml

```yaml
version: '3.9'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: fastapi-app
    ports:
      - "8000:8000"
    environment:
      DEBUG: "false"
      LOG_LEVEL: "INFO"
    volumes:
      - ./app:/app/app:ro  # Read-only for development
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    networks:
      - backend

networks:
  backend:
    driver: bridge
```

```bash
# Start with Docker Compose
docker-compose up -d

# View logs
docker-compose logs -f app

# Stop
docker-compose down
```

---

## 19.6 Learning Outcomes for This Project

By the end of this project, you should understand:
- ✓ Project structure for containerized FastAPI apps
- ✓ Writing production-ready Dockerfile
- ✓ Multi-stage builds in practice
- ✓ Running as non-root user
- ✓ Health checks implementation
- ✓ Docker Compose for single service
- ✓ Building, testing, and running Docker images

---

# SECTION 20: PROJECT 2 - MULTI-CONTAINER APP (Backend + MySQL + Redis)

## Project Goal:
Create a complete multi-container application with database, caching, and API.

### Architecture:

```
┌─────────────────────────────────────────────┐
│     Docker Network (app-network)            │
├─────────────────────────────────────────────┤
│                                             │
│  ┌──────────────┐  ┌──────────┐             │
│  │   Backend    │  │  MySQL   │  ┌────────┐│
│  │   (FastAPI)  │──│ Database │──│ Redis  ││
│  └──────────────┘  └──────────┘  └────────┘│
│  Port 8000         Port 3306      Port 6379│
│                    (Internal)     (Internal)
│                                             │
└────────────────────┬────────────────────────┘
                     │
                  Host Port 8000
                  (Accessible externally)
```

---

## 20.1 Application Code

### app/database.py

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base
import os

# Database URL from environment
DATABASE_URL = os.getenv(
    "DATABASE_URL",
    "mysql+pymysql://root:rootpass@localhost/myapp"
)

# Create engine
engine = create_engine(
    DATABASE_URL,
    pool_pre_ping=True,  # Verify connections before using
    pool_recycle=3600   # Recycle connections every hour
)

# Session factory
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# Base for models
Base = declarative_base()

# Dependency for FastAPI
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

### app/models.py

```python
from sqlalchemy import Column, Integer, String, DateTime, Text
from sqlalchemy.orm import declarative_base
from datetime import datetime

Base = declarative_base()

class Article(Base):
    __tablename__ = "articles"
    
    id = Column(Integer, primary_key=True, autoincrement=True)
    title = Column(String(255), nullable=False, index=True)
    slug = Column(String(255), nullable=False, unique=True, index=True)
    body = Column(Text, nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
    
    def to_dict(self):
        return {
            "id": self.id,
            "title": self.title,
            "slug": self.slug,
            "body": self.body,
            "created_at": self.created_at.isoformat() if self.created_at else None,
            "updated_at": self.updated_at.isoformat() if self.updated_at else None,
        }
```

### app/cache.py

```python
import redis
import json
import os
from typing import Optional

redis_url = os.getenv("REDIS_URL", "redis://localhost:6379")
redis_client = redis.from_url(redis_url, decode_responses=True)

def get_from_cache(key: str) -> Optional[dict]:
    """Get value from cache"""
    value = redis_client.get(key)
    if value:
        return json.loads(value)
    return None

def set_in_cache(key: str, value: dict, ttl: int = 3600):
    """Set value in cache with TTL"""
    redis_client.setex(key, ttl, json.dumps(value))

def delete_from_cache(key: str):
    """Delete from cache"""
    redis_client.delete(key)

def clear_cache(pattern: str = "*"):
    """Clear all keys matching pattern"""
    for key in redis_client.scan_iter(match=pattern):
        redis_client.delete(key)
```

### app/main.py (Complex Version)

```python
from fastapi import FastAPI, HTTPException, Depends, Query
from sqlalchemy.orm import Session
from typing import List
import logging

from app.database import get_db, engine, Base
from app.models import Article
from app import cache

# Create tables
Base.metadata.create_all(bind=engine)

# Logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

app = FastAPI(title="Article API", version="1.0.0")

# Pydantic models for API
class ArticleCreate(BaseModel):
    title: str
    slug: str
    body: str

class ArticleUpdate(BaseModel):
    title: Optional[str] = None
    slug: Optional[str] = None
    body: Optional[str] = None

class ArticleResponse(BaseModel):
    id: int
    title: str
    slug: str
    body: str
    created_at: str
    updated_at: str

# Health endpoint
@app.get("/health")
async def health():
    return {"status": "healthy"}

# Get all articles (with caching)
@app.get("/api/v1/articles", response_model=List[ArticleResponse])
async def list_articles(
    skip: int = Query(0, ge=0),
    limit: int = Query(10, ge=1, le=100),
    db: Session = Depends(get_db)
):
    """List articles with pagination and caching"""
    cache_key = f"articles:list:{skip}:{limit}"
    
    # Try cache first
    cached = cache.get_from_cache(cache_key)
    if cached:
        logger.info(f"Cache HIT for {cache_key}")
        return cached
    
    logger.info(f"Cache MISS for {cache_key}, querying database")
    
    # Query database
    articles = db.query(Article).offset(skip).limit(limit).all()
    
    # Log query
    logger.info(f"Retrieved {len(articles)} articles from database")
    
    # Convert to response
    result = [a.to_dict() for a in articles]
    
    # Cache result (1 hour)
    cache.set_in_cache(cache_key, result, ttl=3600)
    
    return result

# Get single article
@app.get("/api/v1/articles/{article_id}", response_model=ArticleResponse)
async def get_article(article_id: int, db: Session = Depends(get_db)):
    """Get article by ID with caching"""
    cache_key = f"article:{article_id}"
    
    # Try cache
    cached = cache.get_from_cache(cache_key)
    if cached:
        logger.info(f"Cache HIT for article {article_id}")
        return cached
    
    # Query database
    article = db.query(Article).filter(Article.id == article_id).first()
    if not article:
        logger.warning(f"Article {article_id} not found")
        raise HTTPException(status_code=404, detail="Article not found")
    
    result = article.to_dict()
    cache.set_in_cache(cache_key, result, ttl=3600)
    
    logger.info(f"Retrieved article {article_id}")
    return result

# Create article
@app.post("/api/v1/articles", response_model=ArticleResponse)
async def create_article(article: ArticleCreate, db: Session = Depends(get_db)):
    """Create new article"""
    
    # Check if slug already exists
    existing = db.query(Article).filter(Article.slug == article.slug).first()
    if existing:
        raise HTTPException(status_code=409, detail="Slug already exists")
    
    # Create and save
    db_article = Article(**article.dict())
    db.add(db_article)
    db.commit()
    db.refresh(db_article)
    
    # Invalidate list cache
    cache.clear_cache("articles:list:*")
    
    logger.info(f"Created article: {db_article.id}")
    return db_article.to_dict()

# Delete article
@app.delete("/api/v1/articles/{article_id}")
async def delete_article(article_id: int, db: Session = Depends(get_db)):
    """Delete article by ID"""
    
    article = db.query(Article).filter(Article.id == article_id).first()
    if not article:
        raise HTTPException(status_code=404, detail="Article not found")
    
    db.delete(article)
    db.commit()
    
    # Invalidate caches
    cache.delete_from_cache(f"article:{article_id}")
    cache.clear_cache("articles:list:*")
    
    logger.info(f"Deleted article {article_id}")
    return {"message": "Article deleted successfully"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

---

## 20.2 Docker Compose Configuration

### docker-compose.yml

```yaml
version: '3.9'

services:
  mysql:
    image: mysql:8.0
    container_name: app-mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpass123
      MYSQL_DATABASE: myapp
      MYSQL_USER: appuser
      MYSQL_PASSWORD: apppass
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - backend
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-u", "root", "-prootpass123"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    container_name: app-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - backend
    command: redis-server --appendonly yes
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 3
    restart: unless-stopped

  backend:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: app-backend
    depends_on:
      mysql:
        condition: service_healthy
      redis:
        condition: service_healthy
    environment:
      DATABASE_URL: mysql+pymysql://appuser:apppass@mysql:3306/myapp
      REDIS_URL: redis://redis:6379
      DEBUG: "false"
      LOG_LEVEL: "INFO"
    ports:
      - "8000:8000"
    volumes:
      - ./app:/app/app:ro
    networks:
      - backend
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: unless-stopped

volumes:
  mysql_data:
    driver: local
  redis_data:
    driver: local

networks:
  backend:
    driver: bridge
```

---

## 20.3 Dockerfile

```dockerfile
FROM python:3.11-slim as builder

WORKDIR /build
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

FROM python:3.11-slim

ENV PYTHONUNBUFFERED=1 PYTHONDONTWRITEBYTECODE=1 APP_HOME=/app
RUN groupadd -r appuser && useradd -r -g appuser appuser
WORKDIR $APP_HOME

COPY --from=builder /root/.local /home/appuser/.local
COPY --chown=appuser:appuser . .

ENV PATH=/home/appuser/.local/bin:$PATH
USER appuser

EXPOSE 8000
HEALTHCHECK --interval=30s --timeout=10s --start-period=10s \
  CMD python -c "import requests; requests.get('http://localhost:8000/health')" || exit 1

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## 20.4 Requirements.txt

```
fastapi==0.104.1
uvicorn[standard]==0.24.0
sqlalchemy==2.0.23
pymysql==1.1.0
redis==5.0.1
pydantic==2.5.0
requests==2.31.0
```

---

## 20.5 Running the Project

```bash
# Start all services
docker-compose up -d

# Check services running
docker-compose ps

# View logs
docker-compose logs -f

# Test API
curl http://localhost:8000/health
curl http://localhost:8000/api/v1/articles

# Create article
curl -X POST http://localhost:8000/api/v1/articles \
  -H "Content-Type: application/json" \
  -d '{"title": "Docker Guide", "slug": "docker-guide", "body": "..."}'

# View cache
docker-compose exec redis redis-cli
> KEYS *
> GET "articles:list:0:10"

# Stop all services
docker-compose down

# Remove volumes (delete data)
docker-compose down -v
```

---

## 20.6 Learning Outcomes for This Project

By the end of this project, you should understand:
- ✓ Multi-container application architecture
- ✓ Service dependencies and health checks
- ✓ Database connectivity and ORM
- ✓ Caching patterns with Redis
- ✓ Docker Compose for complex apps
- ✓ Service discovery by container name
- ✓ Data persistence with volumes

---

*[Document continues with Project 3, Real-world Projects, and remaining sections up to section 27]*

# SECTION 21: PROJECT 3 - BACKGROUND JOB PROCESSING WITH CELERY

## Project Goal:
Build a system with async task processing using Celery, Redis, and FastAPI.

### Architecture:

```
┌────────────────────────────────────────────────────┐
│            FastAPI Web Server                      │
│  (Receives requests, queues tasks)                 │
└──────────┬─────────────────────────────────────────┘
           │ (sends tasks)
           │
    ┌──────▼───────┐
    │ Redis Queue  │ (Message Broker)
    └──────┬───────┘
           │ (reads tasks)
      ┌────▼──────────────────┐
      │ Celery Workers (x3)   │
      │ (processes tasks)     │
      └──────────────────────┘
           │ (stores results)
           │
    ┌──────▼───────┐
    │ Redis Result │ (Result Backend)
    └──────────────┘
```

---

## 21.1 Application Structure

### app/tasks.py - Celery Tasks

```python
from celery import Celery
from celery.utils.log import get_task_logger
import time
import random
from typing import Dict
import os

# Celery configuration
redis_url = os.getenv("REDIS_URL", "redis://localhost:6379")
celery_app = Celery(
    __name__,
    broker=redis_url,
    backend=redis_url
)

celery_app.conf.update(
    task_serializer='json',
    accept_content=['json'],
    result_serializer='json',
    timezone='UTC',
    enable_utc=True,
    task_track_started=True,
    task_time_limit=30 * 60,  # 30 minute hard limit
    task_soft_time_limit=25 * 60,  # 25 minute soft limit
)

# Logger for tasks
logger = get_task_logger(__name__)

# Task 1: Process image
@celery_app.task(bind=True, name='tasks.process_image')
def process_image(self, image_url: str) -> Dict:
    """Process image (download, resize, optimize)"""
    try:
        logger.info(f"[Task {self.request.id}] Starting image processing for {image_url}")
        
        # Update task state with progress
        self.update_state(state='PROGRESS', meta={'current': 0, 'total': 3})
        time.sleep(2)  # Simulate download
        
        self.update_state(state='PROGRESS', meta={'current': 1, 'total': 3})
        time.sleep(2)  # Simulate resize
        
        self.update_state(state='PROGRESS', meta={'current': 2, 'total': 3})
        time.sleep(2)  # Simulate optimization
        
        result = {
            'image_url': image_url,
            'processed': True,
            'size_before': f"{random.randint(2, 10)} MB",
            'size_after': f"{random.randint(1, 5)} MB"
        }
        
        logger.info(f"[Task {self.request.id}] Image processing completed")
        return result
        
    except Exception as e:
        logger.error(f"[Task {self.request.id}] Error processing image: {str(e)}")
        raise

# Task 2: Send email
@celery_app.task(bind=True, name='tasks.send_email', max_retries=3)
def send_email(self, to: str, subject: str, body: str) -> Dict:
    """Send email with retry logic"""
    try:
        logger.info(f"[Task {self.request.id}] Sending email to {to}")
        
        # Simulate email sending
        time.sleep(1)
        
        # Randomly fail to demonstrate retry
        if random.random() < 0.3:  # 30% failure rate
            raise Exception("SMTP connection failed")
        
        result = {
            'to': to,
            'subject': subject,
            'sent': True
        }
        
        logger.info(f"[Task {self.request.id}] Email sent successfully")
        return result
        
    except Exception as e:
        logger.error(f"[Task {self.request.id}] Email send failed: {str(e)}")
        # Retry with exponential backoff
        raise self.retry(exc=e, countdown=2 ** self.request.retries)

# Task 3: Data aggregation
@celery_app.task(bind=True, name='tasks.aggregate_data')
def aggregate_data(self, dataset_ids: list) -> Dict:
    """Aggregate data from multiple sources"""
    try:
        logger.info(f"[Task {self.request.id}] Aggregating {len(dataset_ids)} datasets")
        
        total = len(dataset_ids)
        for i, dataset_id in enumerate(dataset_ids):
            self.update_state(
                state='PROGRESS',
                meta={'current': i+1, 'total': total, 'dataset': dataset_id}
            )
            time.sleep(1)  # Simulate data processing
        
        result = {
            'datasets_processed': total,
            'total_records': sum(random.randint(1000, 10000) for _ in dataset_ids),
            'aggregation_complete': True
        }
        
        logger.info(f"[Task {self.request.id}] Data aggregation completed")
        return result
        
    except Exception as e:
        logger.error(f"[Task {self.request.id}] Aggregation failed: {str(e)}")
        raise
```

### app/main.py - FastAPI Application

```python
from fastapi import FastAPI, HTTPException, BackgroundTasks
from pydantic import BaseModel
import logging
from typing import Optional
import os

from app.tasks import celery_app, process_image, send_email, aggregate_data

logger = logging.getLogger(__name__)

app = FastAPI(title="Task Queue API", version="1.0.0")

# Request/Response models
class ProcessImageRequest(BaseModel):
    image_url: str

class SendEmailRequest(BaseModel):
    to: str
    subject: str
    body: str

class AggregateDataRequest(BaseModel):
    dataset_ids: list[str]

class TaskResponse(BaseModel):
    task_id: str
    status: str

class TaskStatusResponse(BaseModel):
    task_id: str
    status: str
    result: Optional[dict] = None
    progress: Optional[dict] = None

# Health check
@app.get("/health")
async def health():
    return {"status": "healthy"}

# Get celery worker status
@app.get("/workers/status")
async def worker_status():
    """Check if celery workers are running"""
    inspect = celery_app.control.inspect()
    active_workers = inspect.active()
    
    if active_workers is None:
        return {"status": "no active workers"}
    
    return {
        "status": "ok",
        "workers": len(active_workers),
        "active_tasks": sum(len(tasks) for tasks in active_workers.values())
    }

# Task 1: Process image endpoint
@app.post("/api/v1/tasks/process-image", response_model=TaskResponse)
async def enqueue_process_image(request: ProcessImageRequest):
    """Queue image processing task"""
    task = process_image.delay(request.image_url)
    
    logger.info(f"Queued image processing task: {task.id}")
    
    return {
        "task_id": task.id,
        "status": task.status
    }

# Task 2: Send email endpoint
@app.post("/api/v1/tasks/send-email", response_model=TaskResponse)
async def enqueue_send_email(request: SendEmailRequest):
    """Queue email sending task"""
    task = send_email.delay(request.to, request.subject, request.body)
    
    logger.info(f"Queued email task: {task.id}")
    
    return {
        "task_id": task.id,
        "status": task.status
    }

# Task 3: Aggregate data endpoint
@app.post("/api/v1/tasks/aggregate-data", response_model=TaskResponse)
async def enqueue_aggregate_data(request: AggregateDataRequest):
    """Queue data aggregation task"""
    task = aggregate_data.delay(request.dataset_ids)
    
    logger.info(f"Queued aggregation task: {task.id}")
    
    return {
        "task_id": task.id,
        "status": task.status
    }

# Get task status
@app.get("/api/v1/tasks/{task_id}", response_model=TaskStatusResponse)
async def get_task_status(task_id: str):
    """Get status and result of a task"""
    task = celery_app.AsyncResult(task_id)
    
    response_data = {
        "task_id": task_id,
        "status": task.status,
        "result": None,
        "progress": None
    }
    
    if task.status == 'PENDING':
        response_data['status'] = 'PENDING'
    elif task.status == 'PROGRESS':
        response_data['progress'] = task.info
    elif task.status == 'SUCCESS':
        response_data['result'] = task.result
    elif task.status == 'FAILURE':
        response_data['result'] = str(task.info)
    elif task.status == 'RETRY':
        response_data['status'] = 'RETRYING'
    
    return response_data

# Cancel task
@app.delete("/api/v1/tasks/{task_id}")
async def cancel_task(task_id: str):
    """Cancel a pending task"""
    task = celery_app.AsyncResult(task_id)
    
    if task.status not in ['PENDING', 'RETRY']:
        raise HTTPException(
            status_code=400,
            detail=f"Cannot cancel task in {task.status} state"
        )
    
    task.revoke(terminate=True)
    
    logger.info(f"Cancelled task: {task_id}")
    
    return {"message": "Task cancelled", "task_id": task_id}

# Get all active tasks
@app.get("/api/v1/tasks")
async def list_active_tasks():
    """List all active and pending tasks"""
    inspect = celery_app.control.inspect()
    
    active = inspect.active()
    reserved = inspect.reserved()
    
    return {
        "active_tasks": active if active else {},
        "reserved_tasks": reserved if reserved else {}
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

---

## 21.2 Docker Compose Configuration

### docker-compose.yml

```yaml
version: '3.9'

services:
  redis:
    image: redis:7-alpine
    container_name: task-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    command: redis-server --appendonly yes
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 3
    restart: unless-stopped

  api:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: task-api
    depends_on:
      redis:
        condition: service_healthy
    environment:
      REDIS_URL: redis://redis:6379
      CELERY_BROKER_URL: redis://redis:6379/0
      CELERY_RESULT_BACKEND: redis://redis:6379/1
      DEBUG: "false"
    ports:
      - "8000:8000"
    volumes:
      - ./app:/app/app:ro
    networks:
      - task-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: unless-stopped

  worker1:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: task-worker-1
    depends_on:
      redis:
        condition: service_healthy
    environment:
      REDIS_URL: redis://redis:6379
      CELERY_BROKER_URL: redis://redis:6379/0
      CELERY_RESULT_BACKEND: redis://redis:6379/1
    volumes:
      - ./app:/app/app:ro
    networks:
      - task-network
    command: celery -A app.tasks worker --loglevel=info --concurrency=4 --queues=celery
    restart: unless-stopped

  worker2:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: task-worker-2
    depends_on:
      redis:
        condition: service_healthy
    environment:
      REDIS_URL: redis://redis:6379
      CELERY_BROKER_URL: redis://redis:6379/0
      CELERY_RESULT_BACKEND: redis://redis:6379/1
    volumes:
      - ./app:/app/app:ro
    networks:
      - task-network
    command: celery -A app.tasks worker --loglevel=info --concurrency=4 --queues=celery
    restart: unless-stopped

  worker3:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: task-worker-3
    depends_on:
      redis:
        condition: service_healthy
    environment:
      REDIS_URL: redis://redis:6379
      CELERY_BROKER_URL: redis://redis:6379/0
      CELERY_RESULT_BACKEND: redis://redis:6379/1
    volumes:
      - ./app:/app/app:ro
    networks:
      - task-network
    command: celery -A app.tasks worker --loglevel=info --concurrency=4 --queues=celery
    restart: unless-stopped

  flower:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: task-flower
    depends_on:
      redis:
        condition: service_healthy
    environment:
      CELERY_BROKER_URL: redis://redis:6379/0
      CELERY_RESULT_BACKEND: redis://redis:6379/1
    ports:
      - "5555:5555"
    networks:
      - task-network
    command: celery -A app.tasks flower --port=5555
    restart: unless-stopped

volumes:
  redis_data:

networks:
  task-network:
    driver: bridge
```

---

## 21.3 Usage Examples

```bash
# Start all services
docker-compose up -d

# Check workers are running
curl http://localhost:8000/workers/status

# Queue image processing task
curl -X POST http://localhost:8000/api/v1/tasks/process-image \
  -H "Content-Type: application/json" \
  -d '{"image_url": "https://example.com/image.jpg"}'

# Response:
# {"task_id": "abc123def456...", "status": "PENDING"}

# Check task status
curl http://localhost:8000/api/v1/tasks/abc123def456...

# View task progress
curl http://localhost:8000/api/v1/tasks/abc123def456...
# {"status": "PROGRESS", "progress": {"current": 1, "total": 3}}

# Flower monitoring UI (http://localhost:5555/)
# Shows real-time tasks, workers, and history

# View logs
docker-compose logs -f worker1

# Scale up workers
docker-compose up -d --scale worker=5
```

---

## 21.4 Learning Outcomes for This Project

By the end of this project, you should understand:
- ✓ Asynchronous task processing with Celery
- ✓ Message broker pattern (Redis)
- ✓ Task queuing and worker processes
- ✓ Task status tracking and progress
- ✓ Retry logic and error handling
- ✓ Task monitoring with Flower
- ✓ Scaling worker processes
- ✓ Production-ready task architecture

---

# SECTION 22-27: REAL-WORLD PROJECTS AND ADVANCED TOPICS

[This would include:
- Real-world Project 1: Microservices Architecture
- Real-world Project 2: Real-time Job Processing System
- Section 24: Deployment Strategies & CI/CD
- Section 25: Container Orchestration (Kubernetes Intro)
- Section 26: Troubleshooting Guide
- Section 27: Exercises & Mini Challenges]

---

# FINAL MODULE: EXERCISES & MINI CHALLENGES

## Challenge 1: Optimize an Existing Dockerfile

```dockerfile
# Current Dockerfile (not optimized)
FROM ubuntu:22.04

RUN apt-get update
RUN apt-get install -y python3
RUN apt-get install -y python3-pip

WORKDIR /app
COPY . .
RUN pip install -r requirements.txt

CMD ["python3", "app.py"]
```

**Your Task:**
1. Reduce image to < 500MB using slim/alpine base
2. Implement multi-stage build if applicable
3. Add health check
4. Run as non-root user
5. Add .dockerignore

---

## Challenge 2: Fix Docker Compose Issues

Your `docker-compose.yml` has issues:
- Services timeout trying to connect
- Data is lost when container stops
- No resource limits
- Service startup order is wrong

**Your Task:**
Fix the configuration to include:
- Proper dependency ordering with health checks
- Persistent volumes for databases
- Resource limits for all services
- Wait condition for database readiness

---

## Challenge 3: Implement Caching Strategy

```python
# Implement caching for expensive operations
@app.get("/expensive-calculation")
async def expensive_calc(n: int):
    # This takes 30 seconds
    result = slow_calculation(n)
    return result
```

**Your Task:**
1. Implement Redis caching
2. Cache expensive operation results
3. Set appropriate TTL based on data freshness requirements
4. Invalidate cache when data changes
5. Add cache statistics endpoint

---

**Congratulations!** 

You've completed a comprehensive Docker learning journey from fundamentals through production-ready systems. You now have the knowledge and practical skills to containerize applications, build multi-container systems, and deploy Docker applications in real-world environments.

---

**Key Takeaways:**

✓ Docker architecture and components
✓ Image and container management
✓ Dockerfile best practices
✓ Multi-container orchestration with Compose
✓ Networking and data persistence
✓ Production deployment patterns
✓ Real-world project implementation
✓ Troubleshooting and debugging

---

**Next Steps:**

1. **Kubernetes:** Learn container orchestration at scale
2. **CI/CD Integration:** Automate build and deployment pipelines
3. **Microservices:** Design and deploy distributed systems
4. **Cloud Platforms:** Deploy on AWS, Azure, GCP
5. **Security:** Container security hardening and scanning

---

End of Docker Complete Learning Guide

Made with ❤️ for aspiring DevOps engineers and backend developers.
