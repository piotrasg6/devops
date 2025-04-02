# Docker Command and Configuration Cheat Sheet

## Docker Installation and Setup

### Installation
```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# CentOS/RHEL
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io

# macOS
# Download Docker Desktop from https://www.docker.com/products/docker-desktop

# Windows
# Download Docker Desktop from https://www.docker.com/products/docker-desktop
```

### Post-installation Steps
```bash
# Add your user to the docker group (Linux)
sudo usermod -aG docker $USER
newgrp docker  # Apply group changes without logout

# Start Docker on boot (Linux)
sudo systemctl enable docker

# Check Docker version
docker --version
docker version  # Detailed version info

# Test installation
docker run hello-world
```

### Docker Configuration
```bash
# Docker daemon configuration file: /etc/docker/daemon.json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "default-address-pools": [
    {"base": "172.30.0.0/16", "size": 24}
  ],
  "insecure-registries": ["my-registry:5000"],
  "registry-mirrors": ["https://mirror.gcr.io"],
  "data-root": "/var/lib/docker"
}

# Restart Docker after configuration changes
sudo systemctl restart docker
```

## Basic Docker Commands

### Image Management
```bash
# List images
docker images
docker image ls
docker image ls -a  # Show all images (including intermediates)
docker image ls --digests  # Show image digests
docker image ls --filter "dangling=true"  # Show untagged images

# Pull an image
docker pull ubuntu
docker pull ubuntu:20.04
docker pull registry.example.com/myimage:tag

# Build image from Dockerfile
docker build -t myapp:1.0 .
docker build -t myapp:1.0 -f Dockerfile.prod .  # Custom Dockerfile
docker build --no-cache -t myapp:1.0 .  # Skip cache

# Tag an image
docker tag myapp:1.0 myapp:latest
docker tag myapp:1.0 registry.example.com/myapp:1.0

# Push an image
docker push myapp:1.0
docker push registry.example.com/myapp:1.0

# Search Docker Hub
docker search nginx
docker search --filter is-official=true nginx

# Save an image as a tarball
docker save -o myapp.tar myapp:1.0
docker save myapp:1.0 | gzip > myapp.tar.gz

# Load an image from a tarball
docker load -i myapp.tar
cat myapp.tar.gz | docker load

# Remove an image
docker rmi myapp:1.0
docker rmi 12345abcde  # Delete by image ID
docker rmi $(docker images -q)  # Remove all images
docker image prune  # Remove unused images
docker image prune -a  # Remove all unused images
```

### Container Lifecycle
```bash
# Run a container
docker run nginx
docker run --name my-nginx nginx
docker run -d nginx  # Detached mode (background)
docker run -it ubuntu bash  # Interactive with terminal
docker run --rm nginx  # Auto-remove when stopped
docker run -p 8080:80 nginx  # Port mapping (host:container)
docker run -P nginx  # Map all exposed ports to random host ports

# Container management
docker ps  # List running containers
docker ps -a  # List all containers (including stopped)
docker ps -q  # Only display container IDs

# Start, stop, restart containers
docker start my-nginx
docker stop my-nginx
docker restart my-nginx
docker pause my-nginx
docker unpause my-nginx

# Remove containers
docker rm my-nginx
docker rm -f my-nginx  # Force remove running container
docker rm $(docker ps -aq)  # Remove all containers
docker container prune  # Remove all stopped containers

# Container operations
docker rename my-container my-new-name
docker update --memory 512m --cpuset-cpus 0,1 my-container
```

