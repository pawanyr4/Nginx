# Test NGINX Setup

This repository contains a test NGINX setup demonstrating the following features:

- Returning a 404 response for requests to specific file extensions.
- A custom log format.
- Setting security headers if they are missing from the upstream response.

Configuration files include `nginx.conf` and `security-headers-map.conf`, along with a testing setup using `nginx-upstream.conf` and `docker-compose.yml`.

## 1. Test 404 on Listed Assets

To test the 404 response for specific file types, run the following command:

```bash
curl localhost:8080/test.mp4 -I
```

Expected response:

```
HTTP/1.1 404 Not Found
Server: nginx/1.25.3
Date: Thu, 30 Nov 2023 01:27:26 GMT
Content-Type: text/html
Content-Length: 153
Connection: keep-alive
```

Associated log entry:

```
30/Nov/2023:01:27:26 +0000 nginx/1.25.3 172.19.0.1 0fcd0ea6e0abf2484b094423474cb2b3 404 0 "curl/7.81.0" - _ - 0.000 - - - "/test.mp4" - - "-"
```

## 2. Log Format Sample

Example of a log entry in the custom format:

```
30/Nov/2023:01:28:07 +0000 nginx/1.25.3 172.19.0.1 e942a945bf864c79dee606816f83b09d 200 0 "curl/7.81.0" - _ 172.19.0.2:3000 0.001 0.001 0.001 0.001 "/" 200 - "-"
```

## 3. Security Headers Test

This example demonstrates the `Strict-Transport-Security` header being set by the upstream configuration (with a `max-age` of 60). If not set by the upstream, it defaults to "max-age=31536000; includeSubDomains" as defined in security-headers-map.conf

```bash
curl localhost:8080 -I
```

Response showing security headers:

```
HTTP/1.1 200 OK
Server: nginx/1.25.3
Date: Thu, 30 Nov 2023 01:32:30 GMT
Content-Type: text/html
Content-Length: 615
Connection: keep-alive
Last-Modified: Tue, 24 Oct 2023 13:46:47 GMT
ETag: "6537cac7-267"
Strict-Transport-Security: max-age=60 # Note the max-age
Accept-Ranges: bytes
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: DENY
Content-Security-Policy: frame-ancestors 'none'
Access-Control-Allow-Credentials: TRUE
```

## Running and Testing the Setup

To run and test this setup, use the following commands:

```bash
docker compose up -d
curl -I http://localhost:8080
```

Example output:

```
$ docker compose up -d
[+] Running 3/3
 ✔ Network nginx_config_default              Created                                                                                                                                                                                     0.1s
 ✔ Container nginx_config-upstream-server-1  Started                                                                                                                                                                                     0.1s
 ✔ Container nginx_config-nginx-proxy-1      Started

$ curl -I http://localhost:8080
HTTP/1.1 200 OK
Server: nginx/1.25.3
Date: Thu, 30 Nov 2023 01:34:56 GMT
Content-Type: text/html
Content-Length: 615
Connection: keep-alive
Last-Modified: Tue, 24 Oct 2023 13:46:47 GMT
ETag: "6537cac7-267"
Strict-Transport-Security: max-age=60 # Note the max-age
Accept-Ranges: bytes
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: DENY
Content-Security-Policy: frame-ancestors 'none'
Access-Control-Allow-Credentials: TRUE
```

