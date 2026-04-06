# DOCKER QUICK REFERENCE GUIDE

## Essential Commands at a Glance

### Image Management
```bash
docker build -t myapp:1.0 .              # Build image
docker pull ubuntu:22.04                  # Download image
docker images                             # List images
docker rmi myapp:1.0                      # Remove image
docker tag myapp:1.0 myapp:latest         # Tag image
docker push myapp:1.0                     # Push to registry
```

### Container Management
```bash
docker run -d myapp:latest                # Run container
docker ps                                 # List running
docker ps -a                              # List all
docker start mycontainer                  # Start stopped
docker stop mycontainer                   # Stop running
docker rm mycontainer                     # Remove container
docker logs mycontainer                   # View logs
docker exec -it myapp bash                # Execute command
```

### Common Combinations
```bash
# Run with port mapping
docker run -d -p 8000:8000 myapp:latest

# Run with environment variables
docker run -d -e DEBUG=true myapp:latest

# Run with volume
docker run -d -v ~/data:/app/data myapp:latest

# Run interactively
docker run -it ubuntu:22.04 /bin/bash

# View real-time logs
docker logs -f myapp

# Remove dangling resources
docker system prune

# Remove everything
docker system prune -a --volumes
```

---

## Docker Compose Quick Reference

### Basic Commands
```bash
docker-compose up -d                     # Start services
docker-compose down                      # Stop services
docker-compose ps                        # List services
docker-compose logs -f                   # Follow logs
docker-compose exec app bash             # Execute in service
```

### Typical docker-compose.yml Structure
```yaml
version: '3.9'

services:
  app:
    image: myapp:latest
    ports:
      - "8000:8000"
    environment:
      DEBUG: "true"
    volumes:
      - ./app:/app
    depends_on:
      - db
  
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: pass
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:

networks:
  default:
    driver: bridge
```

---

## Dockerfile Quick Reference

### Basic Template
```dockerfile
# Start from base image
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy files
COPY requirements.txt .

# Install dependencies
RUN pip install -r requirements.txt

# Copy application
COPY . .

# Expose port
EXPOSE 8000

# Run application
CMD ["python", "app.py"]
```

### Best Practices Checklist
- [ ] Use specific version tags for base images
- [ ] Combine RUN commands with &&
- [ ] Order commands by change frequency (dependencies first)
- [ ] Use .dockerignore to exclude files
- [ ] Run as non-root user
- [ ] Add health checks
- [ ] Use multi-stage builds
- [ ] Minimize image size

---

## Environment Setup Checklist

### Windows/Mac Setup
- [ ] Install Docker Desktop
- [ ] Enable virtualization in BIOS (Windows)
- [ ] Allocate sufficient resources (4GB RAM minimum)
- [ ] Verify: `docker --version` and `docker run hello-world`

### Linux Setup
```bash
sudo apt-get update
sudo apt-get install docker.io docker-compose
sudo usermod -aG docker $USER
```

---

## Debugging Quick Tips

### Container Won't Start
```bash
docker logs mycontainer              # Check error message
docker inspect mycontainer           # Check configuration
docker run -it image /bin/bash       # Debug interactively
```

### Port Issues
```bash
docker port mycontainer              # Check mappings
netstat -tlnp | grep 8000           # Check host port
docker run -p 8000:8000 image        # Ensure port mapped
```

### Slow Build
```bash
docker build --no-cache -t app .     # Rebuild all layers
docker images                        # Check for large images
du -sh /var/lib/docker               # Check disk usage
```

### Out of Memory
```bash
docker stats                         # Check memory usage
docker run -m 512m image             # Set memory limit
```

---

## Production Checklist

- [ ] Image is non-root user
- [ ] Memory and CPU limits set
- [ ] Health checks implemented
- [ ] Logging to stdout/stderr
- [ ] All secrets from environment/files (not hardcoded)
- [ ] No 'latest' tag in production
- [ ] Image is optimized (slim/alpine)
- [ ] Restart policy configured
- [ ] Data persisted with volumes
- [ ] Graceful shutdown handled
- [ ] Monitoring/logging set up
- [ ] Security scanning done

---

## Common Docker Errors & Solutions

### "Cannot connect to Docker daemon"
- Docker Desktop not running (Windows/Mac)
- Docker service not started (Linux: `sudo systemctl start docker`)

### "Port already in use"
- Another container using same port
- Solution: Use different port or stop conflicting container

### "Permission denied"
- Running without sudo (Linux)
- Solution: `sudo usermod -aG docker $USER` then log out/in

### "Image not found"
- Image not built yet
- Solution: `docker build -t image_name .`

### "Out of memory"
- Container exceeded memory limit
- Solution: Increase limit or optimize app

---

## Learning Path Summary

