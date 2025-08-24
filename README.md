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
- [Step-by-step Setup & Deployment](#step-by-step-setup--deployment)
- [Screenshots](#screenshots)
- [Files Included](#files-included)
- [License](#license)

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

text

3. **Add all project files to the repository**  
git add .

text

4. **Commit the added files**  
git commit -m "Initial commit: Add complete project code"

text

5. **Add the remote GitHub repository URL**  
git remote add origin https://github.com/kikitha13/Devops-project.git

text

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

text

3. **Push images to Docker Hub**  
docker push kikitha/mean-backend:latest ./backend
docker push kikitha/mean-frontend:latest ./frontend

---


### Set Up an Ubuntu Virtual Machine

- Create a new Ubuntu VM instance on your chosen cloud platform -AWS  
- Access the VM via SSH.  
- Install Docker and Docker Compose on the VM (see [Docker installation guides](https://docs.docker.com/engine/install/ubuntu/) and [Docker Compose installation](https://docs.docker.com/compose/install/)).

### Deploy Application on the VM Using Docker Compose

1. Copy the project's `docker-compose.yml` file and any necessary files to the Ubuntu VM.  
2. Run the following command to start the containers:  
docker-compose up -d



Docker Compose will pull the images from Docker Hub and start the frontend, backend, and database containers as specified.

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

bash
docker-compose pull
docker-compose up -d
Nginx Reverse Proxy Setup
To make the entire application accessible on port 80, we setup Nginx as a reverse proxy on the VM with the following basic configuration:

Install Nginx on the VM:

bash
sudo apt update
sudo apt install nginx -y
Configure Nginx to proxy requests to the frontend container running on port 8081.

Example Nginx server block (e.g., /etc/nginx/sites-available/mean-app):

text
server {
    listen 80;

    location / {
        proxy_pass http://localhost:8081;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
Enable the site and reload Nginx:

bash
sudo ln -s /etc/nginx/sites-available/mean-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
These steps ensure your app is available at the VM's IP on port 80, forwarding requests to the frontend service running in the Docker container.

---
