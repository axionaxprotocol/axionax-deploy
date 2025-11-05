# Axionax Deploy

Production-ready deployment infrastructure for Axionax Protocol services.

##  Quick Deploy

### For VPS Deployment

\\\ash
# On your VPS (Ubuntu/Debian)
cd /opt
git clone https://github.com/axionaxprotocol/axionax-deploy.git
cd axionax-deploy

# Configure environment
cp .env.example .env
nano .env  # Fill in your secrets

# Run automated setup
chmod +x setup-vps.sh
./setup-vps.sh
\\\

**Services will be available at:**
- RPC Node: https://rpc.axionax.org
- Explorer: https://explorer.axionax.org  
- Faucet: https://faucet.axionax.org
- Grafana: http://YOUR_VPS_IP:3000

##  Contents

### Docker Compose Configurations

- **docker-compose.vps.yml** - Production VPS deployment
  - RPC Node (ports 8545/8546)
  - Block Explorer Backend
  - Testnet Faucet
  - PostgreSQL Database
  - Redis Cache
  - Nginx Reverse Proxy with SSL
  - Prometheus + Grafana Monitoring

### Nginx Configurations

- **nginx/nginx.conf** - Main Nginx config
- **nginx/conf.d/rpc.conf** - RPC endpoint (HTTP/WebSocket)
- **nginx/conf.d/explorer.conf** - Explorer with rate limiting
- **nginx/conf.d/faucet.conf** - Faucet with strict rate limiting

### Scripts

- **setup-vps.sh** - Automated VPS setup script
  - Installs Docker & Docker Compose
  - Configures SSL certificates (Let's Encrypt)
  - Starts all services
  - Sets up firewall rules

### Testnet Environments

- **environments/testnet/** - Testnet configurations
  - Validator setup
  - RPC node configs  
  - Genesis ceremony files
  - Monitoring templates

##  Architecture

\\\

       axionax.org                   
    (GitHub Pages - Website)         

              
               DNS
              

     VPS Infrastructure            
   (Docker Compose Services)       

                                   
          
   RPC Node    Explorer       
    :8545       :3001         
          
                                 
          
    Faucet     Monitor        
    :3002       :3000         
          
                                 
          
  Postgres      Redis         
    :5432       :6379         
          
                                   

\\\

##  Prerequisites

- Ubuntu 20.04+ or Debian 11+ VPS
- Minimum: 4GB RAM, 2 CPU cores, 50GB SSD
- Root or sudo access
- Domain with DNS configured

##  Configuration

### 1. DNS Setup

Configure A records in your DNS provider:

\\\
rpc.axionax.org       YOUR_VPS_IP
explorer.axionax.org  YOUR_VPS_IP
faucet.axionax.org    YOUR_VPS_IP
\\\

### 2. Environment Variables

Edit \.env\ file:

\\\ash
DB_PASSWORD=your_secure_postgres_password
REDIS_PASSWORD=your_secure_redis_password
FAUCET_PRIVATE_KEY=0x...your_private_key
GRAFANA_PASSWORD=your_grafana_password
VPS_IP=YOUR_VPS_IP
DOMAIN=axionax.org
\\\

##  Manual Deployment

If you prefer manual setup:

\\\ash
# Start all services
docker-compose -f docker-compose.vps.yml up -d

# View logs
docker-compose -f docker-compose.vps.yml logs -f

# Check status
docker-compose -f docker-compose.vps.yml ps

# Stop services
docker-compose -f docker-compose.vps.yml down
\\\

##  Monitoring

Access Grafana dashboard at \http://YOUR_VPS_IP:3000\

Default dashboards include:
- Node health and sync status
- RPC request metrics
- Database performance
- Faucet usage statistics
- System resources (CPU, RAM, disk)

##  Security Features

- SSL/TLS certificates via Let's Encrypt
- Rate limiting on API endpoints
- Firewall configuration (UFW)
- Security headers (HSTS, X-Frame-Options)
- Private key management via environment variables
- No hardcoded secrets

##  Maintenance

### Update Services

\\\ash
docker-compose -f docker-compose.vps.yml pull
docker-compose -f docker-compose.vps.yml up -d
\\\

### Backup Database

\\\ash
docker exec axionax-postgres pg_dump -U explorer explorer > backup.sql
\\\

### View Logs

\\\ash
# All services
docker-compose -f docker-compose.vps.yml logs -f

# Specific service
docker-compose -f docker-compose.vps.yml logs -f rpc-node
\\\

### SSL Certificate Renewal

Certificates auto-renew. To force renewal:

\\\ash
docker-compose -f docker-compose.vps.yml run --rm certbot renew
docker-compose -f docker-compose.vps.yml restart nginx
\\\

##  Documentation

- **[VPS_DEPLOYMENT.md](VPS_DEPLOYMENT.md)** - Complete deployment guide
- **[GITHUB_SECRETS_SETUP.md](GITHUB_SECRETS_SETUP.md)** - CI/CD secrets

##  Related Repositories

- **[axionax-core](https://github.com/axionaxprotocol/axionax-core)** - Protocol implementation (Rust)
- **[axionax-sdk-ts](https://github.com/axionaxprotocol/axionax-sdk-ts)** - TypeScript SDK
- **[axionax-web](https://github.com/axionaxprotocol/axionax-web)** - Official website
- **[axionax-docs](https://github.com/axionaxprotocol/axionax-docs)** - Documentation

##  Troubleshooting

### Service Not Starting

\\\ash
docker-compose -f docker-compose.vps.yml logs [service-name]
docker ps -a
\\\

### SSL Issues

\\\ash
docker-compose -f docker-compose.vps.yml run --rm certbot certificates
\\\

### High Resource Usage

\\\ash
docker stats
\\\

##  License

MIT License

---

**Need help?** Open an issue at https://github.com/axionaxprotocol/axionax-deploy/issues
