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
Docker's `run` command will pull the image from Dockerhub, create a container and start it.  Most tutorials out there (in)conveniently forget to mention this when they say talk about the run comand and only the run command.


```docker
docker create 
```

To be continued ...