### Container Interaction
```bash
# Execute commands in a running container
docker exec -it my-nginx bash  # Start shell session
docker exec my-nginx ls -la /etc/nginx  # Run command

# Copy files between host and container
docker cp my-nginx:/etc/nginx/nginx.conf ./nginx.conf
docker cp ./nginx.conf my-nginx:/etc/nginx/nginx.conf

# View container logs
docker logs my-nginx
docker logs -f my-nginx  # Follow log output
docker logs --tail 100 my-nginx  # Show last 100 lines
docker logs --since 2023-01-01T00:00:00 my-nginx  # Show logs since timestamp

# View container details
docker inspect my-nginx
docker inspect -f '{{.NetworkSettings.IPAddress}}' my-nginx  # Format output
docker top my-nginx  # Show running processes
docker stats  # Show live resource usage
docker stats my-nginx  # Show stats for specific container
```

### Container Resource Management
```bash
# CPU and memory limits
docker run -d --name limited-nginx --memory="512m" --cpus="0.5" nginx

# Restart policies
docker run -d --restart=always nginx
docker run -d --restart=on-failure:5 nginx
docker run -d --restart=unless-stopped nginx

# Resource reservation
docker run -d --memory="1g" --memory-reservation="750m" nginx
docker run -d --cpus="1.0" --cpu-shares=512 nginx
```

## Docker Compose

### Basic Compose Commands
```bash
# Install Docker Compose (if not installed with Docker Desktop)
# Linux
sudo curl -L "https://github.com/docker/compose/releases/download/v2.18.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Check version
docker-compose --version

# Start services
docker-compose up
docker-compose up -d  # Detached mode
docker-compose up --build  # Rebuild images before starting
docker-compose up -d service1 service2  # Start specific services

# Stop services
docker-compose down
docker-compose down --volumes  # Remove volumes
docker-compose down --rmi all  # Remove images
docker-compose down --rmi local  # Remove only local images

# Other lifecycle commands
docker-compose start
docker-compose stop
docker-compose restart
docker-compose pause
docker-compose unpause

# List containers
docker-compose ps

# View logs
docker-compose logs
docker-compose logs -f  # Follow log output
docker-compose logs service1  # Logs for specific service

# Execute command in service container
docker-compose exec web bash
docker-compose exec db psql -U postgres

# View resource usage
docker-compose top
```

### Docker Compose File (docker-compose.yml)
```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    # or build from Dockerfile
    build:
      context: ./web
      dockerfile: Dockerfile.prod
      args:
        ENV: production
    ports:
      - "8080:80"
    volumes:
      - ./web/html:/usr/share/nginx/html
      - ./web/nginx.conf:/etc/nginx/nginx.conf:ro
    environment:
      - NGINX_HOST=example.com
      - NGINX_PORT=80
    env_file:
      - ./.env
    depends_on:
      - db
    restart: always
    networks:
      - frontend
      - backend
    deploy:
      replicas: 2
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 5s

  db:
    image: postgres:13
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=secret
      - POSTGRES_USER=myuser
      - POSTGRES_DB=mydb
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U myuser"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
    driver: local

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true  # Only accessible by other containers
```

### Compose File Variables and Extensions
```yaml
# .env file
APP_IMAGE=myapp
APP_TAG=1.0
POSTGRES_PASSWORD=secret

# In docker-compose.yml
services:
  app:
    image: ${APP_IMAGE}:${APP_TAG}
    environment:
      DB_PASSWORD: ${POSTGRES_PASSWORD}

# Using multiple compose files (override)
# docker-compose.yml - Base configuration
# docker-compose.override.yml - Development overrides
# docker-compose.prod.yml - Production overrides

# Run with specific config
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d

# Extending services
# In docker-compose.base.yml
services:
  app:
    image: myapp:latest
    environment:
      - ENV=production

# In docker-compose.yml
services:
  web:
    extends:
      file: docker-compose.base.yml
      service: app
    ports:
      - "8080:80"
```

## Dockerfile

