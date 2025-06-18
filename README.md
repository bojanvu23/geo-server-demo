# Geospatial Vector Tile Server

This Ansible playbook sets up a complete Geospatial Vector Tile Server with the following components:

- PostgreSQL with PostGIS
- Tegola vector tile server
- Nginx with vector tile caching
- GDAL/OGR utilities
- Daily data synchronization with Geofabric

## Prerequisites

- Ubuntu LTS server
- Ansible 2.9 or later
- SSH access to the target server
- Open ports: 22 (SSH), 80 (HTTP), 443 (HTTPS, optional)

## Getting Started

1. Edit the inventory file `inventory/hosts` and add your server:
   ```ini
   [geo_servers]
   your_server ansible_host=your_server_ip
   ```

2. Configure `group_vars/geo_servers.yml`:
   ```yaml
   # Set your database credentials
   postgresql_user: your_db_user
   # postgresql_password is set via POSTGRES_PASSWORD environment variable
   postgresql_database: your_db_name
   
   # Set your Geofabric URL
   geofabric_url: your_geofabric_url

   # SSL Configuration
   enable_ssl: false  # Set to true to enable HTTPS
   ssl_domain: your.domain.com  # Required if enable_ssl is true
   ssl_email: your@email.com    # Required if enable_ssl is true
   ```

### Environment Variables

The database password must be set using the POSTGRES_PASSWORD environment variable:

```bash
# Required: Set PostgreSQL password
export POSTGRES_PASSWORD="your_secure_password"

# Run the playbook
ansible-playbook -i inventory/hosts server.yml
```

## Usage

1. Run the playbook(in ansible folder):
   ```bash
   ansible-playbook -i inventory/hosts server.yml
   ```

## Components

### PostgreSQL/PostGIS
- PostgreSQL 14 with PostGIS 3
- Optimized for spatial data
- Configured for vector tile generation

### Tegola
- Vector tile server
- Configured to work with PostGIS
- Serves vector tiles on port 8080

### Nginx
- Reverse proxy for Tegola
- Caches vector tiles
- Configured with CORS headers
- Cache size 512MB
- Optional SSL support with Let's Encrypt

### GDAL/OGR - org2org tool
- Full GDAL/OGR installation
- Additional drivers for various formats
- Used for data synchronization

### Data Synchronization
- Daily sync with Geofabric
- Runs at 2:00 AM by default
- Logs to `/var/log/sync/sync.log`
- To manually trigger sync:
  ```bash
  sudo /usr/local/bin/sync.sh
  ```


### Logs are located in:
- PostgreSQL: `/var/log/postgresql/`
- Tegola: `/var/log/tegola/`
- Nginx: `/var/log/nginx/`
- Sync: `/var/log/sync/`


## Production Considerations

> **Note:** This playbook is configured for development purposes. For production use, additional considerations are required:

- Monitoring and alerting
- Improve logging
- Backup and Disaster Recovery plan
- Performance tunning
- Scaling implementation
