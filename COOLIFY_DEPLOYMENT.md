# OpnForm Coolify Deployment Guide

This guide will help you deploy OpnForm on Coolify using Docker Compose.

## Prerequisites

1. A Coolify instance running and accessible
2. A domain name pointed to your Coolify server
3. SMTP credentials for email functionality (recommended)

## Deployment Steps

### 1. Create a New Project in Coolify

1. Log into your Coolify dashboard
2. Click "New" → "Project"
3. Give your project a name (e.g., "OpnForm")

### 2. Add the Application

1. In your project, click "New" → "Application"
2. Choose "Docker Compose" as the source
3. Provide your repository URL or upload the docker-compose.yml file
4. Set the branch to deploy from (usually `main`)

### 3. Configure Environment Variables

Copy the variables from `.env.coolify` and add them to your Coolify application's environment variables section. **Required variables:**

```bash
APP_URL=https://your-domain.com
APP_KEY=base64:YOUR_32_CHARACTER_KEY_HERE
API_SECRET=your-secure-api-secret
DB_DATABASE=opnform
DB_USERNAME=opnform
DB_PASSWORD=your-secure-database-password
MAIL_MAILER=smtp
MAIL_HOST=your-smtp-host.com
MAIL_PORT=587
MAIL_USERNAME=your-smtp-username
MAIL_PASSWORD=your-smtp-password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@your-domain.com
MAIL_FROM_NAME=OpnForm
```

**To generate APP_KEY:**
Run this command: `php -r "echo 'base64:' . base64_encode(random_bytes(32)) . PHP_EOL;"`
Or use an online Laravel key generator.

### 4. Configure Domain and SSL

1. In Coolify, go to your application settings
2. Add your domain name in the "Domains" section
3. Coolify will automatically generate SSL certificates via Let's Encrypt

### 5. Set Resource Allocation

Recommended minimum resources:
- **CPU**: 1 vCPU
- **Memory**: 2GB RAM
- **Storage**: 10GB (adjust based on expected file uploads)

### 6. Deploy

1. Click "Deploy" in your Coolify application
2. Monitor the deployment logs for any issues
3. The first deployment may take 5-10 minutes

### 7. Post-Deployment Setup

After successful deployment, you may need to run initial setup commands:

1. Access the application container in Coolify
2. Run database migrations:
   ```bash
   php artisan migrate --force
   ```
3. Create an admin user (if needed):
   ```bash
   php artisan make:admin-user
   ```

## Service Architecture

The deployment includes these services:

- **app**: Frontend Nuxt.js application (port 3000)
- **api**: Backend Laravel API (port 80)  
- **api-worker**: Background job processor
- **api-scheduler**: Cron job scheduler
- **db**: PostgreSQL database
- **redis**: Redis cache and queue

## Important Notes

### Routing Configuration

Coolify will automatically route traffic to the `app` service (frontend). The API service is accessible internally for the frontend to communicate with.

### Persistent Storage

The following volumes are configured for persistence:
- `postgres-data`: Database storage
- `redis-data`: Redis persistence
- `opnform_storage`: File uploads and application storage

### Health Checks

All services have health checks configured to ensure proper startup order and monitoring.

### Scaling

You can scale the following services if needed:
- `api-worker`: Scale up for heavy background job processing
- `api`: Scale up for high API traffic (requires load balancer configuration)

## Troubleshooting

### Common Issues

1. **503 Service Unavailable**: Check if all services are healthy in Coolify logs
2. **Database Connection Issues**: Verify database credentials and ensure DB service is running
3. **Email Not Working**: Verify SMTP configuration and credentials
4. **File Upload Issues**: Check volume mounts and permissions

### Viewing Logs

In Coolify:
1. Go to your application
2. Click on individual services to view their logs
3. Monitor the deployment logs during updates

### Accessing the Application Container

1. In Coolify, go to your application
2. Click on the "api" service
3. Use the "Execute Command" feature to run artisan commands

## Updating

To update OpnForm:
1. Pull the latest changes to your repository
2. In Coolify, click "Deploy" to redeploy with the latest code
3. Monitor logs for any migration or update processes

## Backup Strategy

Recommended backup approach:
1. **Database**: Use Coolify's backup features or set up automated PostgreSQL backups
2. **File Storage**: Backup the `opnform_storage` volume regularly
3. **Environment Variables**: Keep a secure copy of your environment configuration

## Security Considerations

1. Use strong passwords for all services
2. Regularly update the OpnForm images
3. Enable SSL/HTTPS (handled automatically by Coolify)
4. Consider setting up fail2ban or similar protection
5. Regularly backup your data

## Support

For OpnForm-specific issues, visit: https://github.com/JhumanJ/OpnForm
For Coolify-specific issues, visit: https://coolify.io/docs