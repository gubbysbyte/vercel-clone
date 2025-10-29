# Vercel Clone

This project is a simplified, open-source clone of Vercel, designed to demonstrate a complete CI/CD pipeline for web applications. It allows users to deploy their frontend projects directly from a GitHub repository. The system automatically builds the project inside a Docker container, pushes the static assets to an AWS S3 bucket, and provides a unique preview URL for the deployed site.

## Architecture

The application is built on a microservices architecture, consisting of the following main components:

1.  **Frontend (Next.js)**: A web interface where users can submit the URL of a GitHub repository for deployment. It provides real-time feedback on the build and deployment process.
2.  **API Server (Node.js/Express)**: A backend service that accepts deployment requests, generates a unique ID for the project, and triggers the build process by running an AWS ECS task.
3.  **Build Server (Docker/Node.js)**: A containerized environment responsible for cloning the user's repository, installing dependencies, running the build command, and uploading the generated static files to AWS S3.
4.  **S3 Reverse Proxy (Node.js/Express)**: A reverse proxy that routes requests from the generated preview URLs (`<project-id>.localhost:8000`) to the corresponding static files stored in the S3 bucket.
5.  **Real-time Logging (Socket.io & Redis)**: A system for providing live build logs to the user. The Build Server publishes logs to a Redis channel, and the API Server (acting as a Socket.io server) broadcasts these logs to the connected frontend client.

### Architecture Diagram



## Features

- **Deploy from GitHub**: Enter a public GitHub repository URL to kick off the deployment.
- **Automated Builds**: Projects are automatically built in an isolated Docker environment on AWS ECS.
- **Static Asset Hosting**: Build outputs are stored in an AWS S3 bucket.
- **Unique Preview URLs**: Each deployment gets a unique subdomain for easy access.
- **Real-time Build Logs**: Watch the build process live from the web interface.

## Tech Stack

- **Frontend**: Next.js, React, TypeScript, Tailwind CSS, shadcn/ui, Socket.io Client, Axios
- **Backend**: Node.js, Express.js
- **CI/CD & Infrastructure**: AWS (ECS, ECR, S3), Docker
- **Real-time Communication**: Socket.io, Redis (Pub/Sub)

## Project Structure

```
vercel-clone/
├── api-server/         # Handles API requests, triggers builds, and manages WebSockets
├── build-server/       # Dockerized environment for building projects
├── frontend-nextjs/    # The Next.js user interface
└── s3-reverse-proxy/   # Routes preview URLs to S3 assets
```

## Prerequisites

Before you begin, ensure you have the following installed and configured:

- Node.js
- Docker
- Redis
- An AWS account with credentials configured locally.

## Local Setup Guide

1.  **Clone the Repository**

    ```bash
    git clone <your-repository-url>
    cd vercel-clone
    ```

2.  **Install Dependencies**

    Run `npm install` in each of the service directories:
    - `api-server`
    - `build-server`
    - `frontend-nextjs`
    - `s3-reverse-proxy`

3.  **Configure Environment Variables**

    You will need to set up your AWS credentials and configuration for the `api-server` and `build-server`. This includes:
    - AWS Region, Access Key ID, and Secret Access Key
    - AWS ECS Cluster and Task Definition ARNs
    - AWS VPC Subnets and Security Groups
    - Redis connection string

4.  **Build and Push the Build Server Image**

    Navigate to the `build-server` directory, build the Docker image, and push it to your AWS ECR repository.

5.  **Run the Services**

    - Start the frontend development server:
      ```bash
      cd frontend-nextjs && npm run dev
      ```
    - Start the API server:
      ```bash
      cd api-server && node index.js
      ```
    - Start the S3 reverse proxy:
      ```bash
      cd s3-reverse-proxy && node index.js
      ```

Your local environment is now ready!
- Frontend will be available at `http://localhost:3000`.
- API Server will be running on port `9000`.
- S3 Reverse Proxy will be running on port `8000`.
- Socket.io server for logs will be on port `9002`.