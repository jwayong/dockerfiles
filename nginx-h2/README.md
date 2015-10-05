## Build Nginx with HTTP/2 support

This repository contains a Dockerfile that compiles an Nginx installation with support for HTTP/2 enabled. We also provide a sample nginx.conf.
The build we do in this Dockerfile will work for a single static website. You may need to change some ```configure``` options according to your needs.

To make the Docker build, you may clone this repository and run the Docker commands as follows.

```
$ git clone 
$ cd 
$ docker build -t nginx-h2 .
$ docker run -d -p 8443:443 -v /path/to/my/website/:/usr/local/nginx/html/ nginx-h2
```

Change ```/path/to/my/website/``` to the absolute path to your website.

Access ```https://localhost:8443/``` through your browser and have fun!

## Important

The HTTP/2 patch for Nginx is still in alpha. You may want to test it before putting it into production. [Read their blog post for more info].

[//]: #
[Read their blog post for more info]: <https://www.nginx.com/blog/early-alpha-patch-http2/>
