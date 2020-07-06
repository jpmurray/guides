# Build & publish docker images

## Prune your stuff

For good measure, I always prune everything. Call me paranoid all you want, I know I am: `docker system prune -a`.

## Build your image(s)

The following will build and tag latest and a speciic version based on the `Dockerfile` present in the current directory.

`docker build --tag [user]/[name]:latest --tag [user]/[name]:[version] .`

## Push your image(s) to Docker Hub

`docker push [user]/[name]:[version]`
