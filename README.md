DOCKER
Understanding Docker Concepts
Docker is a containerization platform that allows developers to package applications and
their dependencies into self-contained units called containers. these containers can then be 
easily deployed and run on any system, ensuring consistency and portability

Container: A lightweight, standalone, and executable package that includes everything needed to run a piece of software.
Image: Think of this as a template or blueprint for containers. It contains all the instructions needed to create a container.
Docker Hub: Like GitHub but for Docker images - it's where you can find and share container images.
Docker Engine: The core technology that runs and manages containers on your machine.

/////////////////////////////////////////////////////////////////////////


DOWNLOADING DOCKER ON WINDOWS
Install Docker Desktop for Windows Professional or Windows Server
https://docs.docker.com/docker-for-windows/install/
README FIRST for Docker Toolbox and Docker Machine users: Docker Desktop for Windows requires
Microsoft Hyper-V to run. The Docker Desktop for Windows installer enables Hyper-V for you, if
needed, and restarts your machine. After Hyper-V is enabled, VirtualBox no longer works, but any
VirtualBox VM images remain. VirtualBox VMs created with docker-machine (including the default
one typically created during Toolbox install) no longer start. These VMs cannot be used side-by-side
with Docker Desktop for Windows. However, you can still use docker-machine to manage remote
VMs.
If you want to download Docker Desktop without logging into hub.docker.com then you can try to
download it from here:
https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe

/////////////////////////////////////////////////////////////////////////

DOWNLOADING DOCKER ON MAC
Install Docker Desktop for MacOS Step by Step
https://docs.docker.com/docker-for-mac/install/
You normally need to sign up first in Docker-Hub to download docker. Officially, there is no other
way – at least none that I found. I have therefore copied the direct download link here, so, if you
really don’t want to signup to Docker-Hub then download it directly.
https://download.docker.com/mac/stable/Docker.dmg

/////////////////////////////////////////////////////////////////////////

try runnind the command :  docker run hello-world
 You should see output similar to this:

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

For more examples and ideas, visit:
 https://docs.docker.com/get-started/ 

Docker daemon (the background service that manages Docker on your machine).

Understanding Docker Images
Now that we've run our first container, let's explore Docker images in more detail. Remember, an image is like a blueprint or a template for a container. 
It contains all the instructions needed to create a container.
To see the images available on your local system, use the command:
docker images

Run a Docker container and access its shell.
 docker container run --interactive --tty --rm ubuntu bash

In this example, we’re giving Docker three parameters:
--interactive says you want an interactive session.
--tty allocates a pseudo-tty.
--rm tells Docker to go ahead and remove the container when it’s done executing.
The first two parameters allow you to interact with the Docker container.
We’re also telling the container to run bash as its main process (PID 1).
Run the following commands in the container.
ls / will list the contents of the root directory in the container, ps aux will show running processes in the container, cat /etc/issue will show which Linux distro the container is running, in this case Ubuntu 20.04.3 LTS.
Type exit to leave the shell session. This will terminate the bash process, causing the container to exit.
You can start a container by running the command 
docker start <container_id or the first two character of the id>
You can connect to the container by running the command 
docker attach <container_id >

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet1
Lecture: Running a single Docker Container step by step
docker run -it ubuntu /bin/bash
• runs the container and attaches to stdin, the standard input, where the /bin/bash processs is
opened
• flags:
o -it: interactive flag
ls
• file system from linux
cli2: docker ps
• Start this from a second terminal
• lists the currently running container
exit
• Exits the container and the linux file system again
Cli2: docker ps
• Lists the currently running containers, which is empty
Cli2: docker ps -a
• Lists all containers, the running ones and the empty ones
docker start container_identifier
• We can restart a container that was previously running
Cli2: docker ps
• The container comes up as running again. Creation date != running date
docker attach container_identifier
• We can re-attach to the container
cli2: docker stop container_identifier
• stop the running container
docker ps• The container is not running anymore
docker ps -a
• The container is still in our system
docker rm container_identifier
• Clean up: Remove the container
docker ps -a
• And the list is empty again

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet2
Running multiple docker containers from the command line step by step
This shows the difference between containers and images. We will create two containers (linux1, linux2) based on the same image (ubuntu)
  docker run -it -d --rm --name linux1 ubuntu /bin/bash
