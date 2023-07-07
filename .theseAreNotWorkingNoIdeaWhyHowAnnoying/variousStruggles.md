I've been slowed sufficiently with confusing things such as the docker daemon complaining until I finally figured out to simply restart Docker Desktop whenever my laptop has been in hibernate for long. A lot of time was lost thinking it was a hardware clock issue, various attempts at fixing the hardware clock and research into that yielded many insufficient results.

## other things
- the output when pushing new docker images seemed to have some reference to old docker images, so I deleted all recently made docker images of twoge both locally and on dockerhub repo. Seems to have done the trick as now I'm only seeing "Pushed" next to everything instead of a reference to a previous build
- tried making sqlalchemy_db_uri work initially, this took up a lot of time trying to debug as well
- docker desktop or minikube (not sure which) seems to struggle a lot and also takes a lot of time debugging

## from here down is more-or-less the same as the other readme, perhaps in it's infancy with a few updates

# How to K8s for an Application
## Currently have:
- docker db image and container running
- docker app container built, I'm not suppose to have a docker app container lol I'm supposed to do it with k8s. Oh but I need to put the image up on Dockerhub in order to do K8s things right?

## create an empty directory for the project on local machine and cd into it
## clone developer's application from their repo
    - If there is not a pre-existing Docker image do the following:
        1. create (2? another for db for minikube?) Dockerfile (link to Dockerfile coming soon when we figure out the markdown) which handles the following:
            - base OS image (python:alpine)
            - ??? connection to db?
            - libraries necessary (bash, postgresql-libs, etc)
            - 3rd party dependencies in requirements.txt
            - start the application in CMD 
            - The Docker image for the app is at matthawkiit/twoge
            - ###################
            - setup docker container for db (without Dockerfile version): 

docker run -itd --network host --name postgres-container -e POSTGRES_USER=mattyyy -e POSTGRES_PASSWORD=Happy123# -e POSTGRES_DB=twoge_db postgres:latest 

don't think i need this application container for the current project 
docker run -d -p 5432:5432 --name postgres-container postgres-flask

        2. build docker image (when building the docker image it can be built setting env vars two different ways)
            - ...with Elastic Beanstalk & using os.environ.get() within app.py
            - ...or via config.cfg & using from_pyfile() within app.py
            - the Docker image can be tested at this point??? But where's the actual db, ok
##############################################################################
##############################################################################
        3. refer to this Docker image when writing Kubernetes yaml files in the next steps
    - You might've been able to use the developer's image that already exists on Dockerhub if there was the option :p
## Need yaml code for at least 5 separate things (the 6th thing, a secrets file is also very useful to have for security purpose):
    1. application deployment
    2. application service
    3. database deployment
    4. database service
    5. database configmap
    6. database secrets



