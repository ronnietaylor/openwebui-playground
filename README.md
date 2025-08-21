# Open WebUI with Nginx Reverse Proxy

This project sets up [Open WebUI](https://github.com/open-webui/open-webui) behind an Nginx reverse proxy using Docker Compose. It includes configuration for HTTPS using self-signed certificates. It also provides an optional [kokoro-web](https://github.com/eduardolat/kokoro-web) service for local text-to-speech (TTS).

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

3.  **Kokoro-web (Local TTS Service) [Optional]:**
    *   The `kokoro-web` service provides a local OpenAI-compatible TTS API.
    *   It is included in `docker-compose.yml` and runs on port `3000` by default.
    *   You must set the `KW_SECRET_API_KEY` environment variable in `docker-compose.yml` to a secret key of your choice. This key will be required to authenticate API requests.
    *   The service uses the `kokoro-cache/` directory to store downloaded TTS models and cache files.

## Running the Application

1.  Navigate to the project directory (`openwebui-playground`) in your terminal.
2.  Start the services using Docker Compose:
    ```bash
    docker compose up -d
    ```
    This command builds (if necessary) and starts the `open-webui`, `nginx`, and (optionally) `kokoro-web` containers in detached mode.


3.  **Access Open WebUI:**
        *   Open your web browser and navigate to `https://localhost`.
        *   You will likely encounter a security warning due to the self-signed certificate. Accept the risk or add an exception to proceed.

4.  **Using Kokoro-web TTS API:**
        *   The TTS API will be available at `http://localhost:3000/v1/audio/speech`.
        *   Authenticate requests using the `Authorization: Bearer <your-secret-key>` header, where `<your-secret-key>` matches the value of `KW_SECRET_API_KEY`.
        *   The API is compatible with the OpenAI TTS API. Example request (using `curl`):
                ```bash
                curl -X POST http://localhost:3000/v1/audio/speech \
                    -H "Authorization: Bearer <your-secret-key>" \
                    -H "Content-Type: application/json" \
                    -d '{
                                "model": "kokoro-medium",
                                "input": "Hello, this is a test.",
                                "voice": "en_us_001"
                            }' --output output.wav
                ```

## Stopping the Application

1.  To stop the running containers:
    ```bash
    docker compose down
    ```


## Project Structure

*   `docker-compose.yml`: Defines the `open-webui`, `nginx`, and `kokoro-web` services, their configurations, volumes, and network.
*   `nginx/conf.d/open-webui.conf`: Nginx server block configuration for proxying requests to Open WebUI and enabling SSL.
*   `ssl/`: Directory containing the SSL certificate (`cert.pem`) and private key (`key.pem`).
*   `kokoro-cache/`: Cache directory for kokoro-web TTS models and files.
*   `gen_private_key.sh`: (Optional/Example) A script to generate self-signed certificates.
*   `README.md`: This file.
