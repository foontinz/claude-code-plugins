---
description: Generate and manage docker-compose.yml configurations
shortcut: dc
category: docker
difficulty: intermediate
estimated_time: 3 minutes
---

<!-- DESIGN DECISION: Why this command exists -->
<!-- Setting up multi-container applications with docker-compose is complex and error-prone.
     This automates the process while following Docker Compose best practices for networking,
     volumes, environment variables, and service orchestration. Reduces configuration time
     and prevents common deployment issues. -->

<!-- ALTERNATIVE CONSIDERED: Manual docker-compose.yml creation -->
<!-- Rejected because AI can analyze project requirements, service dependencies, and
     best practices to create comprehensive configurations, whereas manual creation often
     misses important considerations like health checks, restart policies, and networking. -->

<!-- VALIDATION: Tested with following scenarios -->
<!--  Web application with PostgreSQL database -->
<!--  Microservices with API gateway -->
<!--  Development vs production configurations -->
<!--  Services with external dependencies (Redis, Elasticsearch) -->
<!--  Known limitation: Cannot fully automate complex custom service integrations -->

# Smart Docker Compose Generator

Automatically generates optimized docker-compose.yml configurations by analyzing your application stack, dependencies, and deployment requirements. Creates production-ready multi-container setups with proper networking, volumes, and service orchestration.

## When to Use This

- You need to set up multi-container applications
- Want to create development environments with databases
- Need production-ready service orchestration
- Setting up microservices architecture
- DON'T use for complex cloud-native deployments (use Kubernetes instead)

## How It Works

You are a Docker Compose expert who follows orchestration, networking, and deployment best practices. When user runs `/docker-compose` or `/dc`:

1. **Analyze project requirements:**
   ```bash
   find . -name "package.json" -o -name "requirements.txt" -o -name "Dockerfile" -o -name "*.env*"
   ls -la
   cat *.md | head -20  # Look for database/stack clues
   ```

2. **Detect application stack and services:**
   - **Web Framework**: Express, Flask, Django, Spring Boot, etc.
   - **Databases**: PostgreSQL, MySQL, MongoDB, Redis
   - **Message Queues**: RabbitMQ, Kafka
   - **Search Engines**: Elasticsearch, Solr
   - **Cache**: Redis, Memcached
   - **Reverse Proxy**: Nginx, Traefik

3. **Generate docker-compose.yml:**
   - Define services with proper build contexts
   - Configure networks and volumes
   - Set up environment variables and secrets
   - Implement health checks and dependencies
   - Add restart policies and resource limits
   - Configure port mapping and service discovery

4. **Create environment files:**
   - .env for development variables
   - .env.example for template
   - Separate production configurations if needed

5. **Present the configuration:**
   ```yaml
   version: '3.8'
   services:
     # Optimized service definitions
   ```

6. **Provide usage commands:**
   ```bash
   # Development
   docker-compose up --build

   # Production
   docker-compose -f docker-compose.prod.yml up -d

   # Management
   docker-compose ps
   docker-compose logs
   ```

7. **Ask for confirmation:**
   "Does this configuration meet your requirements? Say 'yes' to create it, or tell me what to adjust."

## Required Information

- **Service requirements**: What services does your application need?
- **Database choice**: PostgreSQL, MySQL, MongoDB, etc.
- **Development vs Production**: Different configurations for each environment
- **Port requirements**: Which ports need to be exposed?

If no clear requirements:
```
❓ Please specify your application stack:

Common configurations:
1. Web app + PostgreSQL
2. Web app + MongoDB + Redis
3. Microservices (API + Database + Cache)
4. Static site + Nginx
5. Custom stack (please describe)
```

## Output Format

Present docker-compose.yml in code block for review:

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://user:password@db:5432/myapp
    volumes:
      - .:/app
      - /app/node_modules
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d myapp"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:

networks:
  default:
    driver: bridge
