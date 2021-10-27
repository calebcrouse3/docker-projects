# Source Material

Docker, Dockerfile, and Docker-Compose (2020 Ready!)

Packt Publishing

Safari Books

# Intro Notes

separation of data and container (using mounted files)

containers are static execution environments which wont suffer from environment drift

images define the configuration, containers are instances of an image

containers are shortlived, images are more longlived

# Docker Run

download and run an ubuntu image from docker hub and hop into a shell on that container \
`docker run -it ubuntu /bin/bash` \
`docker run -it ubuntu:latest /bin/bash`

with docker you are only running a single process, and the container runs as long as the process is running \
that process can run other processes \

`-it` i means interactive (you can type still), t means terminal

`docker ps` list running containers

`docker ps -a` list all containers

rerun a stopped container \
`docker start <conatiner-id OR first-character-of-container-id OR container-name>`

interact with the restarted container by opening a shell on it \
`docker attach <conatiner-id OR first-character-of-container-id OR container-name>` \
note, using attach on a detached container does not open a shell on it, you have to run \
`docker exec -it <container-id> /bin/bash` \
so i dont really understand the various way to open a shell but maybe just try a couple


# Mounting Volumes

run a docker contain with a mounted drive from your local filesystem \
`docker run --rm -v ${PWD}:/myvol ubuntu /bin/bash -c "ls -lha > /myvol/test.txt"`

`--rm` will remove container after it stops

# Rar Container and Dockerfile

run a linux container with winrar, mount the pwd directory, within the mounted directory, archive the txt file \
a stands for archive, it is a winrar command \
`docker run --rm -v ${PWD}:/files klutchell/rar a /files/test.rar /files/test.txt`

entrypoint is the process that the docker container will execute if nothing else is specified

`-w` flag defines the working directory which is like cd'ing into this directory when we start the container \
`docker run --rm -v ${PWD}:/files -w /files klutchell/rar a test.rar test.txt`

# Running PHP scripts with Docker and Mounting

run container preconfiggured with PHP \
`docker run -it --rm --name my-running-script php:7.2-cli /bin/bash`

need to mount a volume because when container goes away all files on it go away to \
`docker run -it -v ${PWD}:/myfiles --rm --name my-running-script php:7.2-cli /bin/bash`

set working directory to location of php file and instead of opening shell just run the script \
`docker run -it --rm -v ${PWD}:/myfiles -w /myfiles --name my-running-script php:7.2-cli php index.php`

# Service Logs and Port Fowarding

`-d` is detached mode and will run container in the background

apache server container \
`docker run -d httpd`

run docker ps and look at ports for that container
`80/tcp`

so you might think you can just use your browser to send a request to localhost:80 and see whats going on in your container but you cant, your request wont make it to the container \
hint: youre going to do port fowarding later 

can look at docker logs \
`docker logs <container-id>`

can look at docker logs and attach to it to see them as they come in \
`docker logs <container-id> -f`

can look at all the docker configurations for a container \
`docker inspect <container-id>`

look at portBindings config and see that its empty so the localhost cannot interact with anything on the container

run container with port binding \
`usage : docker run -p host_port:container_port image-name` \
`docker run -d -p 8080:80 httpd`

can see how ports are bound with docker ps \
`0.0.0.0:8080->80/tcp`

# Writing a Dockerfile

Dockerfiles are an entrypoiny for building an image 

Create a new docker file with a base image and some commands to copy in files and execute them

question: do the commands in the dockerfile run when we create the image or run it? \
it looks like maybe some will execute on build and some will execute on run, just depends \
in first-dockerfile/Dockerfile, everything but the `CMD php index.php` gets run on build

create an image from the docker file \
`docker build -t myphpapp . ` dont forget the dot. That tells docker to use the dockerfile in this directory \
`-t` is the flag for tagging

can view all created images with
`docker iamge ls`

pay attention to that fact that since we copied our php file on build instead of mounting it on run means that local
changes wont change the php file in the container, you have to rebuild to push those changes to the image

