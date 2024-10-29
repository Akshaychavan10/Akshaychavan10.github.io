---
title: 10 How Website Works
date: 2023-10-07 11:11:11 +/-TTTT
categories: [Notes, PreSecurity]
tags: [frontend, backend, html injection] 
author: 1  
description: A website consists of the Front End (user interface) and Back End (server processing), primarily built using HTML (structure), CSS (styling), and JavaScript (interactivity), with potential security vulnerabilities like Sensitive Data Exposure (improperly protected data) and HTML Injection (unsanitized user input).     
---

## Components

- When visiting a website, your browser (e.g., Safari, Google Chrome) sends a request to a web server, asking for information about the page you’re viewing. The server then responds with data that your browser uses to display the page. Essentially, a web server is a dedicated computer located elsewhere in the world that handles these requests.
- Two primary components comprise a website:
    1. Front End (Client-Side): This is how your browser interprets and renders the website. It involves the presentation layer visible to users, including layout, design, and interactivity.
    2. Back End (Server-Side): This refers to the server responsible for processing your request and generating a response. It handles data processing, logic, and interactions with databases or external services.
- Websites are primarily created using:
- HTML, to build websites and define their structure
- CSS, to make websites look pretty by adding styling options
- JavaScript, implement complex features on pages using interactivity

## HTML

- HTML stands for Hypertext Markup Language. It is the standard markup language used to create and design web pages.
- HTML provides a structure for web content by using a system of tags or elements to define various elements such as headings, paragraphs, links, images, and more.
- Tags in HTML are enclosed in angle brackets (< >) and typically come in pairs, with an opening tag and a closing tag. The content to be affected by the tag is placed between these tags.
- HTML documents are hierarchical, with a root `<html>` tag enclosing two main sections: `<head>` and `<body>`. The `<head>` section contains metadata and other information about the document, such as the page title and links to external resources, while the `<body>` section contains the actual content of the page.
- HTML supports various attributes within tags, which provide additional information or functionality. Attributes modify the behavior or appearance of elements and are specified within the opening tag.
- HTML is constantly evolving, with new versions and features being introduced regularly. The latest version as of (Current date) is HTML5, which includes enhanced support for multimedia, improved semantics, and better accessibility features.
- HTML is the foundation of web development and is often combined with other technologies such as CSS (Cascading Style Sheets) for styling and JavaScript for interactivity to create modern, dynamic web experiences.

## Javascript

- JavaScript (JS) is a widely used programming language that enhances web pages by making them interactive.
- While HTML creates the structure and content of web pages, JavaScript controls the functionality, enabling interactive elements. Without JavaScript, web pages would remain static.
- JS enables dynamic updates to web pages in real-time, allowing for actions like changing button styles upon user interaction or displaying animated content.
- In HTML, JavaScript can be included either inline within `<script> tags or externally by referencing a JavaScript file using the src attribute within <script> tags`.
- Example: `<script src="/location/of/javascript_file.js"></script>` imports JavaScript from an external file.
- JavaScript code can manipulate HTML elements. For instance, `document.getElementById("demo").innerHTML = "Hack the Planet";` finds an HTML element with the id “demo” and changes its content to “Hack the Planet”.
- HTML elements can have event attributes like “onclick” or “onhover” that trigger JavaScript functions when the event occurs. For example: `<button onclick='document.getElementById("demo").innerHTML = "Button Clicked";'>Click Me!</button>` This button, when clicked, changes the content of the element with the id “demo” to “Button Clicked”.
- Event handlers can also be defined within JavaScript code, not directly on HTML elements, providing flexibility in organizing code and handling events.

### Sensitive Data Exposure

Sensitive Data Exposure is when a website doesn’t properly protect (or remove) sensitive clear-text information to the end-user; usually found in the frontend source code of sites.
![data exposure](/assets/img/notes/presecurity/dataexpo.png)

### HTML Injection

- HTML Injection is a security vulnerability that arises when unfiltered user input is displayed on a web page without proper sanitization.
- If a website fails to sanitize user input, allowing “malicious” text to be displayed on the page, attackers can exploit this vulnerability by injecting HTML code.
- Proper input validation and sanitization techniques, such as filtering out HTML tags and special characters, are essential for preventing HTML injection vulnerabilities and ensuring the security of web applications.