# Docker Fundamentals and Practical Tasks

## Task 1: Theory Questions

### Docker Fundamentals
#### 1. What is a Docker container, and how is it different from a virtual machine (VM)?
A Docker container is a lightweight, portable, and self-contained unit that packages an application along with its dependencies (such as libraries, configurations, and other required files). It ensures the application can run consistently across various environments. Containers do not contain an entire operating system, unlike virtual machines, which makes them more efficient and faster.

#### 2. What is the purpose of a Dockerfile? Explain the significance of directives like `FROM`, `COPY`, `RUN`, and `CMD`.
A Dockerfile is a plain text file containing instructions for building a Docker image. Each directive contributes a layer to the image, ensuring consistency and reproducibility.

- **FROM:** Specifies the base image to build upon. Example: `FROM ubuntu:latest`.
- **COPY:** Copies files or directories from the host to the image. Example: `COPY ./app /app`.
- **RUN:** Executes commands during the image build process. Example: `RUN apt-get update && apt-get install -y nginx`.
- **CMD:** Defines the default command to run when a container starts. Example: `CMD ["nginx", "-g", "daemon off;"]`.

### Image Management
#### How does Docker optimize space and performance using image layers?
1. **Layer Caching:** Docker caches image layers to avoid redundant builds.
2. **Layer Reuse:** Layers are shared across images, minimizing storage.
3. **Union Filesystem:** Changes are stacked as layers without duplication.
4. **Minimizing Layer Size:** Combining commands reduces the number of layers.
5. **Writable Container Layer:** Containers use a writable layer during runtime, with ephemeral changes.

### Docker Volumes
#### What are Docker volumes, and why are they important?
Docker volumes provide persistent storage independent of a container's lifecycle.

**Benefits:**
- Data persistence beyond container restarts.
- Separation of data from application logic.
- Data sharing between multiple containers.
- Optimized I/O operations for better performance.

**Example:**
```bash
# Running MySQL with a persistent volume
docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=root_password \
  -v mysql-data:/var/lib/mysql \
  mysql:8
```

### Networking in Docker
#### Docker Network Modes
- **Bridge Mode:** Default mode with isolated internal communication.
- **Host Mode:** Shares the host network stack.
- **None Mode:** No network connectivity for the container.

#### Container-to-Container Communication
Containers on the same Docker network communicate using container names as hostnames.

**Example:**
```bash
# Creating a custom bridge network
docker network create mynetwork

# Running two containers on the same network
docker run --network mynetwork --name webserver nginx
```

---

## Task 2: Practical Implementations

### Part A: Dockerfile Creation
#### Requirements:
- Use Ubuntu as the base image.
- Install Nginx and serve a custom `index.html` file.
- Expose port 8080.
- Start Nginx in the foreground.

#### Dockerfile Example:
```Dockerfile
# Use Ubuntu as the base image
FROM ubuntu:latest

# Install Nginx
RUN apt-get update && apt-get install -y nginx

# Copy custom index.html
COPY index.html /var/www/html/index.html

# Expose port 8080
EXPOSE 8080

# Start Nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
```

#### Build and Run the Docker Image:
```bash
# Build the Docker image
docker build -t custom-nginx .

# Run the container
docker run -d -p 8080:80 custom-nginx
```

### Part B: Multi-Container Setup with Docker Compose
#### Requirements:
- One Nginx web server container.
- One PostgreSQL database container.
- Persistent database using a Docker volume.

#### Docker Compose Example:
```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "8080:80"
    depends_on:
      - db
  db:
    image: postgres
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

#### Run the Setup:
```bash
# Start the services
docker-compose up -d
```

### Part C: Resource Limiting
#### Command to Run with Resource Limits:
```bash
# Run Nginx with resource limits
docker run -d --name limited-nginx --memory 512m --cpus 1 nginx
```

#### Benefits of Resource Limits:
- Prevents a single container from consuming excessive system resources.
- Helps maintain stability in production environments.
- Ensures fair resource allocation among services.

---

## Conclusion
This task covered essential Docker concepts and practical exercises, including Dockerfile creation, multi-container setups, and resource management. These activities provide foundational knowledge for containerized application development and deployment in real-world scenarios.

