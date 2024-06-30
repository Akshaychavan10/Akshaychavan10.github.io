---
title: 10. HTTP in Details 
date: 2023-12-19 11.11.11 +/-TTTT
categories: [Notes, Pre Security]
tags: [header, request, response, http ]    
img_path: /assets/img/notes/
---

## What is http and https

HTTP stands for Hypertext Transfer Protocol. It is an application protocol that allows the transfer of hypermedia documents, such as HTML, between clients and servers. HTTP operates as a request-response protocol in the client-server computing model.

HTTPS stands for Hypertext Transfer Protocol Secure. It is an extension of HTTP and is used for secure communication over a computer network. HTTPS encrypts the data exchanged between the client and the server using a protocol called SSL/TLS (Secure Sockets Layer/Transport Layer Security).

## HTTP

- [HTTP](https://tools.ietf.org/html/rfc2616) is an application-level protocol used to access the World Wide Web resources.
- The term `hypertext` stands for text containing links to other resources and text that the readers can easily interpret.
- HTTP communication consists of a client and a server, where the client requests the server for a resource.
- The server processes the requests and returns the requested resource. The default port for HTTP communication is port `80`, though this can be changed to any other port.

#### URL

- Resrouces over HTTP are accessed via a URL.

![url_structure](https://academy.hackthebox.com/storage/modules/35/url_structure.png)

|**Component**|**Example**|**Description**|
|---|---|---|
|`Scheme`|`http://` `https://`|This is used to identify the protocol being accessed by the client, and ends with a colon and a double slash (`://`)|
|`User Info`|`admin:password@`|This is an optional component that contains the credentials (separated by a colon `:`) used to authenticate to the host, and is separated from the host with an at sign (`@`)|
|`Host`|`inlanefreight.com`|The host signifies the resource location. This can be a hostname or an IP address|
|`Port`|`:80`|The `Port` is separated from the `Host` by a colon (`:`). If no port is specified, `http` schemes default to port `80` and `https` default to port `443`|
|`Path`|`/dashboard.php`|This points to the resource being accessed, which can be a file or a folder. If there is no path specified, the server returns the default index (e.g. `index.html`).|
|`Query String`|`?login=true`|The query string starts with a question mark (`?`), and consists of a parameter (e.g. `login`) and a value (e.g. `true`). Multiple parameters can be separated by an ampersand (`&`).|
|`Fragments`|`#status`|Fragments are processed by the browsers on the client-side to locate sections within the primary resource (e.g. a header or section on the page).|
