# http_to_https_wrapper
Notes on how to wrap a existing old HTTP ONLY website, with a HTTPS Docker Server. Basically, as new browsers force to HTTPS only, you may need to open it up in a new browser, so you can wrap the website through a SSL PROXY using the below steps!

Follow the steps below as needed!

## Generating SSL Certificates

First, generate the necessary SSL certificates using OpenSSL. Run these commands in your terminal:

```bash
openssl genrsa -out server.key 2048
openssl req -new -key server.key -out server.csr   # Press Enter for all prompts
openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt
```

## Nginx Configuration

Using the nginx:1.26.2-alpine Docker image (for example), add the following server block to your existing Nginx configuration file:

```nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate /path/to/server.crt;
    ssl_certificate_key /path/to/server.key;

    location / {
        proxy_pass YOUR_URL;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Replace `example.com` with your domain name and `YOUR_URL` with the URL you want to proxy to.

## Docker Compose Configuration

In your `docker-compose.yml` file, mount the following volumes as read-only:

```yaml
version: '3'
services:
  nginx:
    image: nginx:1.26.2-alpine
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./server.crt:/path/to/server.crt:ro
      - ./server.csr:/path/to/server.csr:ro
      - ./server.key:/path/to/server.key:ro
    ports:
      - "443:443"
```

Make sure to adjust the paths according to your project structure.







# LEGACY NOTES BELOW (OLD)
```


Generate certs with these commands

openssl genrsa -out server.key 2048
openssl req -new -key server.key -out server.csr   # enter for everything
openssl x509 -req days 3650 -in server.csr -signkey server.key -out server.crt
 

I am using nginx:1.26.2-alpine docker image

 

Using the default config, just add this to it

server {

                                listen 443 ssl http2;

                                server_name example.com;

                               

                                ssl_certificate /path/to/server.crt;

                                ssl_certificate_key /path/to/server.key;

                               

                                location / {

                                                                proxy_pass YOUR_URL;

                                                                proxy_set_header Host $host;

                                                                proxy_set_header X-Real-IP $remote_addr;

                                }

}

 

 

In docker-compose.yml, mount the following volumes as read-only

nginx.conf
server.crt
server.csr
server.key
 

 


 

 


```
