This is a clear set of deployment steps. Here is a rewritten, more comprehensive deployment document, including best practices, environment checks, and explicit restart commands for a cleaner, more reliable process.

## Comprehensive Application Deployment Guide (Linux Server)

This document outlines the steps for deploying a Full-Stack application, consisting of a **.NET Backend (BE)** and a **Yarn/Node Frontend (FE)**, onto a Linux server environment.

### Prerequisites

  * You have SSH access and necessary credentials (or private key) for the deployment user.
  * The deployment user has `sudo` privileges to switch to the `root` account (`sudo su -`).
  * The .NET Runtime and Yarn/Node environments are correctly set up on the server.
  * The application directories are defined:
      * **Backend Path:** `/opt/dotnet-apps/moneymanagerapi/`
      * **Frontend Path:** `/var/www/moneymanagerapp/`
  * The Backend service (e.g., Kestrel, running via a Systemd service) can be restarted.

-----

### Deployment Procedure

#### Phase 1: Preparation and Backend Deployment

1.  **Log in to the Linux Server (Deployment User)**

    ```bash
    ssh user@your.server.ip
    ```

2.  **Pull the Latest Code**
    Navigate to the source code repository directory and pull the latest changes for both the Frontend and Backend.

    ```bash
    cd /path/to/source/code/repo
    git pull origin main
    ```

3.  **Switch to Root Account**
    Deployment actions (publishing, copying, and service restarts) require root or elevated privileges.

    ```bash
    sudo su -
    # You are now the root user
    ```

4.  **Stop the Backend Service (Recommended)**
    Stopping the service prevents file access conflicts during the copy process.

    ```bash
    systemctl stop moneymanagerapi.service 
    # Replace 'moneymanagerapi.service' with your actual service name
    ```

5.  **Publish the .NET Backend**
    Navigate to the Backend project directory and publish the application, preparing it for deployment.

    ```bash
    cd /path/to/source/code/repo/MoneymanagerApiProject 
    # Use your actual project directory

    dotnet publish -c Release --property:PublishDir=publish
    ```

6.  **Deploy the Backend Files**
    Copy the published files (`publish/*`) to the dedicated application directory, overwriting old files.

    ```bash
    cp -r publish/* /opt/dotnet-apps/moneymanagerapi/
    ```

#### Phase 2: Frontend Deployment

1.  **Build the Frontend**
    Navigate to the Frontend project directory and prepare the environment.

    ```bash
    cd /path/to/source/code/repo/MoneymanagerAppFrontend
    # Use your actual frontend directory

    # 1. Install ALL dependencies (Prod and Dev) to make 'tsc' and 'vite' available for the build process.
    yarn install 

    # 2. Run the build command (which executes "tsc && vite build"). Vite automatically builds for production.
    yarn build

    # 3. Clean up the node_modules directory by removing all development dependencies.
    yarn workspaces focus --production
    ```

2.  **Deploy the Frontend Files**
    Copy the newly built, optimized assets from the `dist` folder to the web server's root directory.

    ```bash
    cp -r dist/* /var/www/moneymanagerapp/
    ```

#### Phase 3: Finalization and Restart

1.  **Restart the Backend Service**
    Start the .NET service to load the new application code.

    ```bash
    systemctl start moneymanagerapi.service
    ```

    *Self-Check:* Verify the service started successfully:

    ```bash
    systemctl status moneymanagerapi.service
    ```

2.  **Exit the Root Session**
    Return to the non-privileged deployment user account.

    ```bash
    exit
    # You are now back to your normal user account
    ```

3.  **Deployment Complete**
    Verify the changes in a web browser. If the Frontend uses a reverse proxy like Nginx or Apache, the service should immediately reflect the new files.

-----