### Basic Dockerfile
```dockerfile
# Use an official base image
FROM ubuntu:20.04

# Set metadata
LABEL maintainer="your@email.com"
LABEL version="1.0"
LABEL description="My application"

# Set environment variables
ENV APP_HOME=/app \
    NODE_ENV=production

# Set working directory
WORKDIR $APP_HOME

# Copy files
COPY . .
COPY package.json package-lock.json ./
COPY ./src /app/src

# Run commands
RUN apt-get update && apt-get install -y \
    nodejs \
    npm \
    && rm -rf /var/lib/apt/lists/* \
    && npm install

# Expose ports
EXPOSE 3000

# Set user
USER node

# Define volume
VOLUME ["/app/data"]

# Set entrypoint (preferred for binaries)
ENTRYPOINT ["node", "app.js"]

# Set default command (can be overridden)
CMD ["--port", "3000"]

# Healthcheck
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
  CMD curl -f http://localhost:3000/ || exit 1
```

### Multi-stage Builds
```dockerfile
# Build stage
FROM node:14 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Advanced Dockerfile Techniques
```dockerfile
# Using build arguments
ARG VERSION=latest
FROM ubuntu:$VERSION
ARG ENV=production
ENV APP_ENV=$ENV

# Node.js example with non-root user
FROM node:14-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
# Create a non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001 -G nodejs
# Change ownership
RUN chown -R nodejs:nodejs /app
USER nodejs
CMD ["node", "server.js"]

# Using shell form vs exec form
# Shell form
CMD npm start
# Exec form (preferred)
CMD ["npm", "start"]

# Optimize layer caching
COPY package.json package-lock.json ./
RUN npm install
# Copy source code after installing dependencies
COPY . .

# Use of .dockerignore
# Example .dockerignore file:
# node_modules
# npm-debug.log
# Dockerfile*
# docker-compose*
# .git
# .gitignore
# README.md
```

### Best Practices
```dockerfile
# 1. Use specific tags
FROM node:14.17.0-alpine3.13

