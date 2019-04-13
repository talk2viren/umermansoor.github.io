---
layout: post
title: What Are Containers? Understanding Containerization and How Docker Works
comments: True
excerpt_separator: <!--more-->
---

Docker is awesome. I was late to the party and didn't pay serious attention until last year. But Docker has quickly become one of the favorite tools. It enables software developers, like myself, to package, ship and run their applications anywhere without having to worry about setup or dependencies. Combined with Kubernetes, it becomes even more powerful streamling cluster deployments. I digress. Back to Docker. Docker is loved by software developers and its adoption rate has been remarkable.

So what exactly is Docker? 
It's a _platform_ for building, testing, deploying and publishing *containerized* applications. I say platform because Docker is a *set of tools* for managing all-things related to containers. Containers are at the heart of Docker so that’s what we’re going to explore in depth next.

<!--more-->

### What is a Container?

Containers provide a way to install and run your applications in isolated environments on a machine. Applications running inside a container are limited to resources (CPU, memory, disk, process space, users, networking, volumes) allocated for that container. Their visibility is limited container’s resources and can’t conflict with another container’s resources. You can think of containers as isolated sandboxes on a single machine for applications to run in.

This might sound familiar. The concept is similar to *virtual machines*. But there’s a a key difference: containers use a very different, light-weight technique to achieve resource isolation. The technique used by containers *exploits* features of the underlying Linux kernel* as opposed to [hypervisor](https://en.wikipedia.org/wiki/Hypervisor) based approach taken by virtual machines. In other words, containers call Linux commands to allocate and isolate a set of resources and then runs your application in this space. Let’s take a quick look at two such features:

##### 1. Namespaces
I’m over simplifying but [Linux namespaces](http://man7.org/linux/man-pages/man7/namespaces.7.html), essentially allow users to isolate resources like CPU, between independent processes. A process’s access and visibility are limited to its namespace. So users can run processes in one namespace without ever having to worry about conflicting with processes running inside another namespace. Processes can even have the same PID on the same machine within different containers. Likewise, applications in two different containers can use port 80 without conflicting with each other.

##### 2. cgroups 
[croups](http://man7.org/linux/man-pages/man7/cgroups.7.html) allow putting *limits and constraints* on resources.  For example, you can create a namespace and limit available memory for processes inside it to 1 GB on a machine that has say 16 GB of memory available.

By now, you’ve probably guessed how Docker works. Behind the scenes, when you ask Docker to run a container, it sets up a resource isolated environment on your machine. Docker copies over your packaged application and its associated files to the filesystem inside the namespace (basically, all it does is that it takes the snapshot of the filesystem that contains your application and all dependencies and creates it in the namespace.) At this point, the environment setup is complete. Docker then executes the command that you specified and hands over the control. 

In short, Docker orchestrates by setting up containers using Linux’s namespace and cgroups (and few other) commands, copying your application files to disk allocated for the container and then running the startup command. At its core, Docker’s is mainly used for performing [operating system level virtualization](https://en.wikipedia.org/wiki/OS-level_virtualisation). It also comes with a bunch of other tools that make managing containers like the ability to list running containers, stopping containers, publishing container images for others to fetch and use, and many others.

![docker containers]({{ site.url }}/img/blogs/docker-containers⁩/containers-on-box.png)

Since containers make direct use of Linux features for creating *native* resource isolated environments, they are more light-weight when compared to virtual machines. And since containers utilize underlying Linux features, they cannot run on other operating systems like macOS or Windows. At this point you might wonder how Docker is running on your macOS? The answer is that it uses a little trick to support containers on non-Linux operating systems which do not support running containers natively. Docker runs containers on macOS by installing a *Linux virtual machine* and then running containers inside it. 

Let’s put everything that we have learned so far and create and run a Docker container from scratch. If you don’t already have Docker installed on your machine, head over [here](https://docs.docker.com/install/) and install if on your machine. In this super made up example, we’ll create a Docker container, download a web server written in C, compile it, run it and then connect to the web server from our web browser (in other words, from host machine that’s running the container.)

We’ll start where all Docker projects start. By creating a file called `Dockerfile`. This file contains instructions that tell Docker how to create an *image* that’s used for creating and running containers. Since, we didn’t discuss images, let’s take a look at the [official definition](https://docs.docker.com/get-started/#images-and-containers):

> An image is an executable package that includes everything needed to run an application--the code, a runtime, libraries, environment variables, and configuration files. A container is a runtime instance of an image

Put simply, when you ask Docker to run a container, you give it an *image* which contains:

File system snapshot containing your application and all of its dependencies.
A startup command to run when the container is launched.

How does Docker know what files it should copy over in the environment? Basically, when you ask Docker to run a container, you provide it an _image_ which contains 
That’s essentially what an image is.

Back to creating our `Dockerfile` so we can build an image. It's extremely common in the Docker world to create images based on other images. For example, the official reds Docker image is based on 'Debian' file system snapshot ([rootfs tarball](http://www.ethernetresearch.com/geekzone/building-linux-rootfs-from-scratch/)), and installs on configures Redis on top of it.

In our example, we’ll base our image on Alpine Linux. When you see the term *alpine* in Docker, it usually means a stripped down, bare-essentials image. [Alpine Linux](https://hub.docker.com/_/alpine) image is about 5 MB in size! 

Alright. Create a new folder on your computer and then create a new file `Dockerfile`. In that file, paste the following contents.  

``` 
# Use Alpine Linux rootfs tarball to base our image on
FROM alpine:3.9 

# Set the working directory to be '/home'
WORKDIR '/home'

# Setup our application on container's file system
RUN wget http://www.cs.cmu.edu/afs/cs/academic/class/15213-s00/www/class28/tiny.c \
  && apk add build-base \
  && gcc tiny.c -o tiny \
  && echo 'Hello World' >> index.html

# Start the web server. This is container's entry point
CMD ["./tiny", "8082"]

# Expose port 8082
EXPOSE 8082
``` 

The `Dockerfile` we created above contains instructions on how to create the *image*. Essentially, we base our image on Alpine Linux ([rootfs tarball](http://www.ethernetresearch.com/geekzone/building-linux-rootfs-from-scratch/)) and set our working directory to be `/home`. Next, we downloaded, compiled and created an executable of a simple web server written in C. After, that we specified the command to be executed when container is run and expose container’s port 8082 to the outside world.

Now, we are ready to create the image. Running `docker build` in the *same directory* where you created `Dockerfile` should do the trick.  

```
umermansoor$ docker build -t codeahoydocker . 
```

If the command is successful, you should see something similar:  

```
Successfully tagged codeahoydocker:latest 
``` 

At this point, our image is created. It essentially contains:

1. Filesystem snapshot (Alpine Linux and the web server we installed)
2. Startup command (`./tiny 8092`)

![image]({{ site.url }}/img/blogs/docker-containers⁩/image.png)

Now that we’ve created the image, we can build and run a container from this image. To do so, run the following command:  

```
umermansoor$ docker run -p 8082:8082 codeahoydocker:latest 
``` 

Let’s understand what’s going on here. 

With `docker run`, we asked Docker to create and start a container from the `codeahoydocker:latest` image. `-p 8082:8082` maps port 8082 of our local machine to port 8082 inside the container. (Remember, our web server inside the container is listening for connections on port 8082.) You’ll not see any output after this command which is totally fine. Switch to your web browser and navigate to [localhost:8082/index.html](localhost:8082/index.html]). You should see ‘Hello World’ message. (Instructions on how to delete the image and container to clean up will be in comments.)

![tiny-container]({{ site.url }}/img/blogs/docker-containers⁩/tiny-container.png)

Hope this was helpful. Until next time.


