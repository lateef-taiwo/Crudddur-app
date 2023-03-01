# Build the image from python:3.10-slim-buster
FROM python:3.10-slim-buster
# make and change directory inside the container image to /backend-flask
WORKDIR /backend-flask
# Copy requirements.txt from host to container image
COPY requirements.txt requirements.txt
# use pip3 to install the dependencies/packages stated in requirements.txt
RUN pip3 install -r requirements.txt
# copy everything from the cuurent directory in the host to the current directory (/backend-flask) in the container image
COPY . .
# set env var
ENV FLASK_ENV=development
# expose port for binding 
EXPOSE ${PORT}
#Run the Dockerfile CMD as an external script
#CMD [ "backendflask.sh" ]
# Run this command to build the container image to run on port 4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
