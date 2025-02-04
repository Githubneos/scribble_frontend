---
layout: post
title: Deployment Blog
search_exclude: true
description: Deployment Blog for qualifying AWS account.
permalink: /deploy
Author: Ian
---

**Step 1: Accessing AWS EC2**
Log in to the AWS Management Console
Navigate to EC2 > Instances
Launch a new EC2 instance using Ubuntu as the base image
Connect to your instance using SSH:

ssh -i your-key.pem ubuntu@your-aws-instance-ip

**Step 2: Setting Up Your Application**

**1. Finding an Available Port**
Run the following command in your EC2 terminal to check for available ports:

docker ps

Select an unused port starting in the 8000–8999 range (using 8887)


**2. Setting Up Docker on Localhost**
Ensure your Docker configuration files match the deployment environment:

FROM docker.io/python:3.11

WORKDIR /

RUN apt-get update && apt-get install -y python3 python3-pip git
COPY . /

RUN pip install --no-cache-dir -r requirements.txt
RUN pip install gunicorn

ENV GUNICORN_CMD_ARGS="--workers=1 --bind=0.0.0.0:8887"

EXPOSE 8087

CMD [ "gunicorn", "main:app" ]
docker-compose.yml
yml
Copy
Edit
version: '3'
services:
  web:
    image: scribble2025
    build: .
    env_file:
      - .env
    ports:
      - "8887:8887"
    volumes:
      - ./instance:/instance
    restart: unless-stopped

**Frontend Configuration**
Update config.js to ensure the frontend communicates with the backend:

export var pythonURI;
if (location.hostname === "localhost" || location.hostname === "127.0.0.1") {
    pythonURI = "http://localhost:8887";
} else {
    pythonURI = "https://flask2025.nighthawkcodingsociety.com";
}


**Step 3: Server Setup**

**1. Clone Your Backend Repository**
cd ~
git clone https://github.com/your-repo/backend.git Scribble_backend
cd Scribble_backend

**2. Build and Run the Docker Container**
docker-compose up -d --build

**Verify deployment with:**
curl localhost:8887

**Step 4: Configuring DNS with Route 53**
Go to AWS Route 53
Select your hosted zone and add a new CNAME record:
Name	Type	Value
flask2025	CNAME	csp.nighthawkcodingsociety.com

**Step 5: Setting Up Nginx as a Reverse Proxy**
Create an Nginx configuration file

sudo nano /etc/nginx/sites-available/Scribble_backend

**Add the following configuration:**
server {
    listen 80;
    listen [::]:80;
    server_name scribble.nighthawkcodingsociety.com;

    location / {
        proxy_pass http://localhost:8887;
        
        if ($request_method = OPTIONS) {
            add_header "Access-Control-Allow-Credentials" "true" always;
            add_header "Access-Control-Allow-Origin" "https://nighthawkcoders.github.io" always;
            add_header "Access-Control-Allow-Methods" "GET, POST, PUT, DELETE, OPTIONS, HEAD" always;
            add_header "Access-Control-Allow-Headers" "Authorization, Content-Type, Accept" always;
            return 204;
        }
    }
}

**Enable the configuration and restart Nginx:**

sudo ln -s /etc/nginx/sites-available/flask_backend /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx

**Step 6: Enabling SSL with Certbot**
Install Certbot and configure HTTPS:

sudo apt update && sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx
Follow the prompts to select your domain and enable HTTPS.

**Step 7: Deployment Updates**
Whenever you update your code:
Pull the latest changes:
cd ~/Scribble_backend
git pull

**Restart the container:**
docker-compose down
docker-compose up -d --build

**Verify the deployment:**
curl localhost:8887

**Step 8: Troubleshooting and Monitoring**
Basic Checks
Check running Docker containers:
docker ps

**Check application logs:**
docker-compose logs

**Check Nginx errors:**
sudo journalctl -u nginx --no-pager | tail -n 20


**Using Cockpit for Server Management**
Log into Cockpit using your subdomain.
Navigate to:
Overview – System health and status
Logs – Server activity and errors
Networking – View active network settings
Terminal – Run administrative commands