# 2. Use multi-stage builds
# 3. Group RUN commands
RUN apt-get update && \
    apt-get install -y --no-install-recommends gcc && \
    rm -rf /var/lib/apt/lists/*

# 4. Order instructions from least to most frequently changing
# 5. Use .dockerignore file
# 6. Use non-root users
# 7. Scan for vulnerabilities
# docker scan myimage:latest

# 8. Minimize the number of layers
# 9. Use buildkit
# DOCKER_BUILDKIT=1 docker build .

# 10. Set proper environment variables
ENV PATH="${PATH}:/app/node_modules/.bin"
```

## Docker Networking

### Network Commands
```bash
# List networks
docker network ls

# Inspect a network
docker network inspect bridge

# Create a network
docker network create my-network
docker network create --driver overlay --attachable my-overlay
docker network create --subnet=172.20.0.0/16 --gateway=172.20.0.1 my-subnet

# Connect/disconnect containers
docker network connect my-network my-container
docker network disconnect my-network my-container

# Remove a network
docker network rm my-network
docker network prune  # Remove all unused networks
```

### Network Drivers
```bash
# Bridge network (default)
docker run --network bridge nginx

# Host network (uses host's network stack)
docker run --network host nginx

# None network (isolated)
docker run --network none nginx

# Overlay network (swarm mode)
docker network create --driver overlay my-swarm-network

# Macvlan network (assign MAC address to container)
docker network create -d macvlan \
  --subnet=192.168.0.0/24 \
  --gateway=192.168.0.1 \
  -o parent=eth0 my-macvlan-net
```

### Network Configuration in Compose
```yaml
version: '3.8'

services:
  app:
    image: myapp:latest
    networks:
      - frontend
      - backend
    # Static IP address (requires subnet configuration)
    networks:
      backend:
        ipv4_address: 172.20.0.2

  db:
    image: postgres:13
    networks:
      - backend

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true  # Only accessible by other containers
    ipam:
      driver: default
      config:
        - subnet: 172.20.0.0/16
          gateway: 172.20.0.1
```

## Docker Volumes

### Volume Commands
```bash
# List volumes
docker volume ls

# Create a volume
docker volume create my-volume

# Inspect a volume
docker volume inspect my-volume

# Remove a volume
docker volume rm my-volume
docker volume prune  # Remove all unused volumes
```

### Volume Types and Usage
```bash
# Named volume
docker run -v my-volume:/app/data nginx

# Bind mount (absolute path)
docker run -v /host/path:/container/path nginx

# Bind mount (relative path)
docker run -v ./logs:/app/logs nginx

# Read-only mount
docker run -v /host/path:/container/path:ro nginx

# tmpfs mount (stored in memory)
docker run --tmpfs /app/temp nginx

# Anonymous volume
docker run -v /app/data nginx
```

### Volume Configuration in Compose
```yaml
version: '3.8'

services:
  app:
    image: myapp:latest
    volumes:
      - app-data:/app/data
      - ./config:/app/config
      - logs:/app/logs
      - /etc/localtime:/etc/localtime:ro  # Sync container time with host
      - /tmp:/tmp:rw  # Shared tmp directory

volumes:
  app-data:
    driver: local
  logs:
    driver: local
    driver_opts:
      type: 'none'
      o: 'bind'
      device: '/var/log/myapp'
```

## Docker Registry

### Public/Private Registry
```bash
# Docker Hub
docker login
docker push username/image:tag
docker pull username/image:tag
docker logout

# Private registry
docker login registry.example.com
docker push registry.example.com/username/image:tag
docker pull registry.example.com/username/image:tag
```

### Run a Local Registry
```bash
# Start a local registry
docker run -d -p 5000:5000 --name registry registry:2

# Push to local registry
docker tag myimage:latest localhost:5000/myimage:latest
docker push localhost:5000/myimage:latest

# Pull from local registry
docker pull localhost:5000/myimage:latest

# Registry with basic auth
docker run -d -p 5000:5000 --name registry \
  -v "$(pwd)"/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" \
  registry:2

# Create auth file
docker run --entrypoint htpasswd registry:2 -Bbn username password > auth/htpasswd
```

### Registry Configuration
```yaml
# config.yml
version: 0.1
log:
  level: info
storage:
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
auth:
  htpasswd:
    realm: Registry Realm
    path: /auth/htpasswd
```

## Docker Security

### Security Best Practices
```bash
# Run as non-root user
docker run -u 1000:1000 nginx
# In Dockerfile
USER 1000:1000

# Limit capabilities
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE nginx

# Read-only filesystem
docker run --read-only nginx

# Limit resources
docker run --memory=512m --memory-swap=512m --cpus=0.5 nginx

# Use seccomp profiles
docker run --security-opt seccomp=/path/to/seccomp.json nginx

# Use no-new-privileges
docker run --security-opt=no-new-privileges nginx

# Scan images for vulnerabilities
docker scan myimage:latest
```

### Content Trust
```bash
# Enable content trust
export DOCKER_CONTENT_TRUST=1

# Sign image during push
docker push myorg/myimage:latest

# Disable content trust for a single command
DOCKER_CONTENT_TRUST=0 docker pull myorg/myimage:latest
```

### Security in Compose
```yaml
version: '3.8'

services:
  app:
    image: myapp:latest
    user: "1000:1000"
    read_only: true
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE
    security_opt:
      - no-new-privileges
    tmpfs:
      - /tmp
      - /var/run
```

## Docker Swarm

### Swarm Initialization
```bash
# Initialize a swarm
docker swarm init --advertise-addr 192.168.1.10

# Join as a worker
docker swarm join --token WORKER-TOKEN 192.168.1.10:2377

# Join as a manager
docker swarm join --token MANAGER-TOKEN 192.168.1.10:2377

# Get join tokens
docker swarm join-token worker
docker swarm join-token manager

# Leave swarm
docker swarm leave
docker swarm leave --force  # Forced leave for manager
```

### Node Management
```bash
# List nodes
docker node ls

# Inspect a node
docker node inspect node-id
docker node inspect --pretty node-id

# Update node
docker node update --availability drain node-id  # Drain for maintenance
docker node update --availability active node-id  # Back to active
docker node update --role manager worker-node-id  # Promote to manager
docker node update --role worker manager-node-id  # Demote to worker

# Node labels
docker node update --label-add region=east node-id
```

### Service Management
```bash
# Create a service
docker service create --name web nginx
docker service create --name web --replicas 3 nginx
docker service create --name web \
  --publish 80:80 \
  --mount type=volume,source=web-data,target=/usr/share/nginx/html \
  nginx

# List services
docker service ls

# Service details
docker service ps web
docker service inspect web
docker service logs web

# Update a service
docker service update --image nginx:1.19 web
docker service update --replicas 5 web
docker service update --constraint-add node.role==manager web

# Rolling updates
docker service update \
  --update-parallelism 2 \
  --update-delay 10s \
  web

# Remove a service
docker service rm web
```

### Stack Deployment
```bash
# Deploy a stack from compose file
docker stack deploy -c docker-compose.yml my-stack

# List stacks
docker stack ls

# List services in a stack
docker stack services my-stack

# List tasks in a stack
docker stack ps my-stack

# Remove a stack
docker stack rm my-stack
```

### Secrets Management
```bash
# Create a secret from a file
docker secret create my-secret secret.txt

# Create a secret from STDIN
echo "mysecretdata" | docker secret create my-secret -

# List secrets
docker secret ls

# Inspect a secret
docker secret inspect my-secret

# Use secret in a service
docker service create \
  --name web \
  --secret my-secret \
  nginx

# Use secret at specific path
docker service create \
  --name web \
  --secret source=my-secret,target=/etc/nginx/ssl/cert.pem \
  nginx

# Remove a secret
docker secret rm my-secret
```

## Docker Monitoring and Logging

### Basic Monitoring
```bash
# View container stats
docker stats
docker stats my-container

# Get container metrics
docker container stats --no-stream

# View container processes
docker top my-container
```

### Logging
```bash
# View container logs
docker logs my-container
docker logs -f my-container  # Follow
docker logs --tail 100 my-container  # Last 100 lines
docker logs --since 2023-01-01T00:00:00 my-container  # Since time

# Configure logging driver
docker run --log-driver=syslog nginx
docker run --log-driver=json-file --log-opt max-size=10m --log-opt max-file=3 nginx

# Logging configuration in daemon.json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

### External Monitoring Tools
```bash
# Run cAdvisor
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  gcr.io/cadvisor/cadvisor:latest

# Run Prometheus
docker run \
  -p 9090:9090 \
  -v /path/to/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus

# Run Grafana
docker run \
  -p 3000:3000 \
  grafana/grafana
```

## Troubleshooting

### Container Troubleshooting
```bash
# Check container state
docker inspect my-container

# Check logs
docker logs my-container

# Interactive debugging
docker exec -it my-container bash
docker exec -it my-container /bin/sh

# Resource inspection
docker stats my-container

# Network debugging
docker exec my-container ping other-container
docker exec my-container curl http://other-container:8080

# Capture network traffic
docker run --net=container:my-container nicolaka/netshoot tcpdump -i eth0
```

### Image Troubleshooting
```bash
# Check image history
docker history myimage:latest

# Run container with debugging
docker run --rm -it myimage:latest /bin/bash

# Create a debugging image
FROM myimage:latest
RUN apt-get update && apt-get install -y curl iputils-ping tcpdump
```

### Docker Engine Troubleshooting
```bash
# Check Docker system info
docker info

# Check system events
docker events

# Check Docker daemon status
sudo systemctl status docker

# View Docker daemon logs
sudo journalctl -u docker

# Export container filesystem for analysis
docker export my-container > container.tar

# Verify all containers are running
docker ps -a --filter "status=exited"

# Docker system information
docker system df  # Disk usage
docker system info  # System-wide information
docker system prune  # Remove unused data
docker system prune -a  # Remove all unused data including images
```

## Advanced Docker Features

### Docker Buildx
```bash
# Enable buildx
export DOCKER_CLI_EXPERIMENTAL=enabled
docker buildx create --use

# Build multi-platform image
docker buildx build --platform linux/amd64,linux/arm64 -t username/myapp:latest .

# Build and push in one command
docker buildx build --platform linux/amd64,linux/arm64 \
  -t username/myapp:latest \
  --push \
  .

# Inspect builder
docker buildx inspect
```

### Docker Context
```bash
# List contexts
docker context ls

# Create a new context
docker context create my-remote --docker "host=ssh://user@hostname"

# Use a specific context
docker context use my-remote

# Remove a context
docker context rm my-remote
```

### Docker Plugins
```bash
# List plugins
docker plugin ls

# Install a plugin
docker plugin install grafana/loki-docker-driver:latest --alias loki

# Inspect a plugin
docker plugin inspect loki

# Enable/disable plugin
docker plugin disable loki
docker plugin enable loki

# Remove a plugin
docker plugin rm loki
```

### Environment Variables
```bash
# Common Docker environment variables
DOCKER_HOST=tcp://192.168.1.10:2375  # Connect to remote daemon
DOCKER_TLS_VERIFY=1  # Enable TLS
DOCKER_CERT_PATH=~/.docker/certs  # Path to TLS certs
DOCKER_CONFIG=~/.docker  # Config directory
DOCKER_CONTENT_TRUST=1  # Enable content trust
DOCKER_BUILDKIT=1  # Enable BuildKit
DOCKER_CLI_EXPERIMENTAL=enabled  # Enable experimental features
COMPOSE_FILE=docker-compose.yml:docker-compose.prod.yml  # Multiple compose files
COMPOSE_PROJECT_NAME=myproject  # Custom project name
```

## Docker Desktop Settings

### Resource Allocation
```
# macOS/Windows settings (approximate paths)
# CPU: 2+ cores recommended
# Memory: 4+ GB recommended
# Swap: 1+ GB recommended
# Disk image size: 60+ GB recommended
```

### Docker Desktop Advanced Settings
```
# Enable Kubernetes
# Enable experimental features
# Enable BuildKit
# Configure registry mirrors
# Configure insecure registries
# Configure proxies
# Configure file sharing paths
```

### WSL 2 Integration (Windows)
```
# Enable WSL 2 integration
# Select which WSL distros can access Docker
# Configure resource allocation for WSL
```

## Production Best Practices

### Production Deployment Checklist
```
# 1. Use specific image tags (not 'latest')
# 2. Implement health checks
# 3. Set proper resource limits
# 4. Use secrets management
# 5. Enable container logging
# 6. Configure autorestart policies
# 7. Run containers as non-root
# 8. Use read-only filesystems when possible
# 9. Remove unnecessary capabilities
# 10. Scan images for vulnerabilities
# 11. Implement proper backup strategies
# 12. Use orchestration for high availability
```

### Security Checklist
```
# 1. Keep Docker updated
# 2. Run security audits (docker-bench-security)
# 3. Limit container resources
# 4. Minimize container privileges
# 5. Use Docker Content Trust
# 6. Configure proper networking
# 7. Scan images regularly
# 8. Use multi-stage builds to minimize image size
# 9. Implement proper secret management
# 10. Follow principle of least privilege
```

### Performance Tips
```
# 1. Use multi-stage builds
# 2. Optimize Dockerfile (minimize layers)
# 3. Use .dockerignore file
# 4. Allocate sufficient resources to Docker
# 5. Use performance monitoring tools
# 6. Optimize networking (use host network when appropriate)
# 7. Use volume mounts efficiently
# 8. Minimize container dependencies
# 9. Keep images small and focused
# 10. Implement proper logging (avoid filling disks)
```
