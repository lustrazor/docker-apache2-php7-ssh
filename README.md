# Docker PHP Development Environment

A lightweight Docker environment for PHP development with Nginx, PHP-FPM, and SFTP access.

## Features

- PHP 7.4 with FPM
- Nginx web server
- SFTP access for file uploads
- Security-focused configuration
- Volume-based file management

## Quick Start

1. Clone this repository
2. Add your website to the public directory
3. Build and run
   ```bash
   docker-compose build --no-cache
   docker-compose up -d
   ```
   (or simply launch on Coolify)
4. Access your website at `http://localhost:8080`
5. Start SFTP service when needed: `docker-compose start sftp`
Alternatively, you can enable SFTP at deployment time.
```bash
docker-compose --profile sftp up -d --build
```
6. Upload files via SFTP to `public/`
7. Changes are immediate - no container restart needed
8. Stop SFTP service when done: `docker-compose stop sftp`


## Coolify Workflow

This repo is intended as an empty starting point. To add your website, either make your own repo from this one and add your website to the public directory, or simply launch this repo on Coolify. After that you can upload your site via SFTP to the public directory of the running container.

```bash
sftp -P 2222 your_username@yoursite.com
```

## Environment Variables

The SFTP service requires the following environment variables to be set
If running in Docker, set them in `.env` file
Otherwise, set them in the Coolify UI
```bash
SFTP_USER=your_username
SFTP_PASSWORD=your_secure_password
SFTP_PORT=2222
```

**Important**: SFTP service will not start if these variables are not set. This is a security measure to prevent running with default credentials.

## Starting SFTP Service

1. First ensure your `.env` file is configured
2. Start SFTP service with:
   ```bash
   docker-compose --profile sftp up -d
   ```

## SFTP Security

The SFTP service:
- Will not start without proper environment variables
- Is not started by default (requires explicit --profile flag)
- Should be stopped when not in use:
  ```bash
  docker-compose stop sftp
  ```

## File Upload Instructions

### Using SFTP Client (FileZilla, WinSCP, etc.)

1. Connect to SFTP:
   - Host: `localhost` (or your server IP)
   - Port: `2222` (or your configured SFTP_PORT)
   - Username: Your configured SFTP_USER
   - Password: Your configured SFTP_PASSWORD

2. File Paths:
   - Upload your website files to `/website/public/`
   - Files placed in `public/` will be immediately live
   - Example: `/website/public/index.php` will be accessible at `http://localhost:8080/index.php`

### Directory Structure
├── public/ # Web root - place your public files here
│ └── index.php # Example file
├── private/ # Place private files here (not web-accessible)
└── docker/ # Docker configuration files

### Security Notes

1. Change the default SFTP password before deploying to production
2. The `public` directory is web-accessible; keep sensitive files outside of it
3. Use the `private` directory for configuration files and non-public assets
4. **Important**: Stop SFTP service when not in use (see SFTP Security below)

## Accessing Your Website

- Web: `http://localhost:8080`
- SFTP: `sftp://localhost:2222` (when SFTP service is running)

## Dynamic Data

If your PHP scripts rely on writable files or folders, attach a Storage Volume or File Mount via Coolify's UI. For example: if your script records clicks to `clicks.txt` in `private/` then set the File Mount destination path to `/app/private/clicks.txt`

## Nginx Options
Edit this file: `/docker/nginx/default.conf`

**Important**: Content security is set to allow inline, so the php info page can load CSS and images from source. If your website doesn't rely on remote files to function, you should set content security to strict. Both variants are included in the default.conf file.

```
# Content Security Policy (CSP)
# Strict CSP - only allow resources from same origin
add_header Content-Security-Policy "default-src 'self'";

# Alternative CSP with support for inline styles and data URIs
# Useful for applications requiring inline styles or base64 images
# WARNING: Enabling 'unsafe-inline' reduces security
add_header Content-Security-Policy "default-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:;";
```

## Common SFTP Commands

Connect via command line
sftp -P 2222 username@localhost
Common SFTP commands
put localfile.php # Upload a file
get remotefile.php # Download a file
ls # List directory contents
cd public # Change directory
pwd # Show current directory

## Troubleshooting

1. SFTP Connection Issues:
   - Verify the correct port number
   - Check username/password in .env file
   - Ensure the SFTP container is running: `docker-compose ps`
   - Try starting SFTP service: `docker-compose start sftp`

2. File Permissions:
   - Uploaded files should automatically have correct permissions
   - If not, they should be readable by www-data group

3. Files Not Appearing on Website:
   - Verify files are in the `public` directory
   - Check nginx logs: `docker-compose logs nginx`
   - Check PHP logs: `docker-compose logs php`

## Development Workflow

1. Start SFTP service when needed: `docker-compose start sftp`
2. Upload files via SFTP to `/website/public/`
3. Changes are immediate - no container restart needed
4. Stop SFTP service when done: `docker-compose stop sftp`
5. Monitor logs with `docker-compose logs -f`

## Production Deployment Notes

Before deploying to production:
1. Change default SFTP credentials
2. Consider using SSH keys instead of password authentication
3. Review and adjust PHP and Nginx configurations as needed
4. Set appropriate file permissions
5. Always stop SFTP service when not in use
6. Consider using a firewall to restrict SFTP access to specific IP addresses
