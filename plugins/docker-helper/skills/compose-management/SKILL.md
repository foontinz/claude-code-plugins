---
name: docker-compose-management
description: |
  This skill generates and manages docker-compose.yml configurations for multi-container applications. It analyzes application stacks, service dependencies, and deployment requirements to create production-ready orchestration setups. Includes proper networking, volume management, health checks, and environment variable configuration. Use this when asked to create docker-compose files, set up development environments, or when the user uses the `/docker-compose` or `/dc` command. It is especially useful for microservices architectures and complex application stacks.
---

## Overview

This skill empowers Claude to create comprehensive Docker Compose configurations that handle complex multi-container applications. By analyzing service dependencies and requirements, it generates orchestration setups that ensure proper service startup order, networking, and data persistence.

## How It Works

1. **Stack Analysis**: The skill examines the application requirements, services needed, and dependencies.
2. **Service Configuration**: Generates proper service definitions with health checks and dependencies.
3. **Orchestration Setup**: Creates networking, volume management, and environment configurations.

## When to Use This Skill

This skill activates when you need to:
- Create docker-compose.yml for multi-container applications
- Set up development environments with databases and caches
- Configure microservices architectures
- Manage service dependencies and health checks
- Use the `/docker-compose` or `/dc` command
- Implement production-ready service orchestration

## Examples

### Example 1: Full-Stack Application Setup

User request: "Create docker-compose for my React frontend and Node.js backend with PostgreSQL"

The skill will:
1. Analyze frontend and backend project structures
2. Configure three services with proper networking
3. Set up database persistence and health checks
4. Configure reverse proxy and environment variables

### Example 2: Microservices Architecture

User request: "/dc setup microservices with API gateway, services, Redis, and MongoDB"

The skill will:
1. Design service topology and communication patterns
2. Configure service discovery and load balancing
3. Set up data persistence and caching layers
4. Implement health checks and graceful shutdown

## Best Practices

- **Health Checks**: Ensure services start in proper order with dependency management
- **Volume Persistence**: Use named volumes for databases and important data
- **Network Security**: Implement proper network segmentation and isolation
- **Environment Management**: Separate development and production configurations
- **Resource Limits**: Set appropriate memory and CPU constraints for production

## Integration

This skill integrates with the Docker ecosystem by providing complete orchestration solutions. It works alongside docker-build-optimization for comprehensive containerization and docker-troubleshooting for issue resolution.