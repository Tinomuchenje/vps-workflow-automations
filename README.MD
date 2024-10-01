# Automated Workflows for VPS Management via GitHub Actions

This repository contains various GitHub Actions workflows to automate the management of a VPS. The repository is designed to be flexible, allowing multiple workflows to handle different tasks, such as deploying Nginx configurations, Docker containers, or other services.

## Features

- **Modular Workflow Structure**: The repository is structured to allow multiple workflows to automate various tasks on your VPS.
- **Automated Nginx Configuration**: One of the workflows handles deploying and updating Nginx configurations for subdomains.
- **Secure Management of Secrets**: Sensitive information like SSH credentials, subdomains, IP addresses, and ports are stored securely using GitHub Secrets.
- **Manual or Event-Based Triggers**: Workflows can be triggered manually or automatically on specific events (e.g., pushing code to a branch).

## Available Workflows

1. ** Redirecting subdomains to specific ports Deployment using Nginx Configuration **:
   - This workflow automates the creation and updating of Nginx configurations for routing subdomains to specific services on the VPS.
   - See [Nginx Configuration Deployment](#nginx-configuration-deployment) for detailed setup instructions.

_Additional workflows will be added here as the repository evolves._

## Prerequisites

Before using any workflow, make sure the following are set up:

1. **GitHub Repository**: You need access to this GitHub repository.
2. **VPS Server**: You should have a VPS server where your services (e.g., Nginx, Docker) are running, with SSH access configured.
3. **GitHub Secrets**: Each workflow will require certain secrets (e.g., SSH credentials, server details) to be added to your GitHub repository.

## Adding GitHub Secrets

1. Go to your GitHub repository.
2. Navigate to **Settings** > **Secrets and variables** > **Actions**.
3. Click **New repository secret** to add the required secrets. Each workflow will specify the secrets it needs.

---

## Nginx Configuration Deployment

This workflow automates the process of setting up and updating Nginx configurations for subdomains on your VPS. You can use this workflow to route subdomains to specific services running on different ports.

### Prerequisites for Nginx Workflow

1. **VPS Server with Nginx Installed**: Ensure Nginx is installed and running on your VPS. Nginx configuration files should be located in `/etc/nginx/sites-available/` and linked to `/etc/nginx/sites-enabled/`.
2. **GitHub Secrets**: Add the following secrets:
   - **SSH_PRIVATE_KEY**: Your private SSH key used to access the VPS.
   - **SSH_USERNAME**: The username for SSH login (e.g., `root` or another user).
   - **SERVER_IP**: The public IP address of your VPS.
   - **SUBDOMAIN**: The subdomain you want to configure (e.g., `docintel.dexruptivetech.com`).
   - **PORT**: The port number your service is running on the VPS (e.g., `4000`).

### How to Run the Nginx Workflow

This workflow is manually triggered from the GitHub Actions tab. Follow these steps to run it:

1. **Go to the GitHub Actions Tab**:

   - In your GitHub repository, click on the **Actions** tab.

2. **Select the Nginx Workflow**:

   - Find the workflow titled **Deploy and Update Nginx** and click on it.

3. **Run the Workflow**:

   - Click the **"Run workflow"** button to start the process. This will update the Nginx configuration on your VPS.

   ![Run Workflow Button](https://docs.github.com/assets/images/help/actions/workflow-dispatch-example.png)

4. **Monitor the Workflow**:
   - You can monitor the progress and logs of the workflow in the GitHub Actions interface.

---

## Adding More Workflows

This repository is designed to support multiple workflows. To add a new workflow:

1. **Create a New Workflow File**:

   - Add a new YAML file to `.github/workflows/` (e.g., `.github/workflows/deploy-docker.yml`).

2. **Define Workflow Triggers and Jobs**:

   - Set up the workflow to be triggered manually or automatically based on your needs.
   - Define the necessary steps (e.g., deploying Docker containers, managing services, running tests).

3. **Use GitHub Secrets**:

   - Make sure to reference any sensitive information (e.g., SSH keys, server IPs) using GitHub Secrets. Add any new secrets required for the new workflow.

4. **Document the Workflow**:
   - Update this `README.md` with instructions for the new workflow, including prerequisites, steps, and secrets required.

### Example of a New Workflow (Docker Deployment)

```yaml
name: Deploy Docker Service

on:
  workflow_dispatch: # Manual trigger

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2

      - name: Set up SSH
        uses: webfactory/ssh-agent@v0.5.3
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}

      - name: Deploy Docker Service
        run: |
          ssh ${{ secrets.SSH_USERNAME }}@${{ secrets.SERVER_IP }} << 'EOF'
            # Commands to deploy Docker containers
            docker-compose -f /path/to/docker-compose.yml up -d
          EOF
```

---

## Troubleshooting

- **SSH Connection Issues**: Make sure your VPS is accessible via SSH, and the SSH key and username in the GitHub Secrets are correct.
- **Nginx Configuration Test Fails**: If the Nginx configuration test fails, ensure that the configuration syntax is correct and there are no conflicts with existing configurations.
