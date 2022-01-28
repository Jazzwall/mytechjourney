# Docker Basics

### Setup
Docker daemon (Docker Engine) runs on Linux machines.  If you have a Mac or a PC, you will need to either use a Linux VM inside your Mac/PC, or download and install Docker Desktop. 

#### Linux VM Route
If you want to run Docker on your Mac/PC but not deal with Docker Desktop, then create a Linux VM, [install Docker using the local package manager](https://docs.docker.com/engine/install/debian/), and then figure a way to expose the VM ports so you can access them on your host machine.

The best part about this route is that you are ONLY dealing with open source parts of the Docker universe.

I have a Mac with  M1 Apple Silicon chip and unfortunately, most virtualization software available have issues with running VM's flawlessly (I tried QTM and Ubuntu Multipass).  As of right now, most of them have issues with networking.  A host restart messes up the networking setup more often than not.

Another downside of this is that VS Code's docker remote plugin only seems to work if you are running Docker locally on the machine instead of a VM or a remote machine.

#### Docker Desktop Route
Just go ahead and make a deal with the devil and download / install Docker desktop on your machine.  It just works.  Yes, there are licensing restrictions if you are a company with revenue over $10M.  At that point, I'm sure Docker's licensing won't be an issue for you.

#### Code Completion
You can manage containers and images from the terminal by either specifying their unique alpanumeric ID's or their friendly names.  I prefer friendly names.  And if you enable code completion for docker, you can use `tab` key to auto-complete the names.  Completion was working from the start when I tried it on an Ubuntu machine.  On my Mac, I had to [follow these steps](https://docs.docker.com/compose/completion/)

### Basic Usage

#### Explicit steps to creating and running a container 
Here are the explicit steps:
1) Download the image using the `pull` command.  Without the version number, you get the latest version of the image.   This is an optional step because when you create a container, if you don't have the right image, Docker will download it for you. 
	`docker pull redis:4.0` 
2) Create a container based on that image.  This is where you can map the container ports to your host machine.  
	`docker create -p 6001:6379 --name redis-demo redis:4.0`
3) Start the container.  Notice I am using the container name instead of it's universal ID.  In fact, I used auto complete in my terminal and didn't have to type the full name.
	`docker start redis-older`
	
#### One step to running a container
Docker's `run` command will pull the image (latest version, unless specified) from Dockerhub, create a container based on that image, and then start it.  Many tutorials out there (in)conveniently forget to mention this when they say talk about the run comand and only the run command.  To do the explicit steps above all in one line:
`docker run -dp 6001:6379 --name redis-demo redis:4.0`
The -d command is for running in detached mode.  That is, the command will spin off the running of the container but then return so you can use that command line again.

#### Looking at logs
`docker logs redis-demo`

#### Running a command inside (including bash terminal)
- For running pwd (present working dir) command inside docker
`docker exec -it redis-demo pwd`
- For running an interactive bash terminal
`docker exec -it redis-demo /bin/bash`

#### Docker Network
Docker allows for the creation of an isolated network that you can place your containers in.  This way they can talk to each other using the container names without having to expose specific ports through the host machine.  Docker comes with a default set of networks.  

You can list existing networks using:
`docker network ls`

You can create a new network using:
`docker network create demo-network`

TODO: This section could be expanded...

### A Simple Project
#### How Docker Plays a Role
Let's say there is a simple dev workflow for building a JavaScript application that uses MongoDB as it's database.  Where would docker show up in this setup?
1) On the dev machine, we could use a MongoDB container (and a MongoExpress container for a nice GUI to manage MongoDb).
2) On the dev machine, we could use a Docker container as our development environtment and use VS Code and it's Docker Remote plugin to develop directly in the container.
3) A code commit/push to a remote repository could trigger a Jenkins / Github Actions workflow that builds the JS app and pushes it to a private Docker repository.
4) The staging server pulls and deploys the app's Docker image.
5) The staging server pulls and deploys the MongoDB image from Docker Hub.

#### Steps
1) Create Docker network: 
`docker network create demo-network`
2) Create a mongodb container with port 27017 exposed with a username and password using environment variables. 
	```docker
	docker run -dp 27017:27017 --name mymongo --network demo-network \
	> -e MONGO_INITDB_ROOT_USERNAME=admin \
	> -e MONGO_INITDB_ROOT_PASSWORD=password \
	> mongo
	```
3) Create a mongo-express container with the right environtment variables so that it connects to the mongo instance
	```docker
	docker run -dp 8081:8081 \
	> -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
	> -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
	> --network demo-network \
	> --name mymongoexpress \
	> -e ME_CONFIG_MONGODB_SERVER=mymongo \
	> mongo-express
	```



### Case for Docker Compose
Creating multiple related containers separately (like above) could get tedious and error prone.  And so, we have Docker Compose that makes it possible to configure and run multiple Docker containers much easier: Docker Compose.  With this tool you create a declarative yaml file that contains the parameters you would have specified in Docker run command in a structured way.

Docker Compose by default takes care of creating a common network.

Here is the yaml file describing the above two containers:
```yaml
version: '3'

services:
  mymongo:
    image: mongo
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=password
  mymongoexpress:
    image: mongo-express
    ports:
      - 8081:8081
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=password
      - ME_CONFIG_MONGODB_SERVER=mymongo
```

