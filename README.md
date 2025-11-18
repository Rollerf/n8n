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
├── .env
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

3. **Create the Docker Environment**
   Run the following command to build the Docker image and start the n8n application along with any defined services:
   ```
   docker-compose up
   ```

### Accessing n8n
Once the containers are up and running, you can access the n8n interface by navigating to `http://localhost:5678` in your web browser.

## Workflow Example
The project includes a sample workflow located at `src/workflows/example-workflow.json`. This file defines the nodes and connections for automation tasks.

## Configuration
- The `.env` file contains environment variables for configuring the n8n application, such as database connection strings and API keys.
- The `docker/Dockerfile` defines the instructions for building the Docker image for the n8n application.
- The `docker/docker-compose.yml` file is used to define and run multi-container Docker applications.

## Contributing
Feel free to submit issues or pull requests for improvements or bug fixes.

## License
This project is licensed under the MIT License.