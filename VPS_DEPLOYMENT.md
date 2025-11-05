# Axionax VPS Deployment Guide

## Prerequisites

- Ubuntu 20.04+ or Debian 11+ VPS
- Minimum 4GB RAM, 2 CPU cores, 50GB SSD
- Root or sudo access
- Domain configured with DNS pointing to VPS IP

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    axionax.org                          │
│               (GitHub Pages - Static)                   │
└─────────────────────────────────────────────────────────┘
                            │
                            │
    ┌───────────────────────┴───────────────────────┐
    │                                               │
    │              VPS Infrastructure               │
    │          YOUR_VPS_IP (e.g., 1.2.3.4)         │
    │                                               │
    ├───────────────┬───────────────┬───────────────┤
    │               │               │               │
┌───▼────┐    ┌────▼─────┐   ┌─────▼────┐   ┌─────▼────┐
│  RPC   │    │ Explorer │   │  Faucet  │   │ Monitor  │
│ :8545  │    │  :3001   │   │  :3002   │   │ :3000    │
└────────┘    └──────────┘   └──────────┘   └──────────┘
     │              │              │              │
     └──────┬───────┴──────┬───────┴──────────────┘
            │              │
      ┌─────▼────┐   ┌─────▼─────┐
      │ Postgres │   │   Redis   │
      │  :5432   │   │   :6379   │
      └──────────┘   └───────────┘
```

---

## Step 1: DNS Configuration

Configure DNS A records in your domain registrar:

```
Type: A
Name: rpc
Value: YOUR_VPS_IP
TTL: 3600

Type: A
Name: explorer
Value: YOUR_VPS_IP
TTL: 3600

Type: A
Name: faucet
Value: YOUR_VPS_IP
TTL: 3600

Type: A
Name: api
Value: YOUR_VPS_IP
TTL: 3600
```

Verify DNS propagation:
```bash
nslookup rpc.axionax.org
nslookup explorer.axionax.org
nslookup faucet.axionax.org
```

---

## Step 2: VPS Setup

### 1. Connect to VPS
```bash
ssh root@YOUR_VPS_IP
```

### 2. Clone deployment repository
```bash
cd /opt
git clone https://github.com/axionaxprotocol/axionax-deploy.git
cd axionax-deploy
```

### 3. Configure environment
```bash
cp .env.example .env
nano .env
```

Fill in required values:
```bash
DB_PASSWORD=your_secure_postgres_password
REDIS_PASSWORD=your_secure_redis_password
FAUCET_PRIVATE_KEY=0x...your_private_key
GRAFANA_PASSWORD=your_grafana_password
VPS_IP=YOUR_VPS_IP
DOMAIN=axionax.org
```

### 4. Run setup script
```bash
chmod +x setup-vps.sh
./setup-vps.sh
```

The script will:
- Install Docker and Docker Compose
- Request SSL certificates via Let's Encrypt
- Start all services
- Configure firewall

---

## Step 3: Verify Deployment

### Check service status
```bash
docker-compose -f docker-compose.vps.yml ps
```

All services should show "Up" status.

### Check service health
```bash
# RPC Node
curl https://rpc.axionax.org/health

# Explorer
curl https://explorer.axionax.org/api/health

# Faucet
curl https://faucet.axionax.org/health
```

### View logs
```bash
# All services
docker-compose -f docker-compose.vps.yml logs -f

# Specific service
docker-compose -f docker-compose.vps.yml logs -f rpc-node
docker-compose -f docker-compose.vps.yml logs -f explorer-backend
docker-compose -f docker-compose.vps.yml logs -f faucet
```

---

## Step 4: Access Services

### Public Services
- **RPC Endpoint**: https://rpc.axionax.org
- **Block Explorer**: https://explorer.axionax.org
- **Testnet Faucet**: https://faucet.axionax.org

### Admin Services (IP restricted)
- **Grafana**: http://YOUR_VPS_IP:3000
  - Username: `admin`
  - Password: (from .env GRAFANA_PASSWORD)
  
- **Prometheus**: http://YOUR_VPS_IP:9090

---

## Maintenance Commands

### Restart services
```bash
cd /opt/axionax-deploy
docker-compose -f docker-compose.vps.yml restart
```

### Stop services
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
docker exec axionax-postgres pg_dump -U explorer explorer > backup_$(date +%Y%m%d).sql
```

### View resource usage
```bash
docker stats
```

### Clean up old containers/images
```bash
docker system prune -a
```

---

## SSL Certificate Renewal

Certificates auto-renew via certbot container. To manually renew:

```bash
docker-compose -f docker-compose.vps.yml run --rm certbot renew
docker-compose -f docker-compose.vps.yml restart nginx
```

---

## Monitoring

Access Grafana at `http://YOUR_VPS_IP:3000`

Default dashboards include:
- Node health and performance
- Database metrics
- API response times
- Faucet usage statistics

---

## Security Best Practices

1. **Change default passwords** in .env file
2. **Restrict Grafana/Prometheus access** via firewall
3. **Regular backups** of PostgreSQL database
4. **Monitor logs** for suspicious activity
5. **Keep Docker images updated**
6. **Use strong private keys** for faucet

---

## Troubleshooting

### Service not starting?
```bash
# Check logs
docker-compose -f docker-compose.vps.yml logs [service-name]

# Check container status
docker ps -a
```

### SSL certificate issues?
```bash
# Check certificate
docker-compose -f docker-compose.vps.yml run --rm certbot certificates

# Renew manually
docker-compose -f docker-compose.vps.yml run --rm certbot renew --force-renewal
```

### Database connection errors?
```bash
# Check PostgreSQL
docker exec axionax-postgres psql -U explorer -c "SELECT 1"

# Reset database (WARNING: deletes all data)
docker-compose -f docker-compose.vps.yml down -v
docker-compose -f docker-compose.vps.yml up -d
```

### High resource usage?
```bash
# Check resource consumption
docker stats

# Adjust resource limits in docker-compose.vps.yml
```

---

## Support

For issues, open a ticket at:
https://github.com/axionaxprotocol/axionax-deploy/issues
