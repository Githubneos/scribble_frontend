---
layout: post
title: Deployment Blog
search_exclude: true
description: Deployment Blog for qualifying AWS account.
permalink: /deploy
Author: Ian
---

# Welcome to Scribble With Users

<table>
    <tr>
        <td><a href="{{site.baseurl}}/index">Home</a></td>
        <td><a href="{{site.baseurl}}/competition">Competitive</a></td>
        <td><a href="{{site.baseurl}}/guess">Guess Game</a></td>
        <td><a href="{{site.baseurl}}/leaderboard">LeaderBoard</a></td>
        <td><a href="{{site.baseurl}}/stats">Statistics</a></td>
        <td><a href="{{site.baseurl}}/about">About Us</a></td>
        <td><a href="{{site.baseurl}}/deploy">Deploy Blog</a></td>
        <td><a href="{{site.baseurl}}/posts">Posts</a></td>
    </tr>
</table>
<br>
<hr>

<style>
    body {
        background: linear-gradient(145deg, rgb(50, 131, 40), rgb(164, 219, 43), rgb(15, 96, 107), #8D77AB);
        color: white;
    }
</style>

## Welcome to Deployment Blogs

<table>
    <tr>
        <td><a href="{{site.baseurl}}/deploy">Deploy 1</a></td>
        <td><a href="{{site.baseurl}}/deploy_v2">Deploy 2</a></td>
    </tr>
</table>

## Step 0: Ensure Docker is installed:
Install Docker:
- https://docs.docker.com/get-docker/

## Step 1: Accessing AWS EC2
Log in to the AWS Management Console  
Navigate to EC2 > Instances  
Launch a new EC2 instance using Ubuntu as the base image  
Connect to the instance using SSH:

```sh
# Connect to your EC2 instance using SSH
ssh -i something-key.pem ubuntu@something-aws-instance-ip #ex aws instance ip: 3.129.109.200
```

## Step 2: Setting Up Application

### 1. Finding an Available Port
Run the following command in the EC2 terminal to check for available ports:

```sh
# List running Docker containers to check for available ports
docker ps
```

Our class port is 802_, scribble's port goes to 8023

### 2. Setting Up Docker on Localhost
Ensure Docker configuration files match the deployment environment:

If `docker.io/python` doesn't work, make sure the versions match up

#### Terminal
```python
# Check the Python version installed
python --version
```

#### Dockerfile
```dockerfile
# Use Python 3.11 as the base image
FROM docker.io/python:3.11

# Set the working directory
WORKDIR /

# Install necessary packages and dependencies
RUN apt-get update && apt-get install -y python3 python3-pip git
COPY . /

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt
RUN pip install gunicorn

# Set Gunicorn environment variables
ENV GUNICORN_CMD_ARGS="--workers=1 --bind=0.0.0.0:8023"

# Expose port 8023
EXPOSE 8023

# Start the application using Gunicorn
CMD [ "gunicorn", "main:app" ]
```

#### docker-compose.yml
```yaml
version: '3'
services:
  web:
    image: scribble_2025
    build: .
    env_file:
      - .env
    ports:
      - "8023:8023"
    volumes:
      - ./instance:/instance
    restart: unless-stopped
```

#### .env File Setup:
In the root of the directory, make an .env and adjust environment keys.

```env
# Environment variables for the application
PORT=8203
DATABASE_URL=<your-database-url>  # URL for Database
SECRET_KEY=<your-secret-key>      # Secret Key for Authorization
```

.env files created are hidden for privacy, ensure that environment file isn't shared, especially secret_key.

### Frontend Configuration
Update `config.js` to ensure the frontend communicates with the backend:

```javascript
// Configure the backend URI based on the environment
export var pythonURI;
if (location.hostname === "localhost" || location.hostname === "127.0.0.1") { //Main domain
    pythonURI = "http://localhost:8023"; //Our port (8023)
} else {
    pythonURI = "https://scribble_backend.stu.nighthawkcodingsociety.com";
}
```

- If localhost works on the backend but not the frontend, always check `pythonURI` before continuing.

## Step 3: Server Setup

### 1. Clone Backend Repository

Ensure repos don't have an uppercase for formatting purposes.
```sh
# Clone the backend repository and navigate into it
cd ~
git clone https://github.com/scribble_backend.git scribble_backend
cd scribble_backend
```

### 2. Build and Run the Docker Container
```sh
# Build and start the Docker container
docker-compose up -d --build
```

### Verify deployment with:
```sh
# Verify the deployment by making a request to the backend
curl localhost:8023
```

## Step 4: Configuring DNS with Route 53
Go to AWS Route 53  
Select hosted zone and add a new CNAME record. 
- Our name is scribble
- Our value is scribble.stu.nighthawkcodingsociety.com

| Name      | Type  | Value                                |
|-----------|-------|--------------------------------------|
| scribble  | CNAME | scribble.stu.nighthawkcodingsociety.com |

## Step 5: Setting Up Nginx as a Reverse Proxy
thie
Create an Nginx configuration file:

```sh
# Create a new Nginx configuration file for the backend
sudo nano /etc/nginx/sites-available/scribble_backend
```

### Add the following configuration:
```nginx
server {
    listen 80;
    listen [::]:80;
    server_name scribble.stu.nighthawkcodingsociety.com;

    location / {
        proxy_pass http://localhost:8203;
        
        if ($request_method = OPTIONS) {
            add_header "Access-Control-Allow-Credentials" "true" always;
            add_header "Access-Control-Allow-Origin" "https://nighthawkcoders.github.io" always;
            add_header "Access-Control-Allow-Methods" "GET, POST, PUT, DELETE, OPTIONS, HEAD" always;
            add_header "Access-Control-Allow-Headers" "Authorization, Content-Type, Accept" always;
            return 204;
        }
    }
}
```

### Enable the configuration and restart Nginx:
```sh
# Enable the new Nginx configuration and restart the service
sudo ln -s /etc/nginx/sites-available/scribble_backend /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## Step 6: Enabling SSL with Certbot
Install Certbot and configure HTTPS:

```sh
# Install Certbot and the Nginx plugin
sudo apt update && sudo apt install certbot python3-certbot-nginx -y
# Run Certbot to configure SSL
sudo certbot --nginx
```
Follow the prompts to select the domain and enable HTTPS.

## Step 7: Deployment Updates
Whenever updating code, do the following:

### Pull the latest changes:
```sh
# Pull the latest changes from the repository
cd ~/scribble_backend
git pull
```

### Restart the container:
```sh
# Restart the Docker container to apply changes
docker-compose down
docker-compose up -d --build
```

### Verify the deployment:
```sh
# Verify the deployment by making a request to the backend
curl localhost:8023
```

## Step 8: Troubleshooting and Monitoring
Troubleshoot Docker configurations. 
- Check if deploy works. 
- Be careful of sudo commands unless mort allows it.

### Basic Checks
Check running Docker containers:
```sh
# List running Docker containers
docker ps
```

### Check application logs:
```sh
# View logs for the Docker containers
docker-compose logs
```

### Check Nginx errors:
```sh
# View the last 20 lines of the Nginx error log
sudo journalctl -u nginx --no-pager | tail -n 20
```

## Using Cockpit for Server Management
Log into Cockpit using subdomain.  
Navigate to:

- **Overview** – System health and status.
- **Logs** – Server activity and errors.
- **Networking** – View active network settings.
- **Terminal** – Run administrative commands.

<script>
function showMessage(message, type) {
    const messageEl = document.getElementById('message');
    messageEl.textContent = message;
    messageEl.style.display = 'block';
    messageEl.style.backgroundColor = type === 'error' ? '#F8FAFC' : '#D9EAFD';
    messageEl.style.color = type === 'error' ? '#BCCCDC' : '#9AA6B2';
    setTimeout(() => messageEl.style.display = 'none', 3000);
}
</script>