· creates container “linux1”
· additional flags:
 -d starts the container as “detached”. Use “docker attach” to attach to it later on.
 --rm cleans up the container after stopping. The container will be removed, basically the same as “docker rm container_identifier” after stopping the container. So everything is kept tidy.
 --name will give the container a dedicated name, which makes it easier to address the container later on.
docker run -it -d --rm --name linux2 ubuntu /bin/bash
· Creates container “linux2”
Cli1: docker attach linux1
· Attaches to container linux1
Cli1: ls
· Lists the file system on linux1
Cli1: mkdir mylinux1
· Creates a new directory on container linux1
Cli1: ls
· Shows that “mylinux1” was created
Cli2: docker attach linux2
· Attaches to container linux2
Cli2: ls
· Shows that the directory of linux2 is different than linux1, although they are both from the same image “ubuntu”
· They are separated, they don’t share their file-system
· The bash process is isolated in the container
Cli2: exit
· This will exit the bash and also remove the container because of the --rm command
Cli2: docker ps -a
· Shows only one container which is running, the other one got removed
Cli1: exit
· Exits the only running container linux1 and deletes the container
  docker ps -a
· Shows nothing anymore

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet3
Running docker containers with a shared host file system (a.k.a. docker volumes)
Let’s share some files from our host to our docker container. This is done with the “-v” flag and then “host-directory:client-directory” and is directly mapped into the container.

docker run --rm -v ${PWD}:/myvol ubuntu /bin/bash -c "ls -lha > /myvol/myfile.txt"
· This command runs “ls -lha” and pipes the output to /myvol/myfile.txt
· /myvol is “mounted” to the current working directory on the host
· On the host you can observe a new file called “myfile.txt” with the output of “ls -lha”
· The container is then ended and removed because of “--rm"
  From the docker hub I get an image that contains the rar tool. It’s minimal, but it demonstrates what can be done with separated environments:
https://hub.docker.com/r/klutchell/rar/dockerfile
docker run --rm klutchell/rar 
· This will download the image klutchell/rar
· Execute it
· In the Dockerfile you can see the “entrypoint” is already the “rar” executable
o This means, upon execution of “docker run” it starts the rar process.
o All you must do is work with it like you work with “rar” itself
· “a” command will add to an archive.
· If we mount the right directory, we can simply compress files from the command line:
  docker run --rm -v ${PWD}:/files klutchell/rar a /files/myrar.rar /files/myfile.txt
· It creates a new compressed rar file called “myrar.rar” inside the hosts working directory
· It’s fully compliant to rar files
  docker run --rm -v ${PWD}:/files -w /files klutchell/rar a myrar.rar myfile.txt
