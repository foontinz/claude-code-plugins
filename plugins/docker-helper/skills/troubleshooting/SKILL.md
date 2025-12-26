---
name: docker-troubleshooting
description: |
  This skill diagnoses and resolves common Docker issues including container startup failures, networking problems, volume mounting errors, image build failures, and performance issues. It provides systematic troubleshooting approaches, analyzes logs and error messages, and offers step-by-step solutions. Use this when experiencing Docker problems, container failures, build errors, or when Docker commands are not working as expected. It is especially useful for debugging development environments and production deployment issues.
---

## Overview

This skill empowers Claude to systematically diagnose and resolve Docker-related problems. By analyzing error messages, container states, and system configurations, it provides targeted solutions for common Docker issues and performance problems.

## How It Works

1. **Issue Detection**: The skill analyzes error messages, container states, and system symptoms.
2. **Root Cause Analysis**: Identifies the underlying cause of Docker problems.
3. **Solution Generation**: Provides step-by-step resolution procedures and preventive measures.

## When to Use This Skill

This skill activates when you need to:
- Diagnose container startup failures
- Resolve Docker networking issues
- Fix volume mounting and permission problems
- Troubleshoot image build errors
- Debug performance and resource issues
- Analyze container logs and error messages
- Resolve Docker daemon or system issues

## Examples

### Example 1: Container Startup Failure

User request: "My container keeps restarting with exit code 1"

The skill will:
1. Check container logs and recent events
2. Analyze exit codes and error messages
3. Examine health check configurations
4. Provide step-by-step debugging procedures

### Example 2: Networking Issues

User request: "Containers can't communicate with each other"

The skill will:
1. Analyze network configurations and connectivity
2. Check service discovery and DNS resolution
3. Examine firewall and port mapping issues
4. Provide network troubleshooting commands

## Common Issues Addressed

**Container Runtime Issues**:
- Exit codes and signal handling
- Health check failures
- Resource constraints and limits
- Permission and user access problems

**Image Build Problems**:
- Dockerfile syntax errors
- Layer caching issues
- Dependency installation failures
- Multi-stage build complications

**Networking Challenges**:
- Port conflicts and binding issues
- Service discovery failures
- DNS resolution problems
- Network isolation and security

**Volume and Storage Issues**:
- Mount point permission errors
- Volume driver problems
- Data persistence failures
- Disk space and cleanup

## Best Practices

- **Systematic Approach**: Start with basic checks before complex solutions
- **Log Analysis**: Always examine container logs and system events first
- **Isolation Testing**: Test components individually before integration
- **Resource Monitoring**: Check memory, CPU, and disk usage regularly
- **Preventive Measures**: Implement monitoring and health checks proactively

## Integration

This skill integrates with the Docker ecosystem by providing comprehensive troubleshooting capabilities. It complements docker-build-optimization and docker-compose-management by ensuring that generated configurations work correctly and efficiently.