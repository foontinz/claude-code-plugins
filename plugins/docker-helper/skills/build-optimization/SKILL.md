---
name: docker-build-optimization
description: |
  This skill generates optimized Dockerfiles and build configurations by analyzing project structure, dependencies, and requirements. It creates production-ready, multi-stage Dockerfiles following security best practices, implements efficient layer caching, and provides build command optimization. Use this when asked to create a Dockerfile, optimize Docker builds, or when the user uses the `/docker-build` or `/db` command. It is especially useful for containerizing applications and implementing CI/CD build pipelines.
---

## Overview

This skill empowers Claude to create professional, optimized Docker configurations automatically. By analyzing project structure and dependencies, it generates Dockerfiles that follow security, performance, and maintainability best practices.

## How It Works

1. **Project Analysis**: The skill examines the project structure, package files, and application type.
2. **Dockerfile Generation**: Based on the analysis, it constructs optimized, multi-stage Dockerfiles.
3. **Build Optimization**: Provides build commands and .dockerignore configurations for efficient builds.

## When to Use This Skill

This skill activates when you need to:
- Create a Dockerfile for an application
- Optimize existing Docker build processes
- Generate multi-stage build configurations
- Implement security best practices for containers
- Use the `/docker-build` or `/db` command
- Set up containerized development environments

## Examples

### Example 1: Node.js Application Containerization

User request: "Create a Dockerfile for my Node.js Express app"

The skill will:
1. Analyze package.json and project structure
2. Generate a multi-stage Dockerfile with security optimizations
3. Create .dockerignore for efficient builds
4. Provide development and production build commands

### Example 2: Production Build Optimization

User request: "/db optimize my production Docker build"

The skill will:
1. Analyze current Dockerfile and build process
2. Implement multi-stage build to reduce image size
3. Add security best practices (non-root user, minimal base image)
4. Configure health checks and proper signal handling

## Best Practices

- **Multi-stage Builds**: Separate build and runtime environments to minimize image size
- **Security**: Use non-root users, minimal base images, and proper file permissions
- **Layer Caching**: Optimize Dockerfile layer order for faster builds
- **Health Checks**: Implement proper health monitoring for containers
- **Environment Configuration**: Separate development and production configurations

## Integration

This skill integrates with the Docker ecosystem by providing complete containerization solutions. It complements other Docker-related skills by focusing specifically on build optimization and image security.