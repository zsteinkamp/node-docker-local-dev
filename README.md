# node-docker-local-dev
An example of how to do local node development with docker-compose.

The goal is to run node and other services (an API app, Postgres DB, and Redis) via docker-compose, but be able to use any editor on your workstation to edit files in the UI app in this directory, while supporting live-reload in the browser.

An nginx container routes requests made to /api/\* to the 'api' container. All other HTTP requests are routed to the UI app in the local directory.

Additionally, the docker-compose.yml file shows how to set up and seed data into your local database by mounting a directory of \*.sql files into your Postgres container. You can do something similar if you want to use mysql.

## Assumptions
This repo assumes you client app is in the repo root directory, and an API application exists at ../my_api_app/. If you do not want to build the API app, then remove the `build` line in the `api` service in the `docker-compose.yml` and change the `image` line to point to your API app tag, e.g:
```
# docker-compose.yml
version: '2'
services:
...
  api:
    image: some.repo.name.com/myrepo/my-api-app:latest
    depends_on:
      - db
      - redis
...
```

If you do not have a separate API app, then remove the section from `docker-compose.yml` and from `config/nginx/nginx.conf`.

Another assumption is that the app runs on port 3000 with BrowserSync running on 3001. If that is not the case, then you will need to change the port numbers in `Dockerfile`, `docker-compose.yml`, and `config/nginx/nginx.conf`.

## Doing The Needful
To do local development, simply run:
```
docker-compose build
docker-compose up
```
Then point your browser at http://localhost:3000/.

You may then use your favorite editor (vim, Sublime, pico) to edit your UI files directly on your workstation.

The source directory is mounted as a volume inside the node UI container, so changes you make locally are reflected in the container. The node process running in the container watches those files, and triggers rebuild/reload as it would running locally.

## Bugs
When you bring up the container it does not open your browser. Not sure if that's even possible (hopefully not).
