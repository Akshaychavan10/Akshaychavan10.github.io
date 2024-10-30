---
title: 11 Putting It All Together
date: 2023-10-06 11:11:11 +/-TTTT
categories: [Notes, PreSecurity]
tags: [load balancer,server, vhost] 
author: 1  
description: Other Componenet of website.      
---

## Other Component

### Load Balancer

- Load balancers are essential when website traffic becomes large or when high availability is required for an application.
- They provide two main features: handling high traffic loads and providing failover if a server becomes unresponsive.
- When a request is made to a website with a load balancer, the load balancer receives the request first and forwards it to one of the multiple servers behind it.
- Different algorithms, like round-robin or weighted distribution, help the load balancer decide which server is best to handle the request.
- Load balancers perform periodic health checks with each server to ensure they are running correctly.
- If a server fails to respond appropriately, the load balancer stops sending traffic to it until it responds appropriately again.
![loadbalancer](/assets/img/notes/presecurity/loadbalancer.png)

### CDN
- It allows you to host static files from your website, such a JavaScript, CSS, Images, Videos, and host them across thousands of servers all over the world.
- When a user requests one of the hosted files, the CDN works out where the nearest server is physically located and sends the request there instead of potentially the other side of the world.

### WAF
A WAF sits between your web request and the web server; its primary purpose is to protect the webserver from hacking or denial of service attacks. It analyses the web requests for common attack techniques, whether the request is from a real browser rather than a bot.

## How Web Servers Works

- A web server is software that listens for incoming connections and delivers web content using the HTTP protocol.
- Common web server software includes Apache, Nginx, IIS, and NodeJS. Web servers serve files from a root directory defined in their settings.
- For example, Nginx and Apache use `/var/www/html` in Linux, while IIS uses `C:in Windows`. When a file like `http://www.example.com/picture.jpg` is requested, the web server sends the corresponding file from its root directory.

### Virtual Host

- Web servers can host multiple websites with different domain names using virtual hosts.
- These hosts are configured through text-based configuration files. When a request is received, the web server software checks the hostname from the HTTP headers against its virtual hosts.
- If a match is found, the corresponding website is provided; otherwise, the default website is served.
- Virtual hosts can have their root directory mapped to different locations on the hard drive, enabling flexibility in hosting multiple websites. There’s no limit to the number of different websites that can be hosted on a web server.

### Static vs Dynamic

Static content refers to web content that remains unchanged over time, such as images, JavaScript, CSS, and unchanging HTML files. These files are directly served from the web server without any alterations.

Dynamic content, in contrast, can change based on different requests. For instance, on a blog homepage, the latest entries are displayed, and when a new entry is added, the homepage updates accordingly. Dynamic content is generated in the backend using programming and scripting languages, which operate behind the scenes and are not visible in the HTML source. The frontend, what users see in their browsers, displays the results of backend processing.