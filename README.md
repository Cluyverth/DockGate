# DockGate

This project provides a ready-to-use, production-oriented Nginx reverse proxy setup for your VPS, fully containerized with Docker. It is designed to be cloned and deployed quickly, with minimal configuration required.

## Features
- Secure reverse proxy for your backend services
- HTTPS (SSL) support (bring your own certificates)
- Security headers and best practices
- No Nginx version or server info exposed on error pages
- Simple to extend for multiple domains/projects
- Dockerized for easy deployment

## Usage

1. **Clone this repository on your VPS:**
   ```sh
   git clone https://github.com/yourusername/nginx-reverse-proxy-boilerplate.git
   cd nginx-reverse-proxy-boilerplate/Nginx
   ```

2. **Add your SSL certificates:**
   - Place your `certificate.crt` and `private.key` in `Nginx/certs/` (create the folder if needed).
   - Update the `docker-compose.nginx.yml` volume section if your certs are elsewhere.

3. **Add a new project (service/domain):**
   - Copy the example config:
     ```sh
     cp http.d/99\ \-\ example.conf http.d/03-myproject.conf
     ```
   - Edit `03-myproject.conf`:
     - Change `server_name` to your domain.
     - Change the `proxy_pass` line to use your Docker Compose service name (e.g., `proxy_pass http://myproject:8080;` or `proxy_pass http://myproject`).
     - Optionally enable gzip/Brotli or add rate limiting by including the relevant snippets.

4. **Add your backend service to Docker Compose:**
   - In your main `docker-compose.yml`, add your service and ensure it is on the same network as Nginx (see comments in `docker-compose.nginx.yml`).
   - Example:
     ```yaml
     services:
       myproject:
         image: myproject:latest
         networks:
           - proxy
     networks:
       proxy:
         external: true
     ```

5. **Build and run the reverse proxy:**
   ```sh
   docker compose -f docker-compose.nginx.yml up -d
   ```

6. **Access your site:**
   - Nginx will listen on ports 80 and 443.

## Troubleshooting
- If your backend is not reachable, check that the service name in `proxy_pass` matches your Docker Compose service.
- Ensure both Nginx and your backend are on the same Docker network (`proxy`).
- Check container logs for errors: `docker logs nginx-reverse-proxy`

## Security Notes
- SSL and security headers are included by default. Adjust `snippets/ssl-params.conf` and `snippets/security-headers.conf` as needed.
- The Nginx container runs with minimal privileges (see `docker-compose.nginx.yml`).
- For extra protection, consider enabling rate limiting and caching via snippets.

## File Structure
- `nginx.conf` — Main Nginx config
- `http.d/` — Virtual host configs (add one per project/domain)
- `snippets/` — Security, SSL, and gzip configs
- `error_pages/` — (Optional) Custom error pages
- `Dockerfile.nginx` — Dockerfile for building the Nginx image
- `docker-compose.nginx.yml` — Compose file for running the proxy

## Security
- Nginx version and server info are hidden on all responses and error pages.
- Security headers are included by default.

## Extending
- Add new files in `http.d/` for each new project/domain.
- Mount additional volumes or update configs as needed.

## License
MIT
