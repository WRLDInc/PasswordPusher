# PasswordPusher Deployment on Coolify

This guide explains how to deploy PasswordPusher on your Coolify instance at https://cool.wrld.one

## Repository Setup ✅

The repository has been cloned from the WRLDInc fork and is ready for deployment.

## Pre-configured Files

1. **`docker-compose-coolify.yml`** - Optimized Docker Compose file for Coolify (without Caddy SSL proxy since Coolify handles SSL)
2. **`.env`** - Environment configuration file with secure defaults
3. **`docker-compose-pwpush.yml`** - Original Docker Compose file (includes Caddy for standalone deployments)

## Deployment Steps on Coolify

### 1. Push to GitHub (if using GitHub integration)

If your Coolify is configured to deploy from GitHub:

```bash
git add .
git commit -m "Configure PasswordPusher for Coolify deployment"
git push origin master
```

### 2. Configure in Coolify

1. **Access Coolify** at https://cool.wrld.one
2. **Create New Project/Resource**:
   - Click "New Project" or "Add Resource"
   - Select "Docker Compose" as the deployment type

3. **Connect Repository**:
   - **Option A: GitHub Integration**
     - Connect to `github.com/WRLDInc/PasswordPusher`
     - Select the `master` branch
   - **Option B: Direct Upload**
     - Upload the `docker-compose-coolify.yml` file directly

4. **Configure Docker Compose**:
   - Set the compose file path to `docker-compose-coolify.yml`
   - Or rename `docker-compose-coolify.yml` to `docker-compose.yml` if Coolify expects that name

### 3. Environment Variables

Configure these in Coolify's environment variables section:

#### Required Variables:
```env
# Database (if you want different from defaults)
POSTGRES_USER=pwpush_user
POSTGRES_PASSWORD=<generate-secure-password>
POSTGRES_DB=pwpush_db

# Security (already generated, but you can regenerate)
SECRET_KEY_BASE=f67faa5471b86f13cf03c42b9f7c8f9808ced34bc7f3c44dbe31271438db816adb41b92f3601b6d0795274c468ec60d3c1bd80b21f01a5b6c214939439e7a777

# Application Domain (update after Coolify assigns domain)
PWP__HOST_DOMAIN=pwpush.your-domain.com
PWP__HOST_PROTOCOL=https
```

#### Optional Features:
```env
# Enable user accounts
PWP__ENABLE_LOGINS=true

# Enable file uploads
PWP__ENABLE_FILE_PUSHES=true

# Enable URL shortening
PWP__ENABLE_URL_PUSHES=true

# Password expiration settings
PWP__PW__EXPIRE_AFTER_DAYS_DEFAULT=7
PWP__PW__EXPIRE_AFTER_VIEWS_DEFAULT=5
```

### 4. Domain & SSL Configuration

1. **Set Domain**: In Coolify, configure your subdomain (e.g., `pwpush.wrld.host`)
2. **SSL Certificate**: Enable Let's Encrypt automatic SSL provisioning
3. **Port**: The application runs on port 5100 internally

### 5. Resource Limits (Optional)

Configure in Coolify if needed:
- **Memory**: Minimum 512MB, recommended 1GB
- **CPU**: 0.5-1 CPU core
- **Storage**: Depends on file upload usage

### 6. Deploy

1. Click **"Deploy"** in Coolify
2. Monitor the deployment logs
3. Wait for all services to be healthy:
   - `pwpush` (main application)
   - `worker` (background jobs)
   - `db` (PostgreSQL database)

## Post-Deployment

### Verify Deployment

1. Access your PasswordPusher instance at the configured domain
2. Create a test password push
3. Verify the expiration settings work
4. Test file uploads if enabled

### Database Backups

Consider setting up automated PostgreSQL backups in Coolify:
- The database volume is `postgres_data`
- Configure regular snapshots or external backup solution

### Monitoring

- Check application logs: `pwpush` service logs
- Check worker logs: `worker` service logs
- Database logs: `db` service logs

## Security Considerations

1. **Change Default Database Password**: Update `POSTGRES_PASSWORD` in Coolify
2. **Secure Secret Key**: The `SECRET_KEY_BASE` has been generated, keep it secure
3. **Enable HTTPS Only**: Coolify should handle this automatically
4. **Regular Updates**: Keep the Docker images updated

## Troubleshooting

### Database Connection Issues
- Verify `DATABASE_URL` matches your PostgreSQL credentials
- Check if the database service is running

### Application Not Accessible
- Verify domain configuration in Coolify
- Check if port 5100 is properly exposed
- Review application logs for errors

### File Uploads Not Working
- Ensure `PWP__ENABLE_FILE_PUSHES=true` is set
- Check volume permissions for `pwpush_storage`

## Additional Resources

- [PasswordPusher Documentation](https://docs.pwpush.com/)
- [Configuration Options](https://github.com/pglombardo/PasswordPusher/blob/master/config/settings.yml)
- [Coolify Documentation](https://coolify.io/docs)

## Support

For issues specific to this deployment:
- Check the application logs in Coolify
- Review the PostgreSQL database logs
- Ensure all environment variables are properly set
