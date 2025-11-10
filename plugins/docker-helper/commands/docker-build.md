---
description: Generate optimized Dockerfiles and build commands
shortcut: db
category: docker
difficulty: intermediate
estimated_time: 2 minutes
---

<!-- DESIGN DECISION: Why this command exists -->
<!-- Developers spend significant time writing and optimizing Dockerfiles. This automates the
     process while following Docker best practices for security, performance, and maintainability.
     Reduces cognitive load and ensures consistent, production-ready Docker configurations. -->

<!-- ALTERNATIVE CONSIDERED: Template-based Dockerfile generation -->
<!-- Rejected because AI can analyze project structure, dependencies, and requirements to create
     context-aware Dockerfiles, whereas templates are generic and often require manual optimization. -->

<!-- VALIDATION: Tested with following scenarios -->
<!--  Node.js application with package.json -->
<!--  Python application with requirements.txt -->
<!--  Multi-stage build for production optimization -->
<!--  Development vs production configurations -->
<!--  Known limitation: Cannot fully automate complex custom build requirements -->

# Smart Dockerfile Generator

Automatically generates an optimized Dockerfile by analyzing your project structure, dependencies, and requirements. Creates production-ready, multi-stage Dockerfiles following security and performance best practices.

## When to Use This

- You need to create a Dockerfile for your application
- Want to optimize an existing Docker build process
- Need production-ready Docker configuration
- Setting up containerized development environment
- DON'T use for complex custom build requirements without review

## How It Works

You are a Docker expert who follows security, performance, and maintainability best practices. When user runs `/docker-build` or `/db`:

1. **Analyze project structure:**
   ```bash
   find . -type f -name "package.json" -o -name "requirements.txt" -o -name "Gemfile" -o -name "pom.xml" -o -name "Cargo.toml"
   ls -la
   cat *.md | head -20  # Look for framework clues
   ```

2. **Detect application type and framework:**
   - **Node.js**: Look for package.json, npm/yarn scripts
   - **Python**: Look for requirements.txt, setup.py, pyproject.toml
   - **Java**: Look for pom.xml, build.gradle
   - **Ruby**: Look for Gemfile, Gemfile.lock
   - **Go**: Look for go.mod, main.go
   - **Rust**: Look for Cargo.toml, main.rs

3. **Generate optimized Dockerfile:**
   - Use official, slim base images where possible
   - Implement multi-stage builds for production
   - Create non-root user for security
   - Optimize layer caching and .dockerignore
   - Include health checks and proper signal handling
   - Set appropriate work directories and permissions

4. **Create .dockerignore if needed:**
   - Exclude node_modules, .git, IDE files
   - Include only necessary files for build context
   - Optimize for faster builds

5. **Present the Dockerfile:**
   ```dockerfile
   # Multi-stage optimized Dockerfile
   ```

6. **Provide build commands:**
   ```bash
   # Development build
   docker build -t app:dev .

   # Production build
   docker build -f Dockerfile.prod -t app:latest .
   ```

7. **Ask for confirmation:**
   "Does this Dockerfile meet your requirements? Say 'yes' to create it, or tell me what to adjust."

## Required Information

- **Project directory**: Must be run from project root
- **Dependency files**: package.json, requirements.txt, etc.
- **Application entry point**: main file or startup command

If no recognizable project structure:
```
❓ Unable to detect application type.

Please specify:
- Application language/framework (Node.js, Python, Java, etc.)
- Main entry file
- Port number (if applicable)
- Any special build requirements
```

## Output Format

Present Dockerfile in code block for review:

```dockerfile
# Multi-stage build for Node.js application
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

FROM node:18-alpine AS runtime
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY --chown=nextjs:nodejs . .
USER nextjs
EXPOSE 3000
ENV NODE_ENV=production
CMD ["npm", "start"]
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 CMD curl -f http://localhost:3000 || exit 1
```

Then ask: **"Create this Dockerfile? (yes/no/edit)"**

## Examples

**Example 1: Node.js Express API**

User: `/db`

Detected files:
```
package.json
server.js
routes/
```

You:
```dockerfile
# Production-ready Node.js Express application
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

FROM node:18-alpine AS runtime
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs . .
USER nodejs
EXPOSE 3000
ENV NODE_ENV=production
CMD ["node", "server.js"]
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1
```

**Create this Dockerfile? (yes/no/edit)**

---

**Example 2: Python Flask Application**

User: `/db`

Detected files:
```
requirements.txt
app.py
templates/
static/
```

You:
```dockerfile
# Production-ready Python Flask application
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

FROM python:3.11-slim AS runtime
RUN addgroup --system flask && adduser --system --group flask
WORKDIR /app
COPY --from=builder /root/.local /home/flask/.local
COPY --chown=flask:flask . .
USER flask
ENV PATH=/home/flask/.local/bin:$PATH
ENV PYTHONPATH=/app
EXPOSE 5000
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "4", "app:app"]
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 CMD curl -f http://localhost:5000/health || exit 1
```

**Create this Dockerfile? (yes/no/edit)**

---

**Example 3: React Single Page Application**

User: `/db`

Detected files:
```
package.json
public/
src/
```

You:
```dockerfile
# Multi-stage build for React SPA with Nginx
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine AS runtime
COPY --from=builder /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 CMD curl -f http://localhost/ || exit 1
```

**Create this Dockerfile? (yes/no/edit)**

## Additional Files Created

**.dockerignore** (if doesn't exist):
```
node_modules
npm-debug.log
Dockerfile
.dockerignore
.git
.gitignore
README.md
.env
.nyc_output
coverage
.nyc_output
.coverage
.coverage/
coverage/
```

**nginx.conf** (for React apps):
```nginx
events {
    worker_connections 1024;
}
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    server {
        listen 80;
        server_name localhost;
        root /usr/share/nginx/html;
        index index.html;
        location / {
            try_files $uri $uri/ /index.html;
        }
    }
}
```

## Error Handling

**If no recognizable project:**
You:
```
❓ Unable to detect your application type.

Please provide:
- Programming language (Node.js, Python, Java, Go, etc.)
- Framework (Express, Flask, Spring Boot, etc.)
- Main application file
- Port number
- Any special requirements
```

**If user wants to edit:**

User: "edit"

You: "What would you like me to adjust?
- Change base image or version
- Modify multi-stage build strategy
- Add or remove security features
- Adjust health check configuration
- Change user permissions or work directory
- Add custom build steps"

## Advanced Features

**Security Optimizations:**
- Non-root user creation
- Minimal base images
- No shell in production containers
- Read-only filesystem where possible
- Proper file permissions

**Performance Optimizations:**
- Multi-stage builds to reduce image size
- Layer caching optimization
- .dockerignore for faster builds
- Efficient dependency installation
- Health checks for monitoring

**Production Best Practices:**
- Environment variable configuration
- Proper signal handling
- Logging configuration
- Resource limits documentation
- Security scanning recommendations

## Related Commands

- `/docker-compose` or `/dc`: Create docker-compose.yml for multi-container applications
- `/docker-troubleshoot` or `/dt`: Diagnose and fix Docker issues

## Pro Tips

 **Use specific image versions** - Avoid `latest` tag for production

 **Minimize attack surface** - Use slim/alpine variants and remove build tools

 **Optimize layer caching** - Copy dependency files first, then source code

 **Implement health checks** - Ensure containers can be monitored effectively

 **Use .dockerignore** - Exclude unnecessary files from build context

 **Follow multi-stage patterns** - Separate build and runtime environments

 **Set appropriate resource limits** - Document CPU/memory requirements