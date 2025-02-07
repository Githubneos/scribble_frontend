---
layout: post
title: Deployment Blog
search_exclude: true
description: Deployment Blog for qualifying AWS account.
permalink: /deploy_v2
Author: Keertha
---

## Welcome to Scribble With Users

<table>
    <tr>
        <td><a href="{{site.baseurl}}/competition">Competitive</a></td>
        <td><a href="{{site.baseurl}}/guess">Guess Game</a></td>
        <td><a href="{{site.baseurl}}/leaderboard">LeaderBoard</a></td>
        <td><a href="{{site.baseurl}}/stats">Statistics</a></td>
        <td><a href="{{site.baseurl}}/about">About Us</a></td>
        <td><a href="{{site.baseurl}}/deploy">Deploy Blog</a></td>
    </tr>
</table>
<br>

Deployment blogs:

## Welcome to deployment blogs
<table>
    <tr>
        <td><a href="{{site.baseurl}}/deploy">Deploy 1</a></td>
        <td><a href="{{site.baseurl}}/deploy_v2">Deploy 2</a></td>
    </tr>
</table>

## Deployment Prerequisites
 - Create burndown List Issue: https://github.com/Githubneos/scribble_frontend/issues/21

 - Prepare Config files for deployment:

   - main.py: 8087 to 8203

```python
if __name__ == "__main__":
    init_db()
    app.run(debug=True, host="0.0.0.0", port="8203")
```

- Dockerfile 
```dockerfile
FROM docker.io/python:3.11

  WORKDIR /

  # --- [Install python and pip] ---
  RUN apt-get update && apt-get upgrade -y && \
      apt-get install -y python3 python3-pip git
  COPY . /

  RUN pip install --no-cache-dir -r requirements.txt
  RUN pip install gunicorn

  ENV GUNICORN_CMD_ARGS="--workers=1 --bind=0.0.0.0:8203"

  EXPOSE 8209

  # Define environment variable
  ENV FLASK_ENV=deployment

  CMD [ "gunicorn", "main:app" ]
```
- docker-compose.yml
```yaml
version: '3'
services:
         web:
                 image: scribble_2025
                 build: .
                 env_file:
                         - .env
                 ports:
                         - "8203:8203"
                volumes:
                         - ./instance:/instance
                restart: unless-stopped
```

```nginx
- nginx file
  server {
      listen 80;
      listen [::]:80;
      server_name scribble.stu.nighthawkcodingsociety.com;

      location / {
          proxy_pass http://localhost:8203;

          # Preflighted requests
          if ($request_method = OPTIONS) {
              add_header "Access-Control-Allow-Credentials" "true" always;
              add_header "Access-Control-Allow-Origin"  "https://Githubneos.github.io" always;
              add_header "Access-Control-Allow-Methods" "GET, POST, PUT, DELETE, OPTIONS, HEAD" always;
              add_header "Access-Control-Allow-MaxAge" 600 always;
              add_header "Access-Control-Allow-Headers" "Authorization, Origin, X-Origin, X-Requested-With, Content-Type, Accept" always;
              return 204;
          }
      }
  }
```

## Deployment Process

### Create DNS record

```markdown
- To create a DNS record, you first have to log into [AWS](https://aws.amazon.com/ec2/)

- Account Details:
    - Account ID: nighthawkcodingsociety
    - IAM username: ubuntu
    - Password:  Ubuntu14*&*41


- Once signed in, you should click the button on the home page titled **Route 53**

- The next step (when on the Route 53 page) is to press the **Hosted zones** section on the column to the left.

- Here, click on the hosted zone called stu.nighthawkcodingsociety.com

- We have made it to the page where we can create a DNS record. Click the button that says Create record

- When creating a record you need to put in some information. Here is the following information that should be inputted:
    
    - Record name: name of your project (ex: cantella)
- Record type: A - Routes traffic to an IPv4 address and some AWS resources
- Value: 3.129.109.200
- TTL (seconds): 300
- Routing policy: Simple routing

- Once all categories have been filled, click the Create record button on the bottom right.

- If successful, you should receive a notification that a new record was successfully created
```
After this has been completed, you will have successfully created a DNS record.

### AWS Setup

```markdown
- First, login into the [AWS Terminal](https://cockpit.stu.nighthawkcodingsociety.com/system/terminal)

- Now run the following commands:
    1. Change into the correct directory: cd ~
    2. Clone your backend repo: git clone https://github.com/Githubneos/scribble_backend.git
    3. Navigate into your repo: cd scribble_backend
    4. Build the site: docker-compose up -d --build
    5. Test your site: curl localhost:8203
```

### Nginx Setup
1. Change into the correct directory: cd ~

2. Navigate into your repo: cd scribble_backend

3. Copy paste your nginx file into /etc/nginx/sites-available: sudo cp scribble_nginx_file /etc/nginx/sites-available/.

4. Change back into ~ directory: cd ~

5. Navigate to sites-enabled: cd /etc/nginx/sites-enabled

6. Create a symbolic link (shortcut) to the nginx file in sites-available: sudo ln -s ../sites-available/scribble_nginx_file

7. Test to see if there is any syntax errors: sudo nginx -t

8. Restart nginx: sudo systemctl restart nginx

9. Test domain name on a browser (http:// only)

### (4) Certbot Config
1. Run sudo certbot –nginx