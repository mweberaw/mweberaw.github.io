---
title: Multiple SSL Certificates on Same IP
layout: post
date: 2013-09-17
categories:
  - Configuration

---
The days where http was the standard for communication over the internet should be over. Most of the connections should be encrypted between the server and the client. In this case we are talking about SSL encryption between the web server and the browser.

Most users that have their own VServer have a single IP. SSL certificates that work for multiple subdomains (wildcard certificates) are very expensive. The solution seams to be to have multiple certificates, one for each subdomain. <a title="StartSSL" href="http://www.startssl.com/" target="_blank">StartSSL</a> offers these kind of certificates for free whereas wildcard certificates are still rather expensive. But there is a drawback in using one certificate per subdomain.

Most modern web servers support a technology that is called <a title="SNI (Server Name Indication)" href="https://en.wikipedia.org/wiki/Server_Name_Indication" target="_blank">SNI (Server Name Indication)</a>. This technology allows the server to distinguish between requests to different subdomains before starting the SSL encryption procedure. The problem with this technology is, that is has to be supported by all devices connecting to the server. Most modern web browsers support SNI, some older browsers and some smartphone apps do not.

When using a client that does not support SNI, the server connects with a default certificate. This certificate is not always the right one for the requested domain. But since without SNI the encryption of the connection has to start before looking at the requested subdomain, the server can not choose the right certificate up front. This is why you get a warning telling you that the certificate is not valid for the requested domain.

There is currently no known solution except for using multiple IPs (one for each subdomain) which is not feasible in most cases, and using wildcard certificates, which is quite expensive. Thankfully most modern software that supports SSL also supports SNI and most software that does not support SNI can be configured to accept this &#8220;wrong&#8221; certificate anyways.
