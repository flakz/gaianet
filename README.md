# Gaianet Node Setup Guide

This repository provides a step-by-step guide to set up and run a Gaianet node inside a Docker container. Easily follow the instructions to build, run, and manage your node with resource limits for optimal performance. Contributions are welcome!

## Prerequisites

- Docker installed on your system
- Basic knowledge of Docker and terminal commands

## Steps to Set Up Gaianet Node

### 1. Create a Dockerfile

Create a file named `Dockerfile` in your project directory with the following content:

```dockerfile
# Use an official Ubuntu image as the base
FROM ubuntu:24.04

# Update and install necessary packages
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y curl lsof

# Download and install Gaianet node
RUN curl -sSfL 'https://github.com/GaiaNet-AI/gaianet-node/releases/latest/download/install.sh' | bash

# Manually add Gaianet CLI tool to PATH and source the environment
RUN echo 'export PATH=$PATH:/root/gaianet/bin' >> /root/.bashrc \
    && echo '. /root/.wasmedge/env' >> /root/.bashrc \
    && export PATH=$PATH:/root/gaianet/bin \
    && . /root/.wasmedge/env

# Initialize the Gaianet node with the config file from the URL
RUN /root/gaianet/bin/gaianet init --config https://raw.githubusercontent.com/GaiaNet-AI/node-configs/main/qwen2-0.5b-instruct/config.json

# Expose the port Gaianet runs on (replace with the correct port if different)
EXPOSE 3000

# Start the Gaianet node and keep the container running
CMD ["/bin/bash", "-c", "/root/gaianet/bin/gaianet start && tail -f /dev/null"]

```
### 2. Build the Docker Image
Open a terminal in the directory where the Dockerfile is located and run the following command to build the Docker image

```
docker build -t gaianet-node .
```
### 3. Run the Docker Container
Run the following command to start a container with the specified CPU and memory limits:

```
docker run -d --name gaianet-node-container --cpus="1" --memory="2g" -p 3000:3000 gaianet-node
```
### 4. Get Node Info
To retrieve the node ID and device ID, run the following command:
```
docker exec -it gaianet-node-container /root/gaianet/bin/gaianet info
```
### 5. Stopping the Gaianet Node
To stop the Gaianet node, run:
```
docker exec -it gaianet-node-container /root/gaianet/bin/gaianet stop
```
### 6. Additional Docker Commands
Check running containers:

```bash
docker ps
```
Check container logs:

```bash
docker logs gaianet-node-container
```
Stop and remove the container:

```bash
docker stop gaianet-node-container
docker rm 
```

