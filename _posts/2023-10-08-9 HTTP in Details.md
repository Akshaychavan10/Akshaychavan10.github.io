---
title: 9 HTTP in Details
date: 2023-10-08 11:11:11 +/-TTTT
categories: [Notes, PreSecurity]
tags: [http, https] 
author: 1  
description: HTTP enables data exchange between a client and server without encryption, while HTTPS adds SSL/TLS encryption for secure communication.    
---

## What is http and https

HTTP stands for Hypertext Transfer Protocol. It is an application protocol that allows the transfer of hypermedia documents, such as HTML, between clients and servers. HTTP operates as a request-response protocol in the client-server computing model.

HTTPS stands for Hypertext Transfer Protocol Secure. It is an extension of HTTP and is used for secure communication over a computer network. HTTPS encrypts the data exchanged between the client and the server using a protocol called SSL/TLS (Secure Sockets Layer/Transport Layer Security).

## HTTP

- [HTTP](https://tools.ietf.org/html/rfc2616) is an application-level protocol used to access the World Wide Web resources.
- The term `hypertext` stands for text containing links to other resources and text that the readers can easily interpret.
- HTTP communication consists of a client and a server, where the client requests the server for a resource.
- The server processes the requests and returns the requested resource. The default port for HTTP communication is port `80`, though this can be changed to any other port.

### URL

- Resrouces over HTTP are accessed via a URL.

![url](https://academy.hackthebox.com/storage/modules/35/url_structure.png)

**url_structure**

| **Component** | **Example** | **Description** |
| --- | --- | --- |
| `Scheme` | `http://` `https://` | This is used to identify the protocol being accessed by the client, and ends with a colon and a double slash (`://`) |
| `User Info` | `admin:password@` | This is an optional component that contains the credentials (separated by a colon `:`) used to authenticate to the host, and is separated from the host with an at sign (`@`) |
| `Host` | `inlanefreight.com` | The host signifies the resource location. This can be a hostname or an IP address |
| `Port` | `:80` | The `Port` is separated from the `Host` by a colon (`:`). If no port is specified, `http` schemes default to port `80` and `https` default to port `443` |
| `Path` | `/dashboard.php` | This points to the resource being accessed, which can be a file or a folder. If there is no path specified, the server returns the default index (e.g. `index.html`). |
| `Query String` | `?login=true` | The query string starts with a question mark (`?`), and consists of a parameter (e.g. `login`) and a value (e.g. `true`). Multiple parameters can be separated by an ampersand (`&`). |
| `Fragments` | `#status` | Fragments are processed by the browsers on the client-side to locate sections within the primary resource (e.g. a header or section on the page). |

### HTTP Flow

![http flow](https://academy.hackthebox.com/storage/modules/35/HTTP_Flow.png)

### cURL

- [cURL](https://curl.haxx.se/) (client URL) is a command-line tool and library that primarily supports HTTP along with many other protocols.
- This makes it a good candidate for scripts as well as automation, making it essential for sending various types of web requests from the command line, which is necessary for many types of web penetration tests.

```
cyber7ron@htb[/htb]$ curl inlanefreight.com

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

```bash
curl -o google.com/index.html #wonload index.html file
```

---

## HTTPS

- communicate in an encrypted format.

**Note:** Although the data transferred through the HTTPS protocol may be encrypted, the request may still reveal the visited URL if it contacted a clear-text DNS server. For this reason, it is recommended to utilize encrypted DNS servers (e.g. 8.8.8.8 or 1.1.1.1), or utilize a VPN service to ensure all traffic is properly encrypted.

### HTTPS Flow

![https flow](https://academy.hackthebox.com/storage/modules/35/HTTPS_Flow.png)

HTTPS_Flow :

- the client (web browser) sends a “client hello” packet, giving information about itself.
- After this, the server replies with “server hello”, followed by a [key exchange](https://en.wikipedia.org/wiki/Key_exchange) to exchange SSL certificates.
- The client verifies the key/certificate and sends one of its own.
- After this, an encrypted [handshake](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake) is initiated to confirm whether the encryption and transfer are working correctly.
- Once the handshake completes successfully, normal HTTP communication is continued, which is encrypted after that.

### cURL for HTTPS

- cURL automatically handles all HTTPS communication standards and perform a secure handshake and then encrypt and decrypt data automatically.
- if the ssl cert is invalid curl by default not proceed with the communications.

---

## HTTP Request and Response

### HTTP Request

![request and response](https://academy.hackthebox.com/storage/modules/35/raw_request.png)

raw_request

### HTTP Response

![http response](https://academy.hackthebox.com/storage/modules/35/raw_response.png)

raw_response

- we can use `cURL` to get request and response information

```bash
curl google.com -v
```

---

## HTTP Headers

Headers can have one or multiple values, appended after the header name and separated by a colon. We can divide headers into the following categories:

1. `General Headers`
2. `Entity Headers`
3. `Request Headers`
4. `Response Headers`
5. `Security Headers`

### General Header

- used in both HTTP requests and responses. They are contextual and are used to `describe the message rather than its contents`.

| **Header** | **Example** | **Description** |
| --- | --- | --- |
| `Date` | `Date: Wed, 16 Feb 2022 10:38:44 GMT` | Holds the date and time at which the message originated. It’s preferred to convert the time to the standard [UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) time zone. |
| `Connection` | `Connection: close` | Dictates if the current network connection should stay alive after the request finishes. Two commonly used values for this header are `close` and `keep-alive`. The `close` value from either the client or server means that they would like to terminate the connection, while the `keep-alive` header indicates that the connection should remain open to receive more data and input. |

### entity headers

- Similar to general headers, [Entity Headers](https://www.w3.org/Protocols/rfc2616/rfc2616-sec7.html) can be `common to both the request and response`.
- These headers are used to `describe the content` (entity) transferred by a message.

| **Header** | **Example** | **Description** |
| --- | --- | --- |
| `Content-Type` | `Content-Type: text/html` | Used to describe the type of resource being transferred. The value is automatically added by the browsers on the client-side and returned in the server response. The `charset` field denotes the encoding standard, such as [UTF-8](https://en.wikipedia.org/wiki/UTF-8). |
| `Media-Type` | `Media-Type: application/pdf` | The `media-type` is similar to `Content-Type`, and describes the data being transferred. This header can play a crucial role in making the server interpret our input. The `charset` field may also be used with this header. |
| `Boundary` | `boundary="b4e4fbd93540"` | Acts as a marker to separate content when there is more than one in the same message. For example, within a form data, this boundary gets used as `--b4e4fbd93540` to separate different parts of the form. |
| `Content-Length` | `Content-Length: 385` | Holds the size of the entity being passed. This header is necessary as the server uses it to read data from the message body, and is automatically generated by the browser and tools like cURL. |
| `Content-Encoding` | `Content-Encoding: gzip` | Data can undergo multiple transformations before being passed. For example, large amounts of data can be compressed to reduce the message size. The type of encoding being used should be specified using the `Content-Encoding` header. |

### Request Header

- The client sends [Request Headers](https://tools.ietf.org/html/rfc2616) in an HTTP transaction.
- These headers are `used in an HTTP request and do not relate to the content` of the message.
- The following headers are commonly seen in HTTP requests.

| **Header** | **Example** | **Description** |
| --- | --- | --- |
| `Host` | `Host: www.inlanefreight.com` | Used to specify the host being queried for the resource. This can be a domain name or an IP address. HTTP servers can be configured to host different websites, which are revealed based on the hostname. This makes the host header an important enumeration target, as it can indicate the existence of other hosts on the target server. |
| `User-Agent` | `User-Agent: curl/7.77.0` | The `User-Agent` header is used to describe the client requesting resources. This header can reveal a lot about the client, such as the browser, its version, and the operating system. |
| `Referer` | `Referer: http://www.inlanefreight.com/` | Denotes where the current request is coming from. For example, clicking a link from Google search results would make `https://google.com` the referer. Trusting this header can be dangerous as it can be easily manipulated, leading to unintended consequences. |
| `Accept` | `Accept: */*` | The `Accept` header describes which media types the client can understand. It can contain multiple media types separated by commas. The `*/*` value signifies that all media types are accepted. |
| `Cookie` | `Cookie: PHPSESSID=b4e4fbd93540` | Contains cookie-value pairs in the format `name=value`. A [cookie](https://en.wikipedia.org/wiki/HTTP_cookie) is a piece of data stored on the client-side and on the server, which acts as an identifier. These are passed to the server per request, thus maintaining the client’s access. Cookies can also serve other purposes, such as saving user preferences or session tracking. There can be multiple cookies in a single header separated by a semi-colon. |
| `Authorization` | `Authorization: BASIC cGFzc3dvcmQK` | Another method for the server to identify clients. After successful authentication, the server returns a token unique to the client. Unlike cookies, tokens are stored only on the client-side and retrieved by the server per request. There are multiple types of authentication types based on the webserver and application type used. |

A complete list of request headers and their usage can be found [here](https://tools.ietf.org/html/rfc7231#section-5).

### Response Headers

- [Response Headers](https://tools.ietf.org/html/rfc7231#section-6) can be `used in an HTTP response and do not relate to the content`

| **Header** | **Example** | **Description** |
| --- | --- | --- |
| `Server` | `Server: Apache/2.2.14 (Win32)` | Contains information about the HTTP server, which processed the request. It can be used to gain information about the server, such as its version, and enumerate it further. |
| `Set-Cookie` | `Set-Cookie: PHPSESSID=b4e4fbd93540` | Contains the cookies needed for client identification. Browsers parse the cookies and store them for future requests. This header follows the same format as the `Cookie` request header. |
| `WWW-Authenticate` | `WWW-Authenticate: BASIC realm="localhost"` | Notifies the client about the type of authentication required to access the requested resource. |

### Security Headers

- HTTP Security headers are `a class of response headers used to specify certain rules and policies` to be followed by the browser while accessing the website.

| **Header** | **Example** | **Description** |
| --- | --- | --- |
| `Content-Security-Policy` | `Content-Security-Policy: script-src 'self'` | Dictates the website’s policy towards externally injected resources. This could be JavaScript code as well as script resources. This header instructs the browser to accept resources only from certain trusted domains, hence preventing attacks such as [Cross-site scripting (XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting). |
| `Strict-Transport-Security` | `Strict-Transport-Security: max-age=31536000` | Prevents the browser from accessing the website over the plaintext HTTP protocol, and forces all communication to be carried over the secure HTTPS protocol. This prevents attackers from sniffing web traffic and accessing protected information such as passwords or other sensitive data. |
| `Referrer-Policy` | `Referrer-Policy: origin` | Dictates whether the browser should include the value specified via the `Referer` header or not. It can help in avoiding disclosing sensitive URLs and information while browsing the website. |