---
title: SCM-Manager behind a proxy (NGINX)
layout: post
date: 2013-08-27
categories:
  - Configuration

---
When configuring my vserver, I came upon a problem of configuring a subdomain for <a title="SCM-Manager" href="http://www.scm-manager.org/" target="_blank">SCM-Manager</a>.

My plan was to run an instance of SCM-Manager to manage git and mercurial repositories. Mostly to have a simple web interface and secondly to test the system for suitability in a production environment. The plan was to have a separate subdomain for all kinds of version control systems, in my case scm.weber-inter.net.

Since I had already configured an instance of NGINX, I added a proxy instance for my subdomain proxying requests to the preconfigured SCM-Manager server. The server ran on port 8085 on localhost, so no access from the outside was possible. The access path for the web application is /scm by default. So the proxy entry in the NGINX configuration looked like this:

```
location / {
  proxy_pass http://127.0.0.1:8085/scm/;
  proxy_buffering off;
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto https;
}
```

The behavior of SCM-Manager was very strange. I could open the page and it showed a login screen as normal. I could login with the default scmadmin:scmadmin credentials. But when the dashboard showed up, the system told me that the session was expired. Other descriptions suggested that the proxy configuration was wrong. But the NGINX configuration was not the problem.

The strange behavior comes from the fact that the cookie that is sent by SCM-Manager includes a path directive. This directive restricts the validity of the cookie to the path /scm, which is correct for the default server when exposed to the outside. The problem in my case was, that proxyed instance was running on the root of the subdomain, so the cookie was invalid for scm.weber-inter.net/.

The solution at the moment is to configure a Jetty of Tomcat instance with the SCM-Manager application instance running on the root path. Then forwarding of the proxy directly goes to http://127.0.0.1:8085/ and the returned cookie is valid.
