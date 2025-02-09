# Use Ubuntu as the base image
FROM ubuntu:latest

# Update and install Nginx
RUN apt-get update && apt-get install -y nginx

# Copy the custom index.html to the appropriate Nginx directory
COPY index.html /var/www/html/index.html

# Expose port 8080
EXPOSE 8080

# Start Nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
