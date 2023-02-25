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

## Build docker image for backend

  `docker build -t  backend-flask ./backend-flask`
### Run Container using the image built
 

