# Open WebUI Playground

A portable, optimized Docker Compose environment for running [Open WebUI](https://github.com/open-webui/open-webui) with optional local text-to-speech capabilities.

This project provides a complete, ready-to-use solution for running Open WebUI locally with HTTPS support, reverse proxy configuration, and optional TTS integration.

## Features

- **Open WebUI**: Full web interface for interacting with LLMs
- **HTTPS Support**: Secure connection with SSL/TLS encryption
- **Nginx Reverse Proxy**: Optimized proxy configuration with WebSocket support
- **Local TTS**: Optional integration with kokron-web for local text-to-speech
- **Cross-Platform**: Works on various operating systems with Docker
- **Docker Compose**: Single command deployment and management

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Quick Setup

1. **Generate SSL certificates** (if needed):
   ```bash
   ./gen_private_key.sh
   ```

2. **Start the application**:
   ```bash
   docker compose up -d
   ```

3. **Access the application**:
   - Open WebUI: https://localhost
   - Kokoro-web TTS API: http://localhost:3000/v1/audio/speech

## Project Structure

```
openwebui-playground/
├── compose.yml              # Docker Compose file
├── .env                     # Main environment variables
├── open-webui.env           # Open WebUI specific environment variables
├── kokoro.env               # Kokoro-web service environment variables
├── gen_private_key.sh       # Script to generate self-signed SSL certificates
├── README.md                # This file
├── ssl/                     # SSL certificate directory
│   ├── cert.pem             # SSL certificate file
│   └── key.pem              # SSL private key file
├── nginx/                   # Nginx configuration directory
│   └── conf.d/
│       └── open-webui.conf  # Nginx server block configuration
└── kokoro-cache/            # Directory for Kokoro-web TTS cache
```

## Services

### Open WebUI
- Runs the Open WebUI web application
- Exposed via Nginx reverse proxy on port 443 (HTTPS)
- Data persistence through Docker volumes
- Includes features for image generation, notes, and memories

### Nginx Reverse Proxy
- Provides HTTPS termination with SSL certificates
- Proxies requests to Open WebUI service
- Includes WebSocket support for streaming responses
- Optimized performance with Gzip compression

### Kokoro-web TTS (Optional)
- Provides local text-to-speech capabilities
- Compatible with OpenAI TTS API
- Runs on port 3000
- Uses `kokoro-cache/` directory for model storage

## Configuration

### Environment Variables

The project uses environment files for configuration:

- `.env`: Main project environment variables
- `open-webui.env`: Open WebUI specific settings
- `kokoro.env`: Kokoro-web service settings

Environment variable for Kokoro-web:
- `KW_SECRET_API_KEY`: Required for TTS API authentication

### SSL Certificates

The project expects SSL certificates in the `ssl/` directory:
- `cert.pem`: SSL certificate file
- `key.pem`: SSL private key file

If you don't have certificates, use `gen_private_key.sh` to generate self-signed certificates.

## Usage

### Starting Services
```bash
docker compose up -d
```

### Stopping Services
```bash
docker compose down
```

### Accessing Applications

#### Open WebUI
- Access at: https://localhost
- Browser will warn about self-signed certificate
- Manually trust the certificate to proceed

#### Kokoro-web TTS API
- Available at: http://localhost:3000/v1/audio/speech
- Authenticate with: `Authorization: Bearer <your-secret-key>`
- Compatible with OpenAI TTS API format

## Troubleshooting

### Certificate Issues
If you encounter certificate warnings in the browser:
1. Navigate to https://localhost
2. Click "Advanced" or "Proceed to localhost (unsafe)"
3. Add an exception for the self-signed certificate

### Port Conflicts
If ports 443 or 3000 are in use:
1. Change the port mappings in `compose.yml`
2. Update Nginx configuration accordingly

### Service Not Starting
Check service logs:
```bash
docker compose logs <service-name>
```

## Security Notes

- Uses self-signed certificates by default
- TTS API requires authentication with secret key
- All data is stored in persistent volumes
- Access to services is limited by port exposure

## License

This project is available under the MIT License.