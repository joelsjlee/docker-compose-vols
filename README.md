# Docker Compose Volume Tutorial

[![N|Solid](https://avatars3.githubusercontent.com/u/20544498?s=200&v=4)](https://github.com/upenndigitalscholarship/)

This is a basic Docker tutorial for setting up docker container communication using docker volumes.

### Setup

The first step is to create a directory for your project using `mkdir docker-compose-vols`. Inside this directory, create two directories, using `mkdir cat` and `mkdir echo`. cd into your `echo` directory and create a file called `Dockerfile`. Do the same inside your `cat` directory. Lastly, inside the root directory, create a `docker-compose.yml` file.

### Code

in `echo/Dockerfile` insert this code:

```
FROM python:3.6-alpine

WORKDIR /app

CMD echo first > /app/echotext.txt
```

in `cat/Dockerfile` insert this code:

```
FROM python:3.6-alpine

WORKDIR /app

CMD cat /app/echotext.txt
```

in `docker-compose.yml` insert this code:

```
version: '3'

volumes:
  local_volume: {}

services:
  echo:
    build:
      context: .
      dockerfile: ./echo/Dockerfile
    image: echo_image
    volumes:
      - local_volume:/app

  cat:
    build:
      context: .
      dockerfile: ./cat/Dockerfile
    image: cat_image
    depends_on:
      - echo
    volumes:
      - local_volume:/app

```
Open a terminal and cd into your project folder. Run
```
docker-compose build
```
If everything worked correctly, you should get this output:
```
$ docker-compose build
Building echo
Step 1/3 : FROM python:3.6-alpine
 ---> 35bb01a3d284
Step 2/3 : WORKDIR /app
 ---> Using cache
 ---> 4d43d91ba0e1
Step 3/3 : CMD echo first > /app/echotext.txt
 ---> Using cache
 ---> 22ad8521329d
Successfully built 22ad8521329d
Successfully tagged echo_image:latest
Building cat
Step 1/3 : FROM python:3.6-alpine
 ---> 35bb01a3d284
Step 2/3 : WORKDIR /app
 ---> Using cache
 ---> 4d43d91ba0e1
Step 3/3 : CMD cat /app/echotext.txt
 ---> Using cache
 ---> 045e0917fcf0
Successfully built 045e0917fcf0
Successfully tagged cat_image:latest
```
Next, run:
```
docker-compose up
```
This should run both of the Dockerfiles. First it will run the echo Dockerfile and write the words "hello world" into echotext.txt, and then it will run the cat Dockerfile and read the file that was just written. If everything went correctly, you should see:

```
$ docker-compose up
Starting dockercomposevols_echo_1 ... done
Starting dockercomposevols_cat_1  ... done
Attaching to dockercomposevols_echo_1, dockercomposevols_cat_1
dockercomposevols_echo_1 exited with code 0
cat_1   | hello world
dockercomposevols_cat_1 exited with code 0
```
