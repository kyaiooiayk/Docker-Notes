# How to create a simple Docker image/container for Ubuntu
***

## How to create an image
- Make sure docker is running in the background
- Create a simple “ubuntu” container image that includes a minimal ubuntu installation with: `$ docker run ubuntu`
- To see this image in lis to available images run: `$ docker image`
- However please note that if we were to use `$ docker ps` nothing will appear because, we have created an image not a container. Keep in mind that a container is an instance of an image.
- If we’d like to enter the container, we can run the exact same command with an `–it` argument: `$ docker run -it ubuntu`. Once we've done that, a bash terminal that gives us access to the container appears. Here we can do whatever the heck we want. We can install things, execute software, and almost everything we do in a normal system. To exit the terminal, just type “exit”.

## How to create a container
- If you want to run a container without building an image (which means without creating a Dockerfile), you need to use an existing image on the [Docker Hub](https://hub.docker.com/search?q=&type=image) with `$ docker run ubuntu`.
- You can now see the container with: `$ docker ps`. If this does not work try: `$ docker ps -l` where `l` stand for latest. Sometimes an `a` as in all is used as well, but this will show stopped and running containers.
***

## References
- [How to use Docker containers and Docker Compose for Deep Learning applications](https://theaisummer.com/docker/)
- [How to start your docker daemon](https://stackoverflow.com/questions/44678725/cannot-connect-to-the-docker-daemon-at-unix-var-run-docker-sock-is-the-docker)
***