· The “-w /files” flag sets the working directory to /files. 
· It’s like “cd /files” inside the container and we can skip the full path to the files we want to compress

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet4
Using PHP with volume mounting in docker images step by step
docker run -it --rm --name my-running-script php:7.2-cli /bin/bash
· Downloads the official php 7.2 cli image
· Runs a shell inside
php -m 
· Get the installed modules
php -i
· Get the config of the PHP cli
echo '<? echo "test text\n";' > ~/index.php
· This will write <? echo “text text\n”; into the index.php file in the home directory of root
· Let’s execute this
php ~/index.php
· Should print now “test text”
exit
· Will end the container, but will also remove the container (including our index.php) because of the --rm flag
· This is where mounting an external host directory comes in handy
docker run -it --rm -v ${PWD}:/myfiles --name my-running-script php:7.0-cli /bin/bash
· Now we are running the php 7.0, not the php 7.2 cli
· And we are mounting the root directory into the /myfiles directory inside the container
echo '<? echo "test text\n";' > /myfiles/index.php
· This creates the same index.php, but this time inside /myfiles.
· Observe the host directory
php /myfiles/index.php
·This should run index.php with the php 7.0 interpreter
exit
· We can safely exit again, because our file is securely stored on the host. It will not be destroyed with the container upon exiting
docker run -it --rm -v ${PWD}:/myfiles --name my-running-script php:7.2-cli /bin/bash
· Let’s re-run the 7.2-cli container
· This time with the same directory mounted in /myfiles
php /myfiles/index.php
· This allows you to execute the same script with another PHP version
exit
· Let’s exit the container
docker run -it --rm -v ${PWD}:/myfiles -w /myfiles --name my-running-script php:7.2-cli php index.php
· Will directory run index.php and output the “test text” and exit again
· No need to actually enter the container
· -w flag again sets the working directory to /myfiles

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet5
Working with Servers, Logs and Port forwarding in Docker
In this example we are starting an apache webserver hosting a single file
docker run -d httpd
• Will start an apache in detached mode (-d)
• It should open a webserver on port 80
Open http://localhost:80
• That doesn’t do anything
• But is the Web-Server really running?
docker exec -it container_identifier /bin/bash
• You get an interactive shell inside the container
apt-get update && apt-get install curl
curl localhost:80
• Should bring up a “it works” message, which means the webserver is running
• But why can’t we see it on the host?
exit
• Exit the interactive shell
docker logs container_identifier
• This will give you the log output of the container
• You even see the request from inside the container
docker logs container_identifier -f
• Will follow the log output until you hit ctrl+c
• Localhost:80 doesn’t do anything
docker inspect container_identifier
• Will print out the container information
• You see there are no ports bound to the host
docker rm -f container_identifier
• Stop and remove the container
docker ps -a
• Should be empty
docker run -d -p 8080:80 httpd
• -p 8080:80 forwards HOST:GUEST
• On the host machine port 8080 is now mapped to port 80 in the guest machine
Open http://localhost:8080
• Should bring up now “it works”
docker inspect container_identifier
• Now shows the forwarded port
docker rm -f container_identifier
• Should stop and remove the container again
http://localhost:8080
• Is again unreachable, because the server stopped
docker run -d -p 8080:80 -v ${PWD}:/var/www/html php:7.2-apache
• Maps the current directory into /var/www/html, which is the document-root for apache
• If you still have the index.php there it will now be served on http://localhost:8080
docker rm -f container_identifier
• Let’s remove the container

/////////////////////////////////////////////////////////////////////////

 Exercise Worksheet6
Your first Dockerfile Step by Step
Let’s create this Dockerfile:
FROM php:7.2-cli
RUN mkdir /myproject
COPY index.php /myproject
WORKDIR /myproject
CMD php index.php
And also this index.php file in the same directory:
<?php
echo "hello world \n\n";
Now go into the folder where the Dockerfile is located at.
docker build -t myphpapp .
• “build” builds a new image
• -t is a tagging. In this case “myphpapp”, you can later find it.
• “.” is telling where the Dockerfile is located at
docker run myphpapp
• Should print out “hello world” with two line breaks
• Ends the container immediately, the process “php index.php” has finished
• “CMD php index.php” opens this upon spin up of the container
docker image ls
• List all the images
• See on top your newly created image
docker ps -a
• List the containers
docker rm container_identifier
• Remove the container from the myphpapp
docker rmi myphpapp
• rmi will delete an image
• And all depending images if they are not necessary anymore

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet7
Using a PHP Dev-Server inside a Container with a Dockerfile
Change the Dockerfile from the previous example to:

