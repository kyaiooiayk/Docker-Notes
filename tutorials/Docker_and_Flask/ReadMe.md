# How to use Docker
- In this tutorial we will:
    - Train a model
    - Serialise the model
    - Create an app with Flask
    - Create a Docker image for our app
    
The main goal of the tutorial is to see how a Docker image is built. The ML model is not tuned and it effectively serves as a dummy model.
***


## Step-by-step guide to create your own Docker image
- Step #1: Save all the packages in the file with: `pip freeze > requirements.txt`. It’s a good practice to list the exact version of the library rather than `><`, but this does not seem to have stuck among practioners. The file looks like something like this:
```
joblib==0.16.0
numpy==1.19.1
pandas==1.1.0
pandas-profiling==2.8.0 ▪ scikit-learn==0.23.2
....
```
- Step #2: Write a Dockerfile file named `Dockerfile` that can be used to build the required virtual environment for our app to run on. Every Dockerfile has to start with a `FROM`. What follows FROM must be an already existing image (either locally on your machine or from the DockerHub repository). Since our environment is based on Python, we use python:3.7 as our base image and eventually create a new image using this Dockerfile. Streamlit runs on a default port of 8501. So for the app to run, it is important to expose that particular port. We use the EXPOSE command for that. WORKDIR sets the working directory for the application. The rest of the commands will be executed from this path. Here COPY command copies all of the files from your Docker client’s current directory to the working directory of the image. RUN command ensures that the libraries we defined in the requirements.txt are installed appropriately. CMD specifies what command to run within the container as it starts. Hence, streamlit run app.py ensures that the Streamlit app runs as soon as the container has spun up.
```
FROM python:3.7
EXPOSE 8501
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD python run app.py
```
- Step #3: Build it and create an image. The idea is this image we create is the reproducible environment irrelevant to the underlying system. (The dot at the end signifies the path for the Dockerfile, which is the current directory.): `docker build --tag app:1.0 .`

- Step #4: Run command runs the specified container on the host machine. `--publish 8501:8501` lets the port 8501 of the container to be mapped to the port 8501 of the host machine, while `-it` is needed for running interactive processes (like shell/terminal): `docker run --publish 8501:8501 -it app:1.0` What do I do with it? You can share the built images on DockerHub or deploy them on the cloud, and so on. The syntax `8501:8501` means `Host port:Container port`.

- A slightly different procedures is this:
  - Once we have created the docker file, we need to build it. Building the docker container is a very simple command: `docker build -f Dockerfile -t yourNameForTheDockerContainer`
  - Now, you can log into the container using the following command: `docker run -ti yourNameForTheDockerContainer /bin/bash`
  - You need to remember that whatever you do in this shell will be lost once you exit the shell. And you can run the training inside the docker container using: `docker run -ti yourNameForTheDockerContainer python3 nameOfYourPythonFile`
***

## Step-by-step tutorial
- **Step #1** Train the model by running the `Model_training_and_serialisation.ipynb` notebook
- **Step #2** Run: `python flask_api.py` and past this address on your browser `http://192.168.1.15:8000/`
- **Step #3** Copy and past this address on the browser `http://192.168.1.15:8000/predict?variance=2&skewness=2&curtosis=2&entropy=1`. Look at the structure of the URL. Fisrt of all there is an `?` soon after the `predict` and then the syntax `variance=2` means that we'd like to send a request for where `variance` is assigned value 2.
- **Step #3** Copy and past this address on the browser `http://192.168.1.15:8000/predict_file?file_name=TestFile.csv` which will then returns the predicted class for the instances listed in the .csv file.
Creation of a front end
- **Step #4** Write a Docker file. This is called `Dockerfile`. Build with: `docker built -t money_api .` which essentially create a Docker image called `money_api` locally, meaning for where you ae running the command line.
- **Step #5** Load the Docker image as: `docker run -p 8000:8000 money_api` and it will give you a message like this `Running on http://172.17.0.2:8000/ (Press CTRL+C to quit)`. Now if you copy and paste this it will not work. What you have to do is paste this instead `http://localhost:8000/`. If you are on a MacOS PC and you have installed Docker, click on `OPEN IN BROWSER` one the Docker image shown.
***

## Troubleshooting
- If you get an erro like this: `OSError: [Errno 48] Address already in use` then follow this [link](https://ishaileshmishra.medium.com/the-python-flask-problem-socket-error-errno-48-address-already-in-use-4d074847587e) to resolve it. Essentially this appens, when you are the app for the first time, you kill the python thread and while relaunching it again.
- Option #1:
   - Try to locate the PID of the process with: `ps -fA | grep python`
   - Then kill the PID with: `kill PID`
   - If the one above does not work try: `kill -s KILL <pid>` or `kill -9 <pid>`
- Option #2:
   - Locate the PID with: `lsof -i:8050`
   -  Then shut the process with: `kill PID`
***

## References
- [YouTube video tutorial](https://www.youtube.com/watch?v=ipFUANeStYE)
- [GitHub code](https://github.com/krishnaik06/Dockers)
- [Dataset Link](https://www.kaggle.com/ritesaluja/bank-note-authentication-uci-data)
