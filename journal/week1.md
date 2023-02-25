# Week 1 — App Containerization
## VSCode Docker Extension
Docker for VSCode makes it easy to work with Docker and I love it.

https://code.visualstudio.com/docs/containers/overview

    Gitpod is preinstalled with this extension

## Containerize Backend
Prior to Containerizing the backend, I ran the application to cofirm it works.
### Run Python flask backend app
    cd backend-flask
    export FRONTEND_URL="*"
    export BACKEND_URL="*"
    python3 -m flask run --host=0.0.0.0 --port=4567
    cd ..

* I unlocked the port on the port tab and open the link for 4567 in my browser
* At first, I got HTTP error 404, then I appended to the url /api/activities/home and I get json data
  
 ![data](./assets/Week-1/http-200-ok.png)
 ![json](./assets/Week-1/Json.png)
 ![json](./assets/Week-1/json-2.png)

### Add Dockerfile for backend
* Ensure you are in the backend directory

  `cd backend`

  `vim Dockerfile`

![DOckerfile](./assets/Week-1/Dockerfile%20backend.png)

 * Paste this code in the Dockerfile. 
    
        FROM python:3.10-slim-buster

        WORKDIR /backend-flask

        COPY requirements.txt requirements.txt
        RUN pip3 install -r requirements.txt

        COPY . .

        ENV FLASK_ENV=development

        EXPOSE ${PORT}
        CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]

   ![Dockerfile](./assets/Week-1/Dockerfile-backend2.png)

* I type the `docker images` command to see the docker images. This displays the newly built backend-flask image and the base image (python3:3.10-slim-buster). The backend-flask image gets a tag of latest (because we didn't specify a tag)

   ![docker](./assets/Week-1/docker-images.png)

## Build docker image for backend

  `docker build -t  backend-flask ./backend-flask`

  ![docker build](./assets/Week-1/docker-build1.png)

  ![docker build](./assets/Week-1/docker-build-1-successful.png)
### Run Container using the image built

    docker run --rm -p 4567:4567 -it backend-flask
    FRONTEND_URL="*" BACKEND_URL="*" docker run --rm -p 4567:4567 -it backend-flask
    export FRONTEND_URL="*"
    export BACKEND_URL="*"
    docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
    docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask
    unset FRONTEND_URL="*"
    unset BACKEND_URL="*"

![docker run](./assets/Week-1/docker-run.png)

### Run in background

 `docker container run --rm -p 4567:4567 -d backend-flask`

  ![docker](./assets/Week-1/backend-container-run-4569.png)

### Return the container id into an Environment variables

 `CONTAINER_ID=$(docker run --rm -p 4567:4567 -d backend-flask)`

### Get Container Images or Running Container Ids

    docker ps
    docker images
  `docker ps` displays containers that are currently running (not exited or stopped), `docker images` displays container images

   ![docker](./assets/Week-1/docker-ps.png)
   ![docker](./assets/Week-1/docker-images.png)

### Send Curl to Test Server
    curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"
     
  ![curl](./assets/Week-1/curl-backend-container.png)

### Check Container Logs
    docker logs CONTAINER_ID -f
    docker logs backend-flask -f
    docker logs $CONTAINER_ID -f

### Debugging adjacent containers with other containers

    docker run --rm -it curlimages/curl "-X GET http://localhost:4567/api/activities/home -H \"Accept: application/json\" -H \"Content-Type: application/json\""

### busybosy is often used for debugging since it install a bunch of thing
     docker run --rm -it busybosy

### Gain Access to a Container

`docker exec CONTAINER_ID -it /bin/bash`
> This can also be written as:

`docker exec CONTAINER_ID -it bash`

> You can just right click a container and see logs in VSCode with Docker extension


![docker](./assets/Week-1/backend-container-attach-shell.png)

### Delete or Remove a Container Image
    docker image rm backend-flask --force
The `--force` flag removes a container image that is currently running. 
    `docker rmi backend-flask` is the legacy syntax to remove an image

### Overriding Ports

      FLASK_ENV=production PORT=8080 docker run -p 4567:4567 -it backend-flask

## Containerize Frontend
### Run NPM Install
We have to run `npm Install` before building the container since it needs to install some dependencies the contents of node_modules

     cd frontend-react-js
     npm i

### Create Docker File
I Created a Docerfile in the frontend-react-js directory using vim editor.

  `vim Dockerfile`

    FROM node:16.18

    ENV PORT=3000

    COPY . /frontend-react-js
    WORKDIR /frontend-react-js
    RUN npm install
    EXPOSE ${PORT}
    CMD ["npm", "start"]

![frontend](./assets/Week-1/frontend-Docker-file.png)

##  Build frontend Container image

    docker build -t frontend-react-js ./frontend-react-js

## Run Container in detached mode
    docker run -p 3000:3000 -d frontend-react-js

## Multiple Containers
### Create a docker-compose file
I Created `docker-compose.yml file` at the root of the project and I pasted the code below in it.

    version: "3.8"
    services:
        backend-flask:
            environment:
            FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
            BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
            build: ./backend-flask
            ports:
            - "4567:4567"
            volumes:
            - ./backend-flask:/backend-flask
        frontend-react-js:
            environment:
            REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
            build: ./frontend-react-js
            ports:
            - "3000:3000"
            volumes:
            - ./frontend-react-js:/frontend-react-js

        # the name flag is a hack to change the default prepend folder
        # name when outputting the image names
    networks: 
        internal-network:
            driver: bridge
            name: cruddur

  ![docker-compose file](./assets/Week-1/docker-compose-file-pics.png)

  ### Run frontend and backend containers and make them communicate.

    docker compose -f "docker-compose.yml" up -d --build

![docker compose](./assets/Week-1/docker-compose-build-complete.png)
