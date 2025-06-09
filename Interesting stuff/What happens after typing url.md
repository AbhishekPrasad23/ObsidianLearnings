
## What happens when you type amazon com or www google com in the browser?

At a high level, the [following operations](https://newsletter.systemdesign.one/p/what-happens-when-you-type-google-com-in-browser) happen in the background when you type a URL into your browser and press Enter:

1. DNS resolution
2. TCP three-way handshake
3. HTTPS upgrade
4. HTTP Request/Response
5. Browser rendering the response from the server

---

---

---

## [](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#terminology)Terminology

The following terminology might be useful for you:

- DNS: data store that contains the mapping from domain name to IP address
- HTTP: standard application-level protocol used to exchange files on the internet
- HTTPS: secure (encrypted) version of HTTP
- TCP: standard that defines how to establish and maintain a network conversation between a client and a server
- Client: web browser or a mobile device that lets you perform different actions on the internet
- Server: a computer that stores files and information in the form of a website
- URL: web address to identify a web resource on the internet

---

---

---

## [](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#introduction)Introduction

When you type a Uniform Resource Locator ([URL](https://en.wikipedia.org/wiki/URL)) into the browser and press Enter, a multitude of actions is executed in the background.

[![HTTP high-level workflow](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/HTTP-high-level-workflow.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/HTTP-high-level-workflow.webp "HTTP high-level workflow")

Figure 1: HTTP high-level workflow

The browser (**client**) must find the Internet protocol ([IP](https://en.wikipedia.org/wiki/IP_address)) address of the server that hosts the website. The client subsequently requests the IP address of the server to transfer or retrieve data. When the client enters a shortened URL (created using a URL shortener) into the browser there is an additional step of URL redirection before transferring data between the client and the server.

---

## What is domain name resolution?

Domain Name System ([DNS](https://en.wikipedia.org/wiki/Domain_Name_System)) is a data store that stores the mapping from a domain name (such as google.com) to its IP address (142.250.185.78). You could compare DNS to an address book or a telephone book[1](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:1).

You need a [domain name](https://en.wikipedia.org/wiki/Domain_name) (instead of using an IP address directly) because it’s trivial to remember domain names for a human. Each server (for example, google.com) should have a unique IP address on the internet. The DNS lets you find the IP address of the specific server on the internet using its domain name.

The domain name space (**DNS hierarchy**) is an inverted tree structure. The DNS hierarchy has a single domain at the top level known as the root domain.

[![DNS hierarchy structure](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/dns-hierarchy.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/dns-hierarchy.webp "DNS hierarchy structure")

Figure 2: DNS hierarchy structure

The DNS resolution starts at the root domain, TLD, and works its way down to the second-level domain. The DNS resolution might traverse through multiple third-level domains until the hostname of the website resolves to an IP address.

A URL consists of the following parts[2](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:2):

- protocol (scheme)
- subdomain
- domain (second-level)
- top-level domain

[![Basic URL structure](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/basic-url-structure.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/basic-url-structure.webp "Basic URL structure")

Figure 3: Basic URL structure

The protocol section of the URL informs the web server which protocol to use on accessing a website. The subdomain indicates the services offered by the website such as maps or mail. The second-level domain indicates the name of the website. The top-level domain indicates the type of entity the organization registers on the internet.

The DNS resolution is a sequential process. The subsequent steps of DNS resolution get executed only if the cache in the former step does not have the relevant DNS entry.

[![How does DNS resolution work?](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/how-does-dns-resolution-work.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/how-does-dns-resolution-work.webp "How does DNS resolution work?")

Figure 4: How does DNS resolution work?

The following operations get executed in sequential order for DNS resolution:

1. The browser (**client**) checks if the hostname to IP address mapping exists in the local cache of the client.
2. If the last step failed, the client checks the Operating System ([OS](https://en.wikipedia.org/wiki/Operating_system)) local cache by executing a system call ([syscall](https://en.wikipedia.org/wiki/System_call#:~:text=In%20computing%2C%20a%20system%20call,on%20which%20it%20is%20executed.)).
3. If the last step failed, the client makes a DNS request to the Gateway/Router and checks the local cache of the Router.
4. If the last step failed, the router forwards the request to Internet Service Provider ([ISP](https://en.wikipedia.org/wiki/Internet_service_provider)) and checks the DNS cache of the ISP.
5. If the last step fails, the DNS [resolver](https://en.wikipedia.org/wiki/Domain_Name_System#DNS_resolvers) queries the root servers (there are 13 root servers with replicas worldwide).
6. DNS resolver queries Top Level Domain (TLD) servers such as .com, or .org.
7. DNS resolver queries Authoritative name servers such as google.com.
8. Optionally, the DNS resolver queries Authoritative subdomain servers such as maps.google.com depending on your query.

[![Recursive DNS lookup](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/recursive-dns-lookup.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/recursive-dns-lookup.webp "Recursive DNS lookup")

Figure 5: Recursive DNS lookup

Once the DNS query is resolved, the intermediate components in the DNS resolution process would cache the result with a Time-to-live (TTL) expiry time limit. If you change the IP address of a website, the client should wait until TTL has elapsed to automatically invalidate the cache. The cache also improves the latency of subsequent client requests.

---

---

---

## [](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#tcp-three-way-handshake)TCP three-way handshake

The client should create a connection to the server to transfer and receive data. Transmission Control Protocol ([TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)) is one of the underlying protocols in Hypertext Transfer Protocol (**HTTP**).

You can compare HTTP to an abstract or a high-level protocol (Application layer or layer 7 in the [OSI](https://en.wikipedia.org/wiki/OSI_model) model) used between the client and the server to transfer data. Data transferred in HTTP is human-readable. TCP is a lower-level protocol (Transport layer or layer 4 in the OSI model) that handles error detection and retransmission of data packets[3](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:3).

The client performs a three-way handshake with the server to establish a TCP connection. TCP requires a three-way handshake because of the bi-directional communication channel. If you make a two-way handshake, you can only start a single-directional communication channel.

[![TCP three-way handshake](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/TCP-three-way-handshake.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/TCP-three-way-handshake.webp "TCP three-way handshake")

Figure 6: TCP three-way handshake

The following synchronize (**SYN**) and acknowledge (**ACK**) messages are sent between the client and the server to open a TCP connection:

1. The client sends an SYN request with a random sequence number (x).
2. The server responds with SYN-ACK. The acknowledgment number is set to one more than the received sequence number (x+1). The server sends another random sequence number (y).
3. The client sends ACK. The client sends an acknowledgment number that is one more than the received sequence number (y+1).

---

---

---

## [](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#https-upgrade)HTTPS Upgrade

Hypertext Transfer Protocol Secure ([HTTPS](https://en.wikipedia.org/wiki/HTTPS)) is an extension of Hypertext Transfer Protocol ([HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)). The data transferred through HTTP is not encrypted and therefore anyone can eavesdrop on the data packets that are transferred. HTTPS encrypts the transferred data and thereby prevents man-in-the-middle attacks[4](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:4). Most of the modern websites on the internet enable HTTPS for secure communication. HTTPS uses port 443 by default while HTTP uses port 80.

In [asymmetric encryption](https://en.wikipedia.org/wiki/Public-key_cryptography), there is a key pair — public and private keys. The public keys can be shared with anyone on the internet while the private key should never be shared. In [symmetric encryption](https://en.wikipedia.org/wiki/Symmetric-key_algorithm), there is only a single private key and both parties should have access to the same key to encrypt or decrypt the message.

You could compare asymmetric encryption to an email service. The public key would be your Email address and the private key would be the password used to access the email account. Anyone can easily verify that you sent the email by looking at the Email address of the sender. However, if anyone sends an Email to your account, only you can access it using the password.

[![HTTPS upgrade request](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/HTTPS-upgrade-request.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/HTTPS-upgrade-request.webp "HTTPS upgrade request")

Figure 7: HTTPS upgrade request

The following operations are executed between the client and server to upgrade HTTP to HTTPS before the transfer of any data packets:

1. The browser (**client**) uses the HTTP Request headers to make an HTTPS upgrade request to the server.
2. The server responds to the client with a Secure Sockets Layer ([SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0,_2.0,_and_3.0)) certificate that contains the public key of the server, which was signed by the Certificate Authority ([CA](https://en.wikipedia.org/wiki/Certificate_authority)).
3. The client by default has the public keys of popular CA (for example, Google CA). The client can use the public keys of the CA to verify the validity of the SSL certificate by checking the digital signature (digital signature in [asymmetric encryption](https://en.wikipedia.org/wiki/Public-key_cryptography)).
4. The client creates a new [symmetric](https://en.wikipedia.org/wiki/Symmetric-key_algorithm) private key and encrypts the new symmetric private key with the public key of the server.
5. The server decrypts the new symmetric private key shared by the client using the server’s private key.
6. Any further communication between the client and the server would be encrypted using the new symmetric private key and therefore remains secure.

Anyone listening to the HTTP Upgrade request or response messages would not be able to capture any meaningful data because of the asymmetric encryption. Once HTTPS is upgraded, further communication between the client and the server utilizes symmetric encryption.

To create an SSL certificate, the server executes a Certificate Signing Request ([CSR](https://en.wikipedia.org/wiki/Certificate_signing_request)) to the CA.

[![SSL Certificate Signing Request](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/SSL-CSR.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/SSL-CSR.webp "SSL Certificate Signing Request")

Figure 8: SSL Certificate Signing Request

The CSR transfers the public key of the server to the CA. The CA has a pair of public and private keys. The CA signs the public key of the server using the private key of the CA and creates an SSL certificate. Anyone on the internet who has access to the public key of the CA could easily verify the digital signature of the SSL certificate.

---

---

---

## [](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#http-requestresponse)HTTP Request/Response

Hypertext Transfer Protocol (**HTTP**) is a mechanism for transporting data between a client and a server. The client issues an HTTP(S) Request to the server to fetch or transfer data. The server responds with relevant content and completion status information about the request[5](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:5), [6](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:6).

[![HTTP simplified workflow](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/HTTP-simplified-workflow.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/HTTP-simplified-workflow.webp "HTTP simplified workflow")

Figure 9: HTTP simplified workflow

The client makes a GET HTTP Request to view a website (such as google.com). The client makes a POST HTTP Request if the client wants to submit data to the server (such as searching for some keyword in google.com).

[![Basic HTTP URL structure](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/basic-url-structure.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/basic-url-structure.webp "Basic HTTP URL structure")

Figure 10: Basic HTTP URL structure

The URL can be broken down into multiple components. The domain name is used to identify the server. The URL path is used to identify the resource (a specific file or information) on the server. The query parameters are used to filter or sort the data (or transfer stateful information). The following is the simplified workflow when you view the google.com website on your client[7](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:7), [8](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:8):

1. client makes an HTTP GET method Request to the google.com server
2. the server responds with a 200 OK status code if successful

The HTTP Request is composed of the following entities:

- Uniform Resource Locator (URL)
- HTTP headers
- HTTP body (optional)

|   |   |
|---|---|
|```<br>1<br>2<br>3<br>4<br>5<br>6<br>7<br>```|```js<br>google.com/search?q=beatles<br><br>method: GET<br>cookie: x=123;y=345<br>Accept-encoding: gzip/deflate<br>Accept: text/HTML<br>User-agent: Chrome<br>```|

The HTTP Request method (Verb) defines the type of action that should be performed on the server. Some of the popular HTTP methods are the following:

[![Popular HTTP methods](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/popular-HTTP-methods.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/popular-HTTP-methods.webp "Popular HTTP methods")

Figure 11: Popular HTTP methods

**Idempotent**: multiple invocations of the resource yield similar outcomes

**Safe**: Invocation does not modify the resource

The server ([Nginx](https://www.nginx.com/), Apache web server) delegates the HTTP Request to the Request handlers. The Request handler is a piece of code that is defined in any server-side programming language (such as Python, Node.js, or Java). The Request handler checks the HTTP headers of the Request (content-type, content-encoding, cookies, etc) and subsequently validates the HTTP Request body. The Request handler then generates an appropriate response in the content-type ([JSON](https://newsletter.systemdesign.one/p/protocol-buffers-vs-json), XML) that was requested by the client.

The HTTP server response is composed of the following entities:

- HTTP headers
- HTTP body

|   |   |
|---|---|
|```<br>1<br>2<br>3<br>4<br>5<br>6<br>```|```js<br>status code: 200 OK<br>Cache-control: private, expires=<datetime><br>Content-type: text/HTML<br>Content-encoding: gzip<br><br><HTTP Response body><br>```|

The HTTP Response Status Code helps you to troubleshoot failures. The HTTP Status Codes can be categorized into the following[9](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:9), [10](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/#fn:10):

[![HTTP Status Codes](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/HTTP-status-codes.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/HTTP-status-codes.webp "HTTP Status Codes")

Figure 12: HTTP Status Codes

The HTTP Request and Response headers are used to control the cache, authorize the client, or compress the data that is transferred between the client and the server. For example, the user-agent HTTP request header helps the server to identify if the client is a mobile or a desktop client. Some of the most popular HTTP headers are the following:

[![Popular HTTP headers](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/popular-HTTP-headers.webp)](https://systemdesign.one/what-happens-when-you-type-url-into-your-browser/popular-HTTP-headers.webp "Popular HTTP headers")