### Week 1: Fundamentals
- Understand containerization concept
- Learn Docker architecture
- Master basic commands (run, ps, logs, exec)
- Run pre-built images from Docker Hub

### Week 2: Images & Dockerfiles
- Write Dockerfiles from scratch
- Build custom images
- Understand layers and caching
- Optimize image size

### Week 3: Multi-Container Apps
- Learn Docker Compose
- Run multiple interconnected services
- Implement service discovery
- Use volumes for persistence

### Week 4: Production & Advanced
- Implement best practices
- Deploy to production
- Setup CI/CD integration
- Learn container orchestration (Kubernetes intro)

---

## Additional Resources

### Official Documentation
- Docker Docs: https://docs.docker.com/
- Docker Compose: https://docs.docker.com/compose/
- Best Practices: https://docs.docker.com/develop/dev-best-practices/

### Learning Platforms
- Docker Labs: https://labs.play-with-docker.com/
- Docker Hub: https://hub.docker.com/

### Tools & Extensions
- Docker Desktop Dashboard
- VS Code Docker Extension
- Portainer (Web UI for Docker)
- Lazydocker (Terminal UI)

---

## File Structure Template for Docker Projects

```
my-project/
├── app/
│   ├── __init__.py
│   ├── main.py
│   └── models.py
├── tests/
│   ├── __init__.py
│   └── test_main.py
├── .dockerignore
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── .env.example
├── README.md
└── docs/
    ├── DEVELOPMENT.md
    └── DEPLOYMENT.md
```

---

## Performance Optimization Tips

1. **Reduce Image Size**
   - Use slim/alpine base images
   - Remove build dependencies after compilation
   - Use multi-stage builds

2. **Layer Caching**
   - Order Dockerfile by change frequency
   - Put RUN commands before COPY
   - Group related RUN commands

3. **Build Performance**
   - Use .dockerignore to exclude files
   - Parallelize builds with BuildKit
   - Cache external downloads

4. **Runtime Performance**
   - Set resource limits appropriately
   - Use health checks
   - Monitor with docker stats

---

---

# COMPREHENSIVE DOCKER LEARNING GUIDE - COMPLETE PACKAGE

## What You've Learned

This complete guide covers Docker from absolute beginner to production-ready professional:

### Core Concepts (Sections 1-3)
- Containerization fundamentals
- Containers vs Virtual Machines
- Docker architecture
- Images and containers explained

### Practical Setup (Sections 4-8)
- Installation on all platforms
- CLI mastery
- Dockerfile best practices
- Building and running images
- Container management

### Advanced Features (Sections 9-14)
- Data persistence with volumes
- Container networking
- Environment configuration
- Port mapping
- Logging and debugging
- Container lifecycle

### Production-Ready Skills (Sections 15-18)
- Image optimization
- Multi-stage builds
- Docker Registry
- Docker Compose
- Best practices
- Security hardening

### Real-World Projects (Sections 19-21)
- Dockerizing FastAPI applications
- Multi-container apps (MySQL + Redis)
- Background job processing (Celery)

### Advanced Topics (Sections 22-27)
- Microservices architecture
- CI/CD integration
- Kubernetes introduction
- Troubleshooting guide
- Practical exercises

---

## How to Use This Guide

### For Self-Paced Learning:
1. Read sections sequentially
2. Practice each command example immediately
3. Work through the projects
4. Complete exercises and challenges

### For Institute/Classroom:
1. Use as curriculum for Docker course
2. Projects can be individual or group assignments
3. Exercises serve as graded assessments
4. Reference guide for quick lookup

### For Reference:
1. Use table of contents to find topics
2. Bookmark quick reference guide
3. Share with team members
4. Update with team-specific patterns

---

## Estimated Learning Time

- **Complete Beginner:** 4-6 weeks (daily practice)
- **Skipping Basics:** 2-3 weeks
- **Projects Only:** 1-2 weeks
- **Quick Reference:** Ongoing

---

## Success Criteria

By the end of this guide, you should be able to:

✓ Explain containerization and Docker's role in DevOps
✓ Write production-ready Dockerfiles
✓ Build, run, and manage containers
✓ Orchestrate multi-container applications
✓ Implement best practices for security and performance
✓ Deploy applications to production
✓ Debug and troubleshoot container issues
✓ Understand advanced topics (orchestration, CI/CD)

---

## Next Steps After Mastering Docker

1. **Container Orchestration:** Learn Kubernetes for managing large-scale containerized applications
2. **CI/CD Pipelines:** Integrate Docker with Jenkins, GitLab CI, GitHub Actions
3. **Cloud Platforms:** Deploy on AWS ECS, Azure Container Instances, Google Cloud Run
4. **Microservices:** Build and scale microservices architectures
5. **Security:** Container security, vulnerability scanning, runtime protection

---

## Tips for Success

1. **Practice Regularly**
   - Don't just read, build hands-on
   - Experiment with variations
   - Break things intentionally to learn

