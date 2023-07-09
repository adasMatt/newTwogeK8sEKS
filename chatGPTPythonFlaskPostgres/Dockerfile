#Thanks chatGPT

# For the chatGPT steps below, I think I actually have to change the endpoint for docker to push images to minikube as is being described, which seems like unecessary extra work that costs too much time with a deadline ahead right now.. decided to go the normal route and simply use my real dockerhub

# Apparently I can "2. Tag the Docker image: Tag the locally built image with a registry-like name. This step is required because Kubernetes expects images to have a registry-like name. For example:
# docker tag my-app-image:latest localhost:5000/my-app-image:latest
# Note: localhost:5000 is used as an example, but you can choose any registry-like name as long as it is accessible within your Kubernetes environment."
#
# Then I can "3. Load the Docker image into Minikube: Minikube has a built-in Docker daemon that you can use to load the locally built image into its registry. Run the following command to load the image:
# minikube image load localhost:5000/my-app-image:latest"

# and next make sure the yaml files are using the correct image on localhost "4..." self explanatory no need for chatGPT guidance on that one

# what is "5.."

# Base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy application files
COPY requirements.txt .
COPY app.py .

# Install dependencies, for some reason there is an error with sqlalchemy, added a second line of commands here
RUN pip install --no-cache-dir -r requirements.txt \
    && pip install --no-cache-dir flask_sqlalchemy

# Set environment variables
ENV FLASK_APP=app.py

# Expose port
EXPOSE 5000

# Run the application
CMD ["flask", "run", "--host=0.0.0.0"]