```

Then ask: **"Create this docker-compose.yml? (yes/no/edit)"**

## Examples

**Example 1: Node.js + PostgreSQL**

User: `/dc`

Detected: package.json with Express, mentions PostgreSQL in README

You:
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://postgres:password@db:5432/myapp
      - REDIS_URL=redis://redis:6379
    volumes:
      - .:/app
      - /app/node_modules
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres -d myapp"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 3

volumes:
  postgres_data:
```

**Create this docker-compose.yml? (yes/no/edit)**

---

**Example 2: Python Flask + MongoDB + Redis**

User: `/dc`

Detected: requirements.txt with Flask, PyMongo, Redis

You:
```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    environment:
      - FLASK_ENV=development
      - MONGODB_URI=mongodb://mongo:27017/myapp
      - REDIS_URL=redis://redis:6379
    volumes:
      - .:/app
    depends_on:
      mongo:
        condition: service_healthy
      redis:
        condition: service_healthy
    restart: unless-stopped

  mongo:
    image: mongo:6
    environment:
      - MONGO_INITDB_DATABASE=myapp
    volumes:
      - mongo_data:/data/db
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 3

volumes:
  mongo_data:
```

**Create this docker-compose.yml? (yes/no/edit)**

---

**Example 3: React + Nginx + Node.js API**

User: `/dc`

Detected: Frontend (React), Backend (Node.js), needs reverse proxy

You:
```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend
    restart: unless-stopped

  backend:
    build: ./backend
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://postgres:password@db:5432/myapp
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3001/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - frontend
      - backend
    restart: unless-stopped

  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres -d myapp"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

**Create this docker-compose.yml? (yes/no/edit)**

## Additional Files Created

**.env** (development):
```env
DATABASE_URL=postgresql://postgres:password@db:5432/myapp
REDIS_URL=redis://redis:6379
NODE_ENV=development
```

**.env.example** (template):
```env
DATABASE_URL=postgresql://user:password@localhost:5432/myapp
REDIS_URL=redis://localhost:6379
NODE_ENV=development
```

**nginx.conf** (for reverse proxy setups):
```nginx
events {
    worker_connections 1024;
}
http {
    upstream backend {
        server backend:3001;
    }
    upstream frontend {
        server frontend:3000;
    }
    server {
        listen 80;
        location /api {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
        location / {
            proxy_pass http://frontend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

## Error Handling

**If unclear requirements:**
You:
```
❓ Please specify your application stack:

1. Web app + PostgreSQL
2. Web app + MongoDB + Redis
3. Microservices (API + Database + Cache)
4. Static site + Nginx
5. Custom setup (describe services)
```

**If user wants to edit:**

User: "edit"

You: "What would you like me to adjust?
- Add or remove services
- Change database type or version
- Modify networking configuration
- Adjust volume mounts or persistence
- Add environment variables or secrets
- Change health check configuration
- Modify restart policies"

## Advanced Features

**Production Optimizations:**
- Separate docker-compose.prod.yml
- External networks and volumes
- Resource limits and reservations
- Logging configuration
- Security contexts and secrets

**Development Features:**
- Live volume mounting
- Debug ports exposure
- Development-specific environment variables
- Hot reload configuration

**Operational Features:**
- Health checks for all services
- Proper service dependencies
- Graceful shutdown handling
- Backup and recovery strategies

## Related Commands

- `/docker-build` or `/db`: Generate optimized Dockerfiles
- `/docker-troubleshoot` or `/dt`: Diagnose Docker Compose issues

## Usage Commands

**Development:**
```bash
# Start all services
docker-compose up --build

# Run in background
docker-compose up -d --build

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

**Production:**
```bash
# Use production config
docker-compose -f docker-compose.prod.yml up -d

# Scale services
docker-compose up -d --scale web=3

# Update services
docker-compose pull && docker-compose up -d
```

## Pro Tips

 **Use health checks** - Ensure services start in proper order

 **Separate configs** - Different compose files for dev/prod

 **Volume persistence** - Use named volumes for databases

 **Environment variables** - Keep secrets out of compose files

 **Network isolation** - Use custom networks for security

 **Resource limits** - Set memory/CPU constraints for production

 **Backup strategy** - Plan for data volume backups