# Docker
***

## Brief intro on Docker
- Docker is a tool designed to create, deploy, and run applications by using containers. The applications you build, if shipped with a docker image, become reproducible anywhere.  Containerisation offers a consistent state across different servers or cloud environments.
- It is open-source and essentially a container file format. It automates the deployment of applications as portable, self-sufficient containers that can run in the cloud or on-premises.
- A container is a standardized software unit, in simple terms — nothing but a packaged bundle of application code and required libraries and other dependencies.
- A **Docker Image** is an executable software package that includes everything needed to run an application and becomes a Container at runtime.
- Docker is like a virtual machine!
- It has its own filesystem and the files from your local machine are not shared to the docker container. 
- If you want to use a path from your local machine and want to modify it too, you would need to mount it to the docker container when running it. 
***

## Motivation
- So, let’s say you have learned git and formatted the code properly, have written proper documentation and have open-sourced your project. Is that enough? No. It’s not. 
- It’s because you wrote code on your computer and that might not work on someone else’s computer because of many different reasons. 
- So, it would be nice if when you distribute the code, you could replicate your computer and others can too when they install your software or run your code. 
- To do this, the most popular way these days is to use Docker Containers 
- Docker containers can be considered as **small virtual machines**. You can create a container for your code, and then everyone will be able to use it and access it. 
***

## Docker (Docker Swarm!) vs. Kubernets
- **Docker** is meant to be run on a single node.
- While it’s common to compare Kubernetes with Docker, a more apt comparison is Kubernetes vs **Docker Swarm**. Docker Swarm is Docker’s orchestration technology that focuses on clustering for Docker containers – tightly integrated into the Docker ecosystem and using its own API.
- **Kubernetes** (also known as k8s) is meant to run across a cluster environment. It is more extensive than Docker and is meant to coordinate clusters of nodes at scale in production in an efficient manner. While Docker provides an open standard for packaging and distributing containerised apps, the potential complexities can add up fast. How do you coordinate and schedule several containers? How do all of the different containers in your app talk to each other? How do you scale several container instances? This is where Kubernetes can help. It contains all the parts and glue to form a resilient and reliable way of running services, including things like load balancers, resource quotas, scaling policies, traffic management, sharing secrets, and more.
- **Can you use them together?** Although they are fundamentally different technologies, they work well together when building, delivering and scaling containerised apps.
***

## Docker vs. Virtual Environment
- You could argue that virtual environment approaches such as conda and virtualenv address these issues. They do, but only partially.
- Several non-Python dependencies are not managed by these solutions.
- Due to the complexity of a typical machine learning stack, a large part of framework dependencies, such as hardware libraries, are outside the scope of virtual environments. 
- Conclusion? Containers can fully encapsulate not just your training code, but the entire dependency stack down to the hardware libraries.
***

## Docker vs. VM
- Docker containers are not VMs. Containrs do not bundle a full operating system but only libraries and settings required to make the software work. 
- This creates an efficient, light-weight, self-contained system which guarantees that the software will always run the same, regardless where it is deployed.
***

## Docker image and container
- **Docker image** is a kind of ready-to-use software read-only template crafted with source codes, libraries, external dependencies, tools, and other miscellaneous files that are needed for any software application to run successfully on any platform or OS. Often referred to as snapshots, representing the app and its virtual environment at a specific point in time. A docker image can’t be executed by themselves and cannot run or start.
- **Docker container** is nothing but a box that has the ability to run the docker image templates. The moment you create a container using those immutable images you essentially end up creating a read-write copy of that filesystem (docker image) inside the given container. A container can also be considered as a cohesive software unit that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. 
- Being more specific:
  - The key difference between a Docker image Vs a container is that a Docker image is a read-only immutable template that defines how a container will be realized. A Docker container is a runtime instance of a Docker image that gets created when the `$docker run` command is implemented.  
  - Before the docker container can even exist docker templates/images are built using `$ docker build` CLI. 
  - Docker image templates can exist in isolation but containers can't exist without images.  
  - So docker image is an integral part of containers that differs only because of their objectives which we have already covered.  
  - Docker images can’t be paused or started but a Docker container is a run time instance that can be started or paused.

