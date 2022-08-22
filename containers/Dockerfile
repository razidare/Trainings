# Official documentation: https://docs.docker.com/engine/reference/builder/
# Every Dockerfile should start with a FROM keyword
# The ARG keyword, however can precede FROM in a Dockerfile

# minimal image used to install your own OS or single binary:
#
# FROM scratch
#
# you can have 2 or more FROM clauses in your Dockerfile
# but each new FROM clears any state created by previous ones

# actual ubuntu OS official image, but containerized
# will create the base of our image
FROM ubuntu:20.04

# add some metadata
LABEL owner="Shikki"
LABEL purpose="Training"

# update package manager and install CURL (will create 2 separate layers in the image)
RUN apt-get update
RUN apt-get install curl -y

# COPY a file from host inside the image
COPY ./containers.md /the_coolest_markdown.md

# Update package manager and install nginx (will create one layer in the image)
RUN apt-get update && apt-get install nginx -y

# Instruct the users on what port the nginx server will listen on
# the EXPOSE keyword does not actually expose the port, but will tell the
# user of the image to include "-p" flag on the "docker run" command
EXPOSE 80

# Start a main process that the container will run around
# this can be done either with CMD, ENTRYPOINT or both together
CMD ["nginx", "-g", "daemon off;"]