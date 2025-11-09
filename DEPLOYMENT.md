# Deployment Guide for Sirkbbir

This guide will help you deploy Sirkbbir to sirkbbir.com.

## Prerequisites

- Docker and Docker Compose installed on your server
- Domain name (sirkbbir.com) pointing to your server's IP address
- Port 80 and 443 open on your server

## Deployment Options

### Option 1: With Nginx Proxy and Let's Encrypt (Recommended for Production)

This setup automatically handles SSL/TLS certificates via Let's Encrypt.

1. Navigate to the deployment directory:
```bash
cd deploy/docker-compose/with-nginx-proxy-and-letsencrypt
```

2. The docker-compose.yml file is already configured for sirkbbir.com. Review the configuration:
   - VIRTUAL_HOST=sirkbbir.com
   - LETSENCRYPT_HOST=sirkbbir.com
   - LETSENCRYPT_EMAIL=admin@sirkbbir.com (update this to your actual email)

3. Start the services:
```bash
docker-compose up -d
```

4. Check the logs to ensure everything is running:
```bash
docker-compose logs -f
```

5. Your Sirkbbir instance should now be available at https://sirkbbir.com

### Option 2: Behind Your Own Reverse Proxy

If you already have a reverse proxy (like Nginx, Caddy, or Traefik) handling TLS:

1. Navigate to the insecure deployment directory:
```bash
cd deploy/docker-compose/insecure
```

2. Start the services:
```bash
docker-compose up -d
```

3. Configure your reverse proxy to forward traffic to `127.0.0.1:80`

Example Nginx configuration:
```nginx
server {
    listen 443 ssl http2;
    server_name sirkbbir.com;

    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;

    location / {
        proxy_pass http://127.0.0.1:80;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## Building and Deploying Custom Frontend

If you've made changes to the frontend:

1. Navigate to the website directory:
```bash
cd website
```

2. Install dependencies:
```bash
yarn install
```

3. Build the frontend:
```bash
yarn build
```

4. The built files will be in `website/dist/` directory

## Monitoring

Check if containers are running:
```bash
docker-compose ps
```

View logs:
```bash
docker-compose logs -f sirkbbir
docker-compose logs -f memcached
```

## Updating

To update Sirkbbir:

1. Pull the latest changes from your repository
2. Rebuild and restart the containers:
```bash
docker-compose down
docker-compose pull
docker-compose up -d
```

## Troubleshooting

### Port Already in Use
If you get a "port already in use" error:
```bash
# Check what's using the port
sudo lsof -i :80
sudo lsof -i :443

# Stop the conflicting service or modify the docker-compose port mapping
```

### Let's Encrypt Certificate Issues
- Ensure your domain is pointing to the correct IP
- Check that ports 80 and 443 are accessible from the internet
- Review logs: `docker-compose logs nginx-proxy-letsencrypt`

### Connection to Memcached Failed
- Ensure the memcached container is running: `docker-compose ps`
- Check memcached logs: `docker-compose logs memcached`

## Security Recommendations

1. Use strong passwords if implementing authentication
2. Regularly update Docker images: `docker-compose pull`
3. Monitor logs for suspicious activity
4. Keep your server OS updated
5. Use firewall rules to restrict access to necessary ports only
6. Consider implementing rate limiting on your reverse proxy

## Backup

To backup your secrets (if using Redis instead of Memcached):

```bash
# If using Redis, backup the data
docker exec redis redis-cli BGSAVE
```

Note: Memcached doesn't persist data, so there's nothing to backup.

## Support

For issues or questions:
- Check the logs: `docker-compose logs`
- Review the main README.md
- Original Yopass documentation: https://github.com/jhaals/yopass