![image](https://user-images.githubusercontent.com/89139139/220131562-d523bc2b-0941-425c-9dd0-4ab02e89904c.png)
***

## Docker file vs. Docker image vs. Docker instance
- A **Docker File** contains the list of commands to run which are necessary for the application to run (like dependencies, codes, command to run etc.)
- A Docker Image** is a lightweight, standalone, executable package of software (built using dockerfile) that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings.
- A **Docker Container** is an instance of Docker Image which contains the running application.
***

## Containers and state
- As cloud computing and microservices grow in popularity, so too has containerisation of applications, whether stateful or stateless. Containers are units of code for an application that are packaged up, together with their libraries and dependencies, so that they’re able to be moved easily and can run in any environment, whether on a desktop, traditional IT infrastructure, or on a cloud. 
- Originally, containers were built to be stateless, as this suited their portable, flexible nature. But as containers have come into more widespread use, people began containerizing (redesigning and repackaging for the purposes of running from containers) existing stateful apps. This gave them the flexibility and speed of using containers, but with the storage and context of statefulness.
- Because of this, stateful applications can look a lot like stateless ones and vice versa. For example, you might have an app that is stateless, requiring no long-term storage, but that allows the server to track requests originating from the same client by using cookies. 
***

## Sharing your container
- You have two options:
  - **Container image**: This is the easiest option. This allows every collaborator or a cluster management service, such as Kubernetes, to pull a container image, instantiate it, and execute training immediately.
  - **Dockerfile**: This is a lightweight option. Dockerfiles contain instructions on what dependencies to download, build, and compile to create a container image. Dockerfiles can be versioned along with your training code. You can automate the process of creating container images from Dockerfiles by using continuous integration services, such as AWS CodeBuild.
***

## General workflow
The general workflow is:
  - Build an image `docker build` or download it from a registry `docker pull`.
  - Run a container from an image `docker run`.
  - A running container can be transformed in a new image `docker commit` and pushed to the registry `docker push`.
***

## Installation
- Follow [this](https://docs.docker.com/desktop/mac/install/) instruction for MacOS.
- Alternatively follows this:
```
$ sudo apt install docker.io
$ sudo systemctl start docker
$ sudo systemctl enable docker 
$ sudo groupadd docker
$ sudo usermod -aG docker $USER 
```
- Validate the installation of Docker on your workstation with the following command: `docker --version`
***

## Docker storage layers
- A useful concept behind Docker images is storage layers. Building an image means installing almost a whole dedicated Linux OS for your package. To avoid running this operation every time, Docker uses a layered filesystem. Here is how it works: if the first layer contains Files A and B, and the second layer adds File C, the resulting filesystems show A, B, and C. If we want to create a second image that uses Files A, B, and D, we only need to change the second layer to add File D. This means that we can have base images that have all the basic packages, and then we can focus on changes specific to your image, 
***

## Python dependencies
- An alternative to `pip freeze` is `pipreqs`. The first outputs all the installed packages in that environment, whereas `pipreqs path/to/project` gives you only the ones actually imported by this project.
- To install all the modules in the `requirements.txt` file use: `pip install --no-cache-dir -r requirements.txt`: 
  - `-r` tells pip that the next name is a requirements file
  - `--no-cache-dir` is used if you don't have space on your hard drive or you previously run pip install with unexpected settings which you do not want to include anymore and finall if you want to keep a Docker image as small as possible. What is cache directory used for? It used to store the installation files(.whl, etc) of the modules that you install through pip and to store the source files (.tar.gz, etc) to avoid re-download when not expired 
***

## Docker CLI flags
- `--rm`: will remove the container as soon as it is stopped;
- `-d`: will run the container in the background (docker run exits immediately);
- `-it`: will run the container in an interactive mode (you can use a terminal inside the container).
- `v`: mounts a Docker volume or host directory into the container—for example, a directory containing datasets.
***

## Dockerfile
- `FROM`: Defines the base container to build from. There are many base containers available to download, such as `ubuntu`, `python3.6`. It tells Docker to pull this image from a registry, in our case DockerHub, and use it as the base image.
- `RUN`: Runs bash command. This is where we want to do package installations, directory creation, etc. Because each line will create a layer in the image, it’s good to have shared and general package installations in the first lines in Dockerfile. This means that during rebuilds, Docker will try to use layers from the cache.
- `ARG`: The `ARG` directive in Dockerfile defines the parameter name and defines its default value. You can use `ARG` values to set `ENV` values.
- `COPY`: Copies files from a context. The context is a set of local files that are exposed to Docker during the build, and it only uses them in the process of building the image. This can be used to copy your source code or data to a container.
- `ENV`: Sets an environment variable. This variable will be part of the image and will be visible in build and run.
- `CMD`: This is the default command for a container. A good practice in Docker is to run one command per container.
- `WORKDIR`: The default directory in the image. This will be the directory the default command will be run from.
- `EXPOSE`: Specifies ports the container will use.
***

## List of commands
- `docker images`: list all images in your system
- `docker run CONTAINER --network NETWORK`: create a container
- `docker start CONTAINER NAME`: start a stopped container
- `docker stop`: stop a running container: 
- `docker ps`: list all running containers
- `docker ps -a`: list all containers including stopped ones
- `docker inspect CONTAINER ID`: inspect the container configuration. For instance network settings and so on: 
- `docker network ls`: list all available virtual networks
- `docker network create NETWORK --driver bridge`: create a new network
- `docker network connect NETWORK CONTAINER`: connect a running container to a network
- `docker network disconnect NETWORK CONTAINER`: disconnect a running container from a network
- `docker network rm NETWORK`: Remove a network: 
- `docker rm <id>`: remove a container
- `docker rmi <id> (or name)`: remove an image
- ```docker container prune``` or ```docker system prune```: Remove all unused containers / images
- `docker logs`: Displays the STDOUT and STDERR information produced by a container, which is very useful for debugging.
- `docker exe container_id bash`: allows you to enter inot a container environment with bash and examine it from inside.
***

## Container nomenclature
- **Docker registries** is where the images are published. The default Docker registry, called DockerHub, is supported by Docker, Inc. An account on DockerHub is free and lets you push public images to it.
- **Docker images** are read-only templates used to build containers. 
- **Docker containers** are deployed instances created from those templates.
- **Nodes** are physical or virtual machines or a physical machine running an instance of the Docker Engine. Typically you have several nodes in a cluster; in a learning or resource-limited environment, you might have only one node.
- **Pods** is where the containers are placed to then be run on a nodes
***

## Docker compose
- Docker Compose is a tool that was developed to help define and share multi-container applications. With Compose, we can create a YAML file to define the services and with a single command, can spin everything up or tear it all down.
- The key difference between docker run versus docker-compose is that docker run is entirely command line based, while docker-compose reads configuration data from a YAML file. The second major difference is that docker run can only start one container at a time, while docker-compose will configure and run multiple.
- As cloud-native environments increase in complexity, docker run commands will become unmanageable long. When a docker run command becomes more than seventy or eighty characters in length, it makes more sense to configure the container in a `docker-compose.yaml` file.
***

## Docker sharing
- Sharing can be done in 2 ways:
  - Commit the Dockerfile which can be used to build the image and container
  - Push the docker image to a central repository like Docker Hub and pull it from there. You need to create an account in docker hub in-order to be able to push the image. 
***

## Available tutorials
- [Model training and serialisation with Docker and Flask](https://github.com/kyaiooiayk/Docker-Notes/tree/main/tutorials/Docker_and_Flask)
- [Building an Ubuntu and Python Docker Image](https://github.com/kyaiooiayk/Docker-Notes/tree/main/tutorials/Building_Ubuntu_and_Python_Docker_Image)
- [How to build a simple Ubuntu Docker image](https://github.com/kyaiooiayk/Docker-Notes/blob/main/tutorials/Simple_ubuntu_docker.md)
***

## References
- [How to Dockerize Any Machine Learning Application](https://towardsdatascience.com/how-to-dockerize-any-machine-learning-application-f78db654c601)
- [Why use Docker containers for machine learning development?](https://aws.amazon.com/blogs/opensource/why-use-docker-containers-for-machine-learning-development/)
- [Bried presentation on what Docker is](https://docs.google.com/presentation/d/1r7SbbajL-UnYHOeY9fQ9YtoJdu9Q70U5M_11E68K1Rg/edit#slide=id.gbd509e17c2_0_807)
- [Stateful and stateless](https://www.redhat.com/en/topics/cloud-native-apps/stateful-vs-stateless)
- [Docker cheatsheet](https://dockerlabs.collabnix.com/docker/cheatsheet/)
- [Introduction to Microservices, Docker, and Kubernetes](https://www.youtube.com/watch?v=1xo-0gCVhTU)
- Hapke, Hannes, and Catherine Nelson. Building machine learning pipelines. O'Reilly Media, 2020.
- [Docker Image vs Container: The Key Differences](https://www.knowledgehut.com/blog/devops/docker-vs-container)
- [Docker run vs docker-compose: What's the difference?](https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Docker-run-vs-docker-compose-Whats-the-difference)
- [MLOps Basics [Week 5]: Model Packaging - Docker](https://www.ravirajag.dev/blog/mlops-docker)
***
