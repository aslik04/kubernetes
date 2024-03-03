
# Variables
IMAGE_NAME := ghcr.io/your-github-username-here/hello:latest
CONTAINER_NAME := hello

# Build the Docker image
build:
	docker build -t $(IMAGE_NAME) .

# Publish the Docker image
publish:
	docker push $(IMAGE_NAME)

login:
	docker login ghcr.io -u aslik04

# Run the Docker container
run:
	docker run -d -p '5000:5000' --name $(CONTAINER_NAME) $(IMAGE_NAME)

# Stop and remove the Docker container
stop:
	docker stop $(CONTAINER_NAME)
	docker rm $(CONTAINER_NAME)

# Clean up the Docker image
clean:
	docker rmi $(IMAGE_NAME)
