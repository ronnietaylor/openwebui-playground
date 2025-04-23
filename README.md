# Open WebUI with Nginx Reverse Proxy

This project sets up [Open WebUI](https://github.com/open-webui/open-webui) behind an Nginx reverse proxy using Docker Compose. It includes configuration for HTTPS using self-signed certificates.

## Prerequisites

*   [Docker](https://docs.docker.com/get-docker/)
*   [Docker Compose](https://docs.docker.com/compose/install/)

## Setup

1.  **SSL Certificates:**
    *   This project expects SSL certificate (`cert.pem`) and private key (`key.pem`) files to be present in the `ssl/` directory.
    *   If you don't have existing certificates, you can generate self-signed ones. A sample script `gen_private_key.sh` might be provided or you can use tools like OpenSSL:
        ```bash
        openssl req -x509 -newkey rsa:4096 -keyout ssl/key.pem -out ssl/cert.pem -sha256 -days 365 -nodes -subj "/CN=localhost"
        ```
    *   **Note:** Browsers will likely warn you about self-signed certificates. You'll need to manually trust the certificate in your browser to proceed.

2.  **Nginx Configuration:**
    *   The Nginx configuration is located in `nginx/conf.d/open-webui.conf`. It's pre-configured to proxy requests to the Open WebUI container over HTTPS on port 443.

## Running the Application

1.  Navigate to the project directory (`openwebui-playground`) in your terminal.
2.  Start the services using Docker Compose:
    ```bash
    docker compose up -d
    ```
    This command builds (if necessary) and starts the `open-webui` and `nginx` containers in detached mode.

3.  **Access Open WebUI:**
    *   Open your web browser and navigate to `https://localhost`.
    *   You will likely encounter a security warning due to the self-signed certificate. Accept the risk or add an exception to proceed.

## Stopping the Application

1.  To stop the running containers:
    ```bash
    docker compose down
    ```

## Project Structure

*   `docker-compose.yml`: Defines the `open-webui` and `nginx` services, their configurations, volumes, and network.
*   `nginx/conf.d/open-webui.conf`: Nginx server block configuration for proxying requests to Open WebUI and enabling SSL.
*   `ssl/`: Directory containing the SSL certificate (`cert.pem`) and private key (`key.pem`).
*   `gen_private_key.sh`: (Optional/Example) A script to generate self-signed certificates.
*   `README.md`: This file.
