+++
title = 'Play with Docker'
date = 2024-03-25T10:50:25+08:00
draft = false
+++

hey

#### Commands

Build a image

```bash
# . means the current directory, so the example command would use ./Dockerfile to build a image
docker build -t image_name .
```

Run a docker container using a image

```bash
# -d means run in the background
# --name container_name specifies the containner name
docker run -d --name container_name image_name
```

Show containners

```bash
# show the containers that are running
docker ps
# show all the containners
docker ps -a
```

Show the logs of a container

```bash
docker logs container_name
```

To be continue 

###### Makefile

A binary built on a Debian system may not work on an Alpine container due to differences in the underlying system libraries. Alpine uses `musl libc` instead of `glibc` which is commonly used in other distributions like Debian. As a result, a binary compiled against `glibc` might not work on Alpine.

To build a binary that is compatible with Alpine, you need to statically compile your Go application with `CGO_ENABLED=0` to not link against `c` libraries and ensure all dependencies are included in the binary itself.

Here's how you can modify your Makefile to build a statically linked binary that will run on Alpine:

makefile

```makefile
build:
        @CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o bin/servi .

run: build
        ./bin/servi
```

The `CGO_ENABLED=0` disables CGO, `GOOS=linux` ensures that the binary is built for Linux, `-a` forces rebuilding of all packages, and `-installsuffix cgo` adds a suffix to the package path to ensure a unique binary name. This will produce a binary that should work in your Alpine Docker container.

###### Dockerfile

An example:

```dockerfile
# Use a specific version of the minimal Alpine base image
FROM alpine:3.15

# Set the working directory inside the container
WORKDIR /app

# Copy the binary file of your Go application into the container
# Ensure that the binary has executable permissions before building the Docker image
COPY bin/servi /app/servi

# Set the entry point of the container to your Go application binary
ENTRYPOINT ["./servi"]
```

Remember to replace `3.15` with the version of Alpine that you want to use. Also, before building the Docker image, make sure your `servi` binary is executable by running `chmod +x bin/servi` on your host machine if necessary.