Then run the command:
docker build -t myphpapp .
·Will rebuild the image and tag it again as “myphpapp”
·Will overwrite the old one
·Will copy the index.php to a new directory /myproject
·Will run “php -S 0.0.0.0:8000” and serve the content of /myproject on port 8000
·CMD will take an array and run a command. Every parameter must be in a separate array element, so: CMD [“command”,”param1”, “param2”, …]
docker run --name myphp-container -p 8080:8000 myphpapp
· Will run spin up the image “myphp” which we generated
·Will forward port 8000 in the container to port 8080 on the host
·http://localhost:8080 should bring up the hello world
ctrl+c 
·to stop the container
·Mind on Windows: The container is still running in the background.
docker rm -f myphp-container
To remove the container: cleanup
docker rmi myphpapp
· Remove the myphpp image again

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet8
www.vomtom.at
From the Course:
Understanding Docker Run, Dockerfile, Docker-Compose for Beginners
Ship your Web-Application using Apache and PHP as Docker container with a
Dockerfile
Let’s create a Docker image and run this image as container with an Apache with PHP pre-installed.
Then serve our index.php from the previous example.
Change the Dockerfile to:
FROM php:7.2-apache
COPY index.php /var/www/html
Then run the following commands:
docker build -t myphpapp .
• This will build an image myphpapp:latest
• Will copy the index.php from the host inside the container in the directory /var/www/html
• Will package everything
docker run --name myphp-apache -p 8080:80 myphpapp
• Will run the image myphpapp image
• Open the apache inside the image
• Bind port 8080 on the host to port 80 on the guest
• Will give the container the name “myphp-apache”
http://localhost:8080
• Should bring up the “hello world” from the index.php
• Changing the index.php on the host should not change it on the guest – it’s baked into the
image
• If you want a live version you need to volume-mount it into the container
ctrl+c
• To stop the container
docker rm -f myphp-apache
• To remove the container
docker rmi myphpapp
• To remove the image for now

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet9
Create an Image and upload it to Docker-Hub
Use this Dockerfile:
FROM alpine
RUN apk update && apk add curl
ENTRYPOINT [ "curl" ]
Run
docker build -t mycurl .
• This will build an image
docker image ls
• List the images
docker run --rm mycurl www.google.com
• Should output the HTML from the google website
docker login
• Login to Docker-Hub
• If you don’t have a user-name then create one first
• Remember your username! Let’s assume it is “myUser123”
docker tag mycurl myUser123/mycurl:latest
• Tag your image with “username/repository:tag” -> “myUser123/mycurl:latest”
docker image ls
• Lists also the new image as well, with the same container ID
docker push myUser123/mycurl:latest
• Pushes the image to docker hub
docker rmi mycurl
• Remove the “mycurl” image
docker rmi myUser123/mycurl:latest
• Remove the myUser123/mycurl:latgest image as well
docker run --rm myUser123/mycurl:latest www.google.com
• It will download the image again from docker hub from your public repository
docker rmi myUser123/mycurl:latest
• cleanup

/////////////////////////////////////////////////////////////////////////

Exercise Worksheet10
The docker-compose.yml File Explained Line by Line for Composer Beginners
Let’s re-use this Dockerfile:
FROM php:7.2-apache
COPY index.php /var/www/html
And this index.php file in the same directory:
<?php
echo "hello world \n\n";
But this time we will add a docker-compose.yml file in the same directory as well:
version: '3'
services:
phpapp:
ports:
- "8080:80"
build:
context: ./
dockerfile: Dockerfile
Then we run
docker-compose up
• It will read the docker-compose.yml file in the current directory
• It will build a new image “folder-name”_”service-name” based on the Dockerfile
o called “step-1_phpapp”
• It will run this as a container named “folder-name”_”service_name”_”index”
o So: “step-1_phpapp_1”
Now we can access http://localhost:8080 and it should show us the “hello world” again from our
index.php.
If we change the index.php nothing happens.
• Change the “hello world” to “hello docker”.
• Save it.
• Reload the browser, you still see “hello world” instead of “hello docker”
• Do you know why?
ctrl-c
• Stop the running container(s)
docker-compose up
• Start the container(s) again
Open http://localhost:8080 should still bring up “hello world”.
docker-compose up --build
• This rebuilds the image before spinning it up
• Now http://localhost:8080 container “hello docker”
docker-compose rm
• Cleanup, remove the containers

/////////////////////////////////////////////////////////////////////////
