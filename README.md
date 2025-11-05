# Axionax Deploy

Production-ready deployment infrastructure for Axionax Protocol services.

##  Overview

This repository contains everything needed to deploy Axionax Protocol infrastructure on a VPS, including:
- **RPC Node** - JSON-RPC endpoint for blockchain interaction
- **Block Explorer** - Web interface for browsing transactions and blocks
- **Testnet Faucet** - AXX token distribution for testing
- **Monitoring Stack** - Prometheus + Grafana dashboards
- **SSL/TLS** - Automatic Let's Encrypt certificates
- **Reverse Proxy** - Nginx with security hardening

##  Quick Start

### 1. Clone Repository on VPS

```bash
ssh root@YOUR_VPS_IP
cd /opt
git clone https://github.com/axionaxprotocol/axionax-deploy.git
cd axionax-deploy
```

### 2. Configure Environment

```bash
cp .env.example .env
nano .env
```

Required variables:
```env
DB_PASSWORD=your_postgres_password
REDIS_PASSWORD=your_redis_password
FAUCET_PRIVATE_KEY=0x...
GRAFANA_PASSWORD=your_grafana_password
VPS_IP=YOUR_VPS_IP
DOMAIN=axionax.org
```

### 3. Run Setup Script

```bash
chmod +x setup-vps.sh
./setup-vps.sh
```

The script automatically:
- Installs Docker and Docker Compose
- Requests SSL certificates
- Starts all services
- Configures firewall

##  Architecture

```

         Nginx Reverse Proxy (SSL)           
    rpc.axionax.org | explorer.axionax.org   
         faucet.axionax.org                   

              
    
                                
   
  RPC   Explore  Faucet  Monitor
 :8545   :3001   :3002    :3000 
   
                      
    
                  
     
    Postgre  Redis  
      SQL    Cache  
     
```

##  Services

### RPC Node (Port 8545/8546)
- HTTP JSON-RPC endpoint
- WebSocket support
- CORS enabled for public access
- Health check: `https://rpc.axionax.org/health`

### Block Explorer (Port 3001)
- Real-time blockchain data
- Transaction/block search
- Account history
- PostgreSQL backed

### Testnet Faucet (Port 3002)
- AXX token distribution
- Rate limiting (1 request/24h per IP)
- Redis-backed queue
- Configurable amount

### Monitoring (Port 3000/9090)
- Grafana dashboards
- Prometheus metrics
- Node health alerts
- Resource usage tracking

##  Management Commands

### View logs
```bash
cd /opt/axionax-deploy
docker-compose -f docker-compose.vps.yml logs -f [service-name]
```

### Restart services
```bash
docker-compose -f docker-compose.vps.yml restart
```

### Stop all services
```bash
docker-compose -f docker-compose.vps.yml down
```

### Update images
```bash
docker-compose -f docker-compose.vps.yml pull
docker-compose -f docker-compose.vps.yml up -d
```

### Backup database
```bash
docker exec axionax-postgres pg_dump -U explorer explorer > backup.sql
```

##  DNS Configuration

Point these subdomains to your VPS IP:

```
Type: A, Name: rpc, Value: YOUR_VPS_IP
Type: A, Name: explorer, Value: YOUR_VPS_IP
Type: A, Name: faucet, Value: YOUR_VPS_IP
Type: A, Name: api, Value: YOUR_VPS_IP
```

##  Security Features

-  Automatic SSL/TLS via Let's Encrypt
-  Security headers (HSTS, X-Frame-Options, CSP)
-  Rate limiting on API endpoints
-  Firewall configuration (UFW)
-  Non-root container users
-  Secret management via .env

##  Monitoring Access

- **Grafana**: http://YOUR_VPS_IP:3000
  - Username: `admin`
  - Password: (from .env)
  
- **Prometheus**: http://YOUR_VPS_IP:9090

##  Repository Structure

```
.
 docker-compose.vps.yml    # Main service definitions
 setup-vps.sh              # Automated setup script
 .env.example              # Environment template
 nginx/
    nginx.conf            # Main Nginx config
    conf.d/               # Site configurations
        rpc.conf          # RPC proxy
        explorer.conf     # Explorer proxy
        faucet.conf       # Faucet proxy
 monitoring/
    prometheus.yml        # Metrics config
    grafana/              # Dashboards
 VPS_DEPLOYMENT.md         # Detailed guide

```

##  CI/CD Integration

Services auto-deploy when new images are pushed to GitHub Container Registry:

```bash
# Pull latest images
docker-compose -f docker-compose.vps.yml pull

# Recreate containers
docker-compose -f docker-compose.vps.yml up -d
```

##  Requirements

- Ubuntu 20.04+ or Debian 11+
- Minimum 4GB RAM, 2 CPU cores
- 50GB+ SSD storage
- Root or sudo access
- Domain with DNS access

##  Documentation

- **[VPS_DEPLOYMENT.md](VPS_DEPLOYMENT.md)** - Complete deployment guide
- **[GITHUB_PAGES_SETUP.md](GITHUB_PAGES_SETUP.md)** - Website hosting setup

##  Related Projects

- **[axionax-core](https://github.com/axionaxprotocol/axionax-core)** - Protocol implementation (Rust)
- **[axionax-sdk-ts](https://github.com/axionaxprotocol/axionax-sdk-ts)** - TypeScript SDK
- **[axionax-web](https://github.com/axionaxprotocol/axionax-web)** - Official website
- **[axionax-marketplace](https://github.com/axionaxprotocol/axionax-marketplace)** - Compute marketplace

##  Troubleshooting

### Service not responding?
```bash
docker-compose -f docker-compose.vps.yml logs [service-name]
docker ps -a  # Check container status
```

### SSL certificate issues?
```bash
docker-compose -f docker-compose.vps.yml run --rm certbot renew --force-renewal
docker-compose -f docker-compose.vps.yml restart nginx
```

### Database connection errors?
```bash
docker exec axionax-postgres psql -U explorer -c "SELECT 1"
```

##  Contributing

1. Fork the repository
2. Create feature branch
3. Test changes locally with Docker Compose
4. Submit pull request

##  License

MIT License

##  Support

- **Issues**: https://github.com/axionaxprotocol/axionax-deploy/issues
- **Docs**: https://docs.axionax.org
- **Discord**: https://discord.gg/axionax
- **Website**: https://axionax.org

---

Built with  by the Axionax team
