# n8n Docker Project

## Overview
This project sets up a local development environment for n8n using Docker. It includes a sample workflow, Docker configuration files, and environment settings to facilitate easy development and deployment.

## Project Structure
```
n8n-docker-project
├── src
│   └── workflows
│       └── example-workflow.json
├── docker
│   ├── Dockerfile
│   └── docker-compose.yml
├── .env.example
├── README.md
```

## Getting Started

### Prerequisites
- Ensure you have Docker and Docker Compose installed on your machine.

### Local Development Setup
1. **Clone the Repository**
   Clone this repository to your local machine.

2. **Navigate to Project Directory**
   Open a terminal and navigate to the project root directory:
   ```
   cd n8n-docker-project
   ```

3. **Configure the Environment**
   Copy the example environment file and adjust the values for your setup (make sure `N8N_ENCRYPTION_KEY` is a random, private string—`openssl rand -hex 16` is one way to generate it—and that `HOST_UID`/`HOST_GID` match your local user):
   ```bash
   cp .env.example .env
   ```
   Edit `.env` with your preferred values.

4. **Create the Docker Environment**
   Run the following command from the project root to build the Docker image and start the n8n application along with any defined services:
   ```bash
   docker compose -f docker/docker-compose.yml up -d
   ```

### Accessing n8n
Once the containers are up and running, you can access the n8n interface by navigating to `http://localhost:5678` in your web browser.

### Data Directory Permissions
Before the `n8n` container starts, a lightweight helper service fixes the ownership of the `docker/data` directory using the `HOST_UID` and `HOST_GID` values from `.env`. Adjust these values if your local user uses a different UID/GID to avoid permission errors such as `EACCES: permission denied, open '/home/node/.n8n/config'`.

## Workflow Example
The project includes a sample workflow located at `src/workflows/example-workflow.json`. This file defines the nodes and connections for automation tasks.

## Configuration
- The `.env` file contains environment variables for configuring the n8n application, such as database connection strings, encryption key, and host UID/GID used to set the proper permissions on the shared data directory.
- The `docker/Dockerfile` defines the instructions for building the Docker image for the n8n application.
- The `docker/docker-compose.yml` file is used to define and run multi-container Docker applications.

## Contributing
Feel free to submit issues or pull requests for improvements or bug fixes.

## License
This project is licensed under the MIT License.
