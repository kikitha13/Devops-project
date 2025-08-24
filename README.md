# Full-Stack Dockerized Application

This repository contains a full-stack web application that is containerized using Docker. The application is set up to deploy on an Ubuntu virtual machine with Docker Compose, automated through a CI/CD pipeline using GitHub Actions, and exposed via an Nginx reverse proxy on port 80.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Repository Setup](#repository-setup)
- [Prerequisites](#prerequisites)
- [Containerization & Deployment](#containerization--deployment)
- [Database Setup](#database-setup)
- [CI/CD Pipeline](#cicd-pipeline)
- [Nginx Reverse Proxy](#nginx-reverse-proxy)

---

## Project Overview

This project comprises frontend and backend services packaged as Docker containers. The system is designed for easy deployment on an Ubuntu VM and managed with Docker Compose. It includes MongoDB as the database, automated builds and deployments via GitHub Actions, and Nginx configured as a reverse proxy to serve the application through port 80.
---
## Repository Setup

To set up the repository for this project, follow these steps:

1. **Create a new GitHub repository**  
   Go to GitHub and create a new repository

2. **Initialize your local project folder as a Git repository**  
git init



3. **Add all project files to the repository**  
git add .



4. **Commit the added files**  
git commit -m "Initial commit: Add complete project code"



5. **Add the remote GitHub repository URL**  
git remote add origin https://github.com/kikitha13/Devops-project.git



6. **Push the committed code to GitHub main branch**  
git push -u origin main

---
## Containerization & Deployment

 Dockerfiles placed in their respective frontend and backend directories.

### Build and Push Docker Images to Docker Hub

1. **Log in to Docker Hub**  
docker login


2. **Build Docker images**  
docker build -t kikitha/mean-backend:latest ./backend
docker build -t kikitha/mean-frontend:latest ./frontend

<img width="1919" height="974" alt="Screenshot 2025-08-24 181743" src="https://github.com/user-attachments/assets/c8fe9bd5-b42d-4aab-8454-82fcee2e8aef" />


3. **Push images to Docker Hub**  
docker push kikitha/mean-backend:latest ./backend
docker push kikitha/mean-frontend:latest ./frontend
<img width="1718" height="966" alt="Screenshot 2025-08-24 181758" src="https://github.com/user-attachments/assets/19fa9a50-9b81-4c65-a2ad-0a67f17c8083" />


---


### Set Up an Ubuntu Virtual Machine

- Create a new Ubuntu VM instance on your chosen cloud platform -AWS  
- Access the VM via SSH.
- <img width="1908" height="914" alt="Screenshot 2025-08-24 181820" src="https://github.com/user-attachments/assets/1e9a28b0-fcbc-492b-9213-ad07152a2185" />
<img width="1916" height="1079" alt="Screenshot 2025-08-24 182036" src="https://github.com/user-attachments/assets/61293850-cf71-431d-88ef-723c1d5b5f5a" />


- Install Docker and Docker Compose on the VM (see [Docker installation guides](https://docs.docker.com/engine/install/ubuntu/) and [Docker Compose installation](https://docs.docker.com/compose/install/)).

### Deploy Application on the VM Using Docker Compose
<img width="1910" height="1079" alt="Screenshot 2025-08-24 182058" src="https://github.com/user-attachments/assets/48e16650-a67c-4091-9d13-1810c538ed30" />


1. Copy the project's `docker-compose.yml` file and any necessary files to the Ubuntu VM.  
2. Run the following command to start the containers:  
docker-compose up -d



Docker Compose will pull the images from Docker Hub and start the frontend, backend, and database containers as specified.
<img width="1501" height="919" alt="Screenshot 2025-08-24 182145" src="https://github.com/user-attachments/assets/c250781c-5529-4dcd-b7a9-26b6ddf8f685" />

--

## MongoDB Setup Using Docker Compose

This project uses the **official MongoDB Docker image** (`mongo:6.0`) as part of the multi-service architecture defined in the `docker-compose.yml` file.

### Why use MongoDB in Docker?

- **Easy setup:** No need to install MongoDB directly on your machine or server.
- **Data persistence:** Uses Docker volumes to persist data.
- **Isolation:** Runs in its own container, simplifying management and upgrades.

### How MongoDB is configured

In `docker-compose.yml`, the MongoDB service is defined as:

services:
mongo:
image: mongo:6.0
container_name: mongo
restart: always
ports:
- "27017:27017"
volumes:
- mongo-data:/data/db



- The database runs on port **27017**, mapped to the host.
- Data is stored persistently in a Docker volume named `mongo-data`.

### Connecting Backend to MongoDB

The backend service uses the MongoDB hostname `mongo` (the service name in Docker Compose) in its connection string:

mongodb://mongo:27017/tutorials



This allows the backend container to communicate directly with the MongoDB container.

### Starting the MongoDB container

To start MongoDB along with backend and frontend, run:

docker-compose up --build

This will pull the official MongoDB image (if not already present), create the container, and start it along with other services.
<img width="1919" height="1023" alt="Screenshot 2025-08-24 132807" src="https://github.com/user-attachments/assets/edd36299-7705-4260-84fb-9c903d6cb38a" />


This setup provides a clean, isolated MongoDB database 
---
CI/CD Pipeline Configuration
We use Jenkins to implement the CI/CD pipeline that automates:

Building updated Docker images whenever code is pushed to GitHub

Pushing these images to Docker Hub

Automatically pulling the latest images and restarting containers on the VM

Pipeline Steps Summary:
Jenkins listens for changes pushed to the GitHub repository.

Builds Docker images for backend and frontend.

Logs in to Docker Hub securely using Jenkins credentials.

Pushes the Docker images to the Docker Hub repository.

SSHs into the VM to pull the latest images and restart Docker containers using:
docker-compose pull
docker-compose up -d



---
### Nginx Reverse Proxy Setup
To make the entire application accessible on port 80, we setup Nginx as a reverse proxy on the VM with the following basic configuration:

Install Nginx on the VM:


sudo apt update
sudo apt install nginx -y
<img width="1906" height="981" alt="Screenshot 2025-08-24 171524" src="https://github.com/user-attachments/assets/ce5100f5-729d-4def-beff-ef01480d3e2b" />

Configure Nginx to proxy requests to the frontend container running on port 8080.

Example Nginx server block (e.g., /etc/nginx/sites-available/mean-app):

server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://localhost:80;  # Frontend service port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /api/ {
        proxy_pass http://localhost:3000; # Backend service port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

Enable the site and reload Nginx:


sudo ln -s /etc/nginx/sites-available/mean-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
<img width="1658" height="989" alt="Screenshot 2025-08-24 171837" src="https://github.com/user-attachments/assets/9ed558aa-300e-43db-97f9-5020516e4250" />
<img width="1907" height="950" alt="Screenshot 2025-08-24 132225" src="https://github.com/user-attachments/assets/594c3af2-9b88-4f84-ab3d-02c6d93d1261" />



These steps ensure your app is available at the VM's IP on port 80, forwarding requests to the frontend service running in the Docker container.

---
