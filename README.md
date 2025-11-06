# AxionAX Protocol - Deployment Infrastructure 🚀

Production-ready deployment infrastructure for **AxionAX Protocol** services.

[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Protocol](https://img.shields.io/badge/Protocol-AxionAX-purple)](https://axionax.org)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED)](https://www.docker.com/)

---

## Overview

This repository contains everything needed to deploy the complete **AxionAX
Protocol** infrastructure stack on a VPS or cloud environment.

### Part of AxionAX Ecosystem

Deploys the full AxionAX Protocol stack:

- **Protocol Node**: [`../core`](../core) - AxionAX RPC node
- **Block Explorer**: Blockchain data visualization
- **Testnet Faucet**: AXX token distribution
- **Web Interface**: [`../web`](../web) - Static frontend
- **Monitoring**: Prometheus + Grafana

**Main Repository**:
[axionaxprotocol/axionaxiues](https://github.com/axionaxprotocol/axionaxiues)

---

## What Gets Deployed

### AxionAX Protocol Services

- **RPC Node (Port 8545/8546)** - Full AxionAX Protocol node
  - HTTP JSON-RPC endpoint
  - WebSocket support
  - CORS enabled
  - Health monitoring
- **Block Explorer (Port 3001)** - Blockchain visualization
  - Real-time AxionAX block data
  - Transaction/block search
  - Account history
  - Network statistics
- **Testnet Faucet (Port 3002)** - Token distribution
  - AXX token distribution
  - Rate limiting (1 request/24h)
  - Configurable amounts
- **Monitoring Stack** - System health
  - Grafana dashboards (Port 3000)
  - Prometheus metrics (Port 9090)
  - AxionAX node health alerts

### Infrastructure Components

- Nginx reverse proxy with SSL/TLS
- PostgreSQL database for blockchain indexing
- Redis cache for performance
- Automatic Let's Encrypt certificates
- Docker containerization
- Systemd service management

## Quick Start

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

## Architecture

```

         Nginx Reverse Proxy (SSL)
    rpc.axionax.org | explorer.axionax.org
         faucet.axionax.org





  RPC   Explore  Faucet  Monitor
 :8545   :3001   :3002    :3000





    Postgre  Redis
      SQL    Cache

```

## Services

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

## Management Commands

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

## DNS Configuration

Point these subdomains to your VPS IP:

```
Type: A, Name: rpc, Value: YOUR_VPS_IP
Type: A, Name: explorer, Value: YOUR_VPS_IP
Type: A, Name: faucet, Value: YOUR_VPS_IP
Type: A, Name: api, Value: YOUR_VPS_IP
```

## Security Features

- Automatic SSL/TLS via Let's Encrypt
- Security headers (HSTS, X-Frame-Options, CSP)
- Rate limiting on API endpoints
- Firewall configuration (UFW)
- Non-root container users
- Secret management via .env

## Monitoring Access

- **Grafana**: http://YOUR_VPS_IP:3000
  - Username: `admin`
  - Password: (from .env)
- **Prometheus**: http://YOUR_VPS_IP:9090

## Repository Structure

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

## CI/CD Integration

Services auto-deploy when new images are pushed to GitHub Container Registry:

```bash
# Pull latest images
docker-compose -f docker-compose.vps.yml pull

# Recreate containers
docker-compose -f docker-compose.vps.yml up -d
```

## Requirements

- Ubuntu 20.04+ or Debian 11+
- Minimum 4GB RAM, 2 CPU cores
- 50GB+ SSD storage
- Root or sudo access
- Domain with DNS access

## Documentation

- **[VPS_DEPLOYMENT.md](VPS_DEPLOYMENT.md)** - Complete deployment guide
- **[GITHUB_PAGES_SETUP.md](GITHUB_PAGES_SETUP.md)** - Website hosting setup

## AxionAX Protocol Ecosystem

| Component         | Description               | Location                     |
| ----------------- | ------------------------- | ---------------------------- |
| **Deploy** (this) | Infrastructure deployment | `deploy/`                    |
| **Core**          | AxionAX Protocol node     | [`../core`](../core)         |
| **Web**           | Frontend interface        | [`../web`](../web)           |
| **SDK**           | Developer SDK             | [`../sdk`](../sdk)           |
| **Docs**          | Documentation             | [`../docs`](../docs)         |
| **DevTools**      | Development tools         | [`../devtools`](../devtools) |

---

## Related Projects

### Core Components

- **[Protocol Core](../core)** - AxionAX blockchain implementation
- **[Web Interface](../web)** - Frontend (deployed separately)
- **[SDK](../sdk)** - Used by Explorer/Faucet APIs
- **[Documentation](../docs)** - Full protocol documentation

### External Resources

- **Main Repository**: https://github.com/axionaxprotocol/axionaxiues
- **Protocol Website**: https://axionax.org
- **Documentation**: https://docs.axionax.org

---

## Contributing

1. Fork the main repository:
   [axionaxprotocol/axionaxiues](https://github.com/axionaxprotocol/axionaxiues)
2. Create feature branch
3. Work in the `deploy/` directory
4. Test changes with Docker Compose locally
5. Submit pull request

---

## License

MIT License - see [LICENSE](LICENSE) for details.

**Note**: The AxionAX Protocol Core uses AGPLv3. See
[`../core/LICENSE`](../core/LICENSE).

---

## Support

- **Issues**: https://github.com/axionaxprotocol/axionaxiues/issues
- **Docs**: https://docs.axionax.org or [`../docs`](../docs)
- **Main Repository**: https://github.com/axionaxprotocol/axionaxiues

### Community (Coming Q1 2026)

- **Discord**: https://discord.gg/axionax
- **Twitter**: https://twitter.com/axionaxprotocol

---

**Part of the AxionAX Protocol Ecosystem**

Built with ❤️ by the AxionAX team

**Last Updated**: November 6, 2025
