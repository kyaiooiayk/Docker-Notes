# Dockerize your Pipenv Python application
***

## Step #1
Chose an startingimage from the those provided by the Docker organisation that has Python 3.7 installed and is slimmed down. We give this image the name base and it will be used in two other build stages:
```Dockerfile
FROM python:3.7-slim AS base
```
***

## Step #2
Set some environment variables and prevent Python from generating `.pyc` files, and enable Python tracebacks on segfaults:
```Dockerfile
# Setup env
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONFAULTHANDLER 1
```
***

## Step #3
Build and install all python dependencies in the `python-deps` image, and later copy them into our runtime image. This limits the objects in the runtime image to only those needed to run the application:
```Dockerfile
FROM base AS python-deps
```
***

## Step #4
Install pipenv and we install gcc (not necessary in all the cases!):
```Dockertfile
# Install pipenv and compilation dependencies
RUN pip install pipenv
RUN apt-get update && apt-get install -y --no-install-recommends g
```
***

## Step #5
Install the dependencies in a new virtual environment specifinf the exact location `PIPENV_VENV_IN_PROJECT`:
```Dockerfile
# Install python dependencies in /.venv
COPY Pipfile .
COPY Pipfile.lock .
RUN PIPENV_VENV_IN_PROJECT=1 pipenv install --deploy
```
***

## Step #6
Build a new image called `runtime` image:
```Dockerfile
FROM base AS runtime
```
***

## Step #7
Copy in new virtual environment in the python-deps image, but not everything else we created in the process:
```Dockerfile
# Copy virtual env from python-deps stage
COPY --from=python-deps /.venv /.venv
ENV PATH="/.venv/bin:$PATH"
```
***

## Step #8
Running as the root user is a security risk, so we create a new user and use their home directory as the working directory:
```Dockerfile
# Create and switch to a new user
RUN useradd --create-home appuser
WORKDIR /home/appuser
USER appuser
```
***

## Step #9
- Copy in the application code from our source repository. By default all the files in our project will be copied which is neither efficient nor secure. see `.dockerignore`:
```Dockerfile
# Install application into container
COPY . .
```
***

## Step #10
Set the ENTRYPOINT which is the command that will be run when the image is run, and the CMD which is the default arguments that will be added to the command:
```Dockerfile
# Run the application
ENTRYPOINT ["python", "-m", "http.server"]
CMD ["--directory", ".", "8000"]
```
***

## The complete `Docker` file
```Dockerfile
FROM python:3.7-slim as base

# Setup env
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONFAULTHANDLER 1


FROM base AS python-deps

# Install pipenv and compilation dependencies
RUN pip install pipenv
RUN apt-get update && apt-get install -y --no-install-recommends gcc

# Install python dependencies in /.venv
COPY Pipfile .
COPY Pipfile.lock .
RUN PIPENV_VENV_IN_PROJECT=1 pipenv install --deploy


FROM base AS runtime

# Copy virtual env from python-deps stage
COPY --from=python-deps /.venv /.venv
ENV PATH="/.venv/bin:$PATH"

# Create and switch to a new user
RUN useradd --create-home appuser
WORKDIR /home/appuser
USER appuser

# Install application into container
COPY . .

# Run the application
ENTRYPOINT ["python", "-m", "http.server"]
CMD ["--directory", "directory", "8000"]
```
***

## `.dockerignore`
We can make a .dockerignore file to exclude files and directories from the image. To avoid forgetting to update it every time we add a new file that should be excluded I prefer to ignore everything by default, and then add back in files that are actually needed:
```
# Ignore everything
To avoid forgetting to update it every time we add a new file that should be excluded, some prefer to ignore everything by default, and then add back in files that are actually needed:
**
# ... except for dependencies and source
!Pipfile
!Pipfile.lock
!my_package/
```
***

## How to run
- Build and tag the image: `docker build . -t name:tag`
- Run the image: `docker run --rm name:tag arg1 arg2`
- Where:
  - `--rm` will delete the container once it has completed running
  - `arg1` and `arg2` will override the arguments that we provided in `CMD ["directory", ".", "8000"]`
***

## References
- [A perfect way to Dockerize your Pipenv Python application](https://sourcery.ai/blog/python-docker/)
***
