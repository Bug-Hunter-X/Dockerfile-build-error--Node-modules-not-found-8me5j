# Dockerfile Bug: Missing Node Modules

This repository demonstrates a common error in Dockerfiles: failing to copy dependencies correctly before running the application.

The original `Dockerfile` attempts to copy the entire project directory after installing dependencies. This is inefficient and leads to errors because the node modules folder is missing at runtime.  The corrected `DockerfileFixed` shows the proper way to handle this situation.

## Bug

The initial `Dockerfile` will produce an error because the `node_modules` directory is created within a temporary layer.  The subsequent `COPY . .` command overwrites the project directory, removing the `node_modules` directory and resulting in runtime errors.

## Solution

The solution is to copy the `package.json` and `package-lock.json` files *before* running `npm install`. This ensures that the dependencies are installed within the image.  Subsequently, copying the rest of the project files guarantees that the application's code is available.

## How to Reproduce the Bug

1. Clone this repository.
2. Build the original `Dockerfile` using `docker build -t buggy-app .`
3. Attempt to run the container using `docker run buggy-app` (this will fail)
4. Build the fixed `DockerfileFixed` using `docker build -t fixed-app -f DockerfileFixed .`
5. Run the fixed container using `docker run fixed-app` (this will run successfully)