2. **Follow Along**
   - Run every command in the guide
   - Modify examples for your use case
   - Build your own projects

3. **Deep Dive**
   - Understand the "why" not just "how"
   - Read official Docker documentation
   - Join Docker community

4. **Stay Updated**
   - Docker evolves constantly
   - Follow Docker blog and releases
   - Update your practice regularly

---

## About This Guide

This comprehensive guide is designed for:
- Backend developers entering DevOps
- DevOps engineers new to containerization
- Development teams adopting Docker
- IT professionals managing containers
- Students learning cloud-native development

The guide combines:
- Theoretical understanding
- Practical command examples
- Real-world project implementations
- Production best practices
- Industry patterns and standards

---

## Document Organization

### Part 1: Fundamentals (Sections 1-8)
- File: Docker_Complete_Learning_Guide.md
- Content: Basics through CLI and Dockerfile

### Part 2: Advanced & Projects (Sections 9-27)
- File: Docker_Complete_Learning_Guide_Part2.md
- Content: Advanced features, Compose, and projects

### Quick Reference
- File: DOCKER_QUICK_REFERENCE_GUIDE.md
- Content: Quick lookup for commands and patterns

---

## Import to Your System

### Converting to Word Document
1. Copy content from markdown files
2. Paste into Microsoft Word
3. Use "Markdown to Word" converter tools
4. Or use Pandoc:
   ```bash
   pandoc Docker_Complete_Learning_Guide.md -o Docker_Guide.docx
   ```

### Using as Notes
- PDF conversion: Print from browser to PDF
- Online: Upload to Google Drive for cloud access
- Collaboration: Share with team for comments

---

## Feedback & Improvements

This guide is comprehensive but not exhaustive. For your specific needs:

1. **Customize Examples:** Replace generic names with your stack
2. **Add Team Patterns:** Document your team's Docker standards
3. **Include Runbooks:** Add troubleshooting for your infrastructure
4. **Version Control:** Track guide updates with git

---

## Key Concepts Summary

### Images
- Immutable templates
- Built from Dockerfile instructions
- Layers with caching
- Versioned and tagged

### Containers
- Running instances of images
- Isolated processes
- Short-lived by default
- Data-agnostic

### Compose
- Multi-container orchestration
- Service definitions
- Network management
- Volume coordination

### Best Practices
- Non-root users
- Health checks
- Resource limits
- Data persistence
- Graceful shutdown

---

## Common Pitfalls to Avoid

1. **Running as Root**
   - Security risk
   - Solution: Create non-root user in Dockerfile

2. **Large Images**
   - Slow deployment
   - Solution: Use slim base images, multi-stage builds

3. **Data Loss**
   - Stopping container loses data
   - Solution: Use named volumes

4. **Hard-coded Secrets**
   - Exposed in images and logs
   - Solution: Use environment files, secret management

5. **Poor Logging**
   - Can't debug container behavior
   - Solution: Log to stdout, structured logging

6. **No Health Checks**
   - Can't detect failed containers
   - Solution: Implement HEALTHCHECK

7. **Latest Tag in Production**
   - Unpredictable deployments
   - Solution: Use specific versions

---

## Resources Provided

### Guides
- Complete learning journey (27 sections)
- Quick reference cards
- Troubleshooting guide
- Best practices checklist

### Examples
- 3 complete projects (code provided)
- 10+ Docker Compose examples
- 20+ Dockerfile templates
- Command examples for every feature

### Exercises
- Mini challenges
- Real-world scenarios
- Debugging exercises
- Optimization tasks

---

## Your Learning Completion Checklist

- [ ] Completed Sections 1-8 (Fundamentals)
- [ ] Completed Sections 9-14 (Features)
- [ ] Completed Sections 15-18 (Production)
- [ ] Completed Project 1 (FastAPI)
- [ ] Completed Project 2 (Multi-container)
- [ ] Completed Project 3 (Celery/Background jobs)
- [ ] Reviewed Quick Reference Guide
- [ ] Practiced all commands hands-on
- [ ] Completed exercises and challenges
- [ ] Built own Docker project

---

## Final Thoughts

Docker is now an essential skill for anyone in software development and DevOps. This guide provides everything needed to master it.

Remember:
- **Start simple:** Run pre-built images first
- **Build gradually:** Write simple Dockerfiles before complex ones
- **Practice consistently:** Regular hands-on work is key
- **Learn deeply:** Understand the concepts, not just commands
- **Stay updated:** Docker evolves; keep learning

---

**Congratulations on completing this comprehensive Docker learning guide!**

You now have the knowledge and skills to containerize applications, build production-ready systems, and work professionally with Docker.

---

Made with ❤️ for aspiring DevOps engineers and backend developers.
Last Updated: 2024
Version: 1.0

---
