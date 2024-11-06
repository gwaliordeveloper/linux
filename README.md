# Running Nginx and Apache on the Same Linux Machine

This guide explains how to run two web applications on a single Linux machine using **Nginx** and **Apache** web servers. We will configure Nginx to run on a different port or domain/subdomain, and Apache to run on another port or domain/subdomain, ensuring no conflicts.

## Prerequisites

- A Linux machine (Ubuntu, Debian, CentOS, or RHEL).
- **root** or **sudo** access to the machine.

---

## 1. Install Nginx and Apache

### Ubuntu/Debian-based Systems

```bash
sudo apt update
sudo apt install nginx apache2

sudo yum install nginx httpd

2. Configure Nginx

By default, Nginx listens on port 80. You need to configure Nginx to run on a different port (e.g., 8080) or use a domain/subdomain.

Option 1: Change Nginx Port to 8080
Edit the Nginx default site configuration file:

sudo nano /etc/nginx/sites-available/default

Update the listen directive to listen on port 8080:
server {
    listen 8080;
    server_name localhost;

    root /var/www/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}


Option 2: Set Up Virtual Hosts for Nginx
Configure Nginx to serve a specific domain or subdomain (e.g., app1.example.com):

server {
    listen 80;
    server_name app1.example.com;

    root /var/www/app1;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}

3. Configure Apache

Apache typically listens on port 80, so we need to make sure Apache runs on a different port (e.g., 8081) to avoid conflicts.

Option 1: Change Apache Port to 8081
Edit Apache's ports.conf to listen on port 8081:

sudo nano /etc/apache2/ports.conf
Listen 8081

Now, modify the Apache default site configuration to use the new port:
sudo nano /etc/apache2/sites-available/000-default.conf

Update the VirtualHost directive:
<VirtualHost *:8081>
    DocumentRoot /var/www/app2
    ServerName app2.example.com

    <Directory /var/www/app2>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

Option 2: Set Up Virtual Hosts for Apache
You can also configure Apache to serve a different domain or subdomain, e.g., app2.example.com.

<VirtualHost *:80>
    ServerName app2.example.com
    DocumentRoot /var/www/app2

    <Directory /var/www/app2>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

4. Enable the Configuration Changes

After configuring Nginx and Apache, make sure the configurations are enabled and both services are restarted.

For Nginx:

sudo nginx -t            # Test the Nginx configuration
sudo systemctl restart nginx  # Restart Nginx

sudo nginx -t            # Test the Nginx configuration
sudo systemctl restart nginx  # Restart Nginx

For Apache:
sudo apache2ctl configtest  # Test the Apache configuration
sudo systemctl restart apache2  # Restart Apache


6. Testing the Setup

Visit http://localhost:8080 (or http://app1.example.com) in your browser to test the Nginx application.
Visit http://localhost:8081 (or http://app2.example.com) to test the Apache application.
If everything is configured correctly, both web servers should be running simultaneously, serving different applications.

7. Optional: Reverse Proxy with Nginx (If Needed)

If you'd like Nginx to handle incoming traffic and proxy requests to Apache for a specific application, you can set up Nginx as a reverse proxy.

In Nginx's configuration:

server {
    listen 80;
    server_name app1.example.com;

    location / {
        proxy_pass http://localhost:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

This will route requests to app1.example.com through Nginx to Apache running on port 8081.

Conclusion

By following this guide, you'll be able to run two web applications on the same machine—one served by Nginx and the other by Apache. This setup allows you to manage multiple web services on the same system without conflicts.


### Explanation of Structure:
- **Headings and Subheadings**: The document is structured with clear headings for each step, making it easy to follow.
- **Code Blocks**: Commands and configuration files are enclosed in code blocks for easy copy-pasting.
- **Testing and Optional Sections**: The guide also includes testing steps and an optional reverse proxy configuration.

You can simply copy and paste the above content into a `README.md` file in your GitHub repository, and it should render properly with proper formatting when viewed on GitHub.

Let me know if you'd like to tweak anything or need further clarification!


