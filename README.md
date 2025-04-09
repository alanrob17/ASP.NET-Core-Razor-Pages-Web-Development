# ASP.NET Core Razor Pages Web Development

These are my notes from a course I am doing on creating a Razor Web Pages application.

I have created a Dockerised version of the notes that runs as a website on an Alpine Linux image running an Apache web server.

## Dockerfile

```bash
    FROM httpd:alpine
    COPY ./html/ /usr/local/apache2/htdocs/
```

## Build

```bash
    docker build -t razor-pages-web-dev-app .
```

## Run

```bash
    docker run -d --name razor-pages-web-dev-web -p 8000:80 learning-razor-pages-app
```
