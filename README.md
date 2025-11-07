# Axionax Protocol - Deployment Infrastructure ??

Production-ready deployment infrastructure for **Axionax Protocol** services.

[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Protocol](https://img.shields.io/badge/Protocol-axionax-purple)](https://axionax.org)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED)](https://www.docker.com/)
[![Status](https://img.shields.io/badge/Status-Pre--Testnet-orange)](https://github.com/axionaxprotocol/axionax-core)

---

## ?? Latest Update (November 2025)

?? **Preparing Infrastructure for Public Testnet Launch!**

We're completing final preparations before public testnet:

? **Infrastructure Checklist:**
- ??? Monitoring & Alerting Setup (Prometheus + Grafana)
- ?? Load Testing & Performance Optimization
- ?? Backup & Disaster Recovery Plans
- ?? Security Hardening & SSL Configuration
- ?? Resource Scaling Strategy

?? **Active Development:**
- Security audits in progress
- Performance benchmarking ongoing
- Documentation being finalized

?? **Deployment Ready:** All scripts tested and validated for VPS deployment

---

## Overview

This repository contains everything needed to deploy the complete **axionax
Protocol** infrastructure stack on a VPS or cloud environment.

### Part of axionax Ecosystem

Deploys the full Axionax Protocol stack:

- **Protocol Node**: [`../axionax-core`](../axionax-core) - axionax RPC node
- **Block Explorer**: Blockchain data visualization
- **Testnet Faucet**: AXX token distribution
- **Web Interface**: [`../axionax-web`](../axionax-web) - Static frontend
- **Monitoring**: Prometheus + Grafana dashboards
- **Issue Tracker**: [`../issue-manager`](../issue-manager) - Track deployment tasks

**Main Repository**:
[axionaxprotocol/axionaxiues](https://github.com/axionaxprotocol/axionaxiues)

**Pre-Testnet Status:** Infrastructure ready, final testing in progress

---

## What Gets Deployed

### Axionax Protocol Services

- **RPC Node (Port 8545/8546)** - Full Axionax Protocol node
  - HTTP JSON-RPC endpoint
  - WebSocket support
  - CORS enabled
  - Health monitoring
- **Block Explorer (Port 3001)** - Blockchain visualization
  - Real-time axionax block data
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
  - axionax node health alerts

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

### Minimum Requirements (Testing)

- Ubuntu 20.04+ or Debian 11+
- **4GB RAM**, 2 CPU cores
- 50GB+ SSD storage
- Root or sudo access
- Domain with DNS access

### Recommended for Pre-Testnet (Current Phase)

- Ubuntu 22.04 LTS
- **8GB RAM**, 4 CPU cores
- 100GB NVMe SSD
- 2TB+ bandwidth/month
- Dedicated IP
- Cost: ~$20-40/month

### Production Testnet (After Launch)

- Ubuntu 22.04 LTS
- **16GB RAM**, 8 CPU cores
- 200GB NVMe SSD
- 4TB+ bandwidth/month
- DDoS protection
- Cost: ~$60-100/month

## Pre-Launch Checklist

Use our [Issue Manager](../issue-manager) to track:

- [ ] ??? Infrastructure monitoring setup
- [ ] ?? Load testing completed
- [ ] ?? Backup systems verified
- [ ] ?? Security audit passed
- [ ] ?? Scaling strategy tested
- [ ] ?? Documentation complete

## Documentation

- **[VPS_DEPLOYMENT.md](VPS_DEPLOYMENT.md)** - Complete deployment guide
- **[TESTNET_LAUNCH_CHECKLIST.md](TESTNET_LAUNCH_CHECKLIST.md)** - Pre-launch checklist
- **[GITHUB_PAGES_SETUP.md](GITHUB_PAGES_SETUP.md)** - Website hosting setup

## Axionax Protocol Ecosystem

| Component         | Description               | Location                                         | Status     |
| ----------------- | ------------------------- | ------------------------------------------------ | ---------- |
| **Deploy** (this) | Infrastructure deployment | `axionax-deploy/`                                | ?? Testing |
| **Core**          | Axionax Protocol node     | [`../axionax-core`](../axionax-core)             | ? Ready   |
| **Web**           | Frontend interface        | [`../axionax-web`](../axionax-web)               | ? Ready   |
| **SDK**           | Developer SDK             | [`../axionax-sdk-ts`](../axionax-sdk-ts)         | ? Ready   |
| **Docs**          | Documentation             | [`../axionax-docs`](../axionax-docs)             | ?? Active  |
| **DevTools**      | Development tools         | [`../axionax-devtools`](../axionax-devtools)     | ? Ready   |
| **Marketplace**   | Compute marketplace       | [`../axionax-marketplace`](../axionax-marketplace) | ?? Beta  |
| **Issue Manager** | Task tracking             | [`../issue-manager`](../issue-manager)           | ?? New!    |

---

## Related Projects

### Core Components

- **[Protocol Core](../axionax-core)** - axionax blockchain implementation
- **[Web Interface](../axionax-web)** - Frontend (deployed separately)
- **[SDK](../axionax-sdk-ts)** - Used by Explorer/Faucet APIs
- **[Documentation](../axionax-docs)** - Full protocol documentation

### External Resources

- **Main Repository**: https://github.com/axionaxprotocol/axionaxiues
- **Protocol Website**: https://axionax.org
- **Documentation**: https://docs.axionax.org

---

## Contributing

1. Fork the main repository:
   [axionaxprotocol/axionax-core](https://github.com/axionaxprotocol/axionax-core)
2. Create feature branch
3. Work in the `deploy/` directory
4. Test changes with Docker Compose locally
5. Submit pull request

---

## License

MIT License - see [LICENSE](LICENSE) for details.

**Note**: The Axionax Protocol Core uses AGPLv3. See
[`../axionax-core/LICENSE`](../axionax-core/LICENSE).

---

## Support

- **Issues**: https://github.com/axionaxprotocol/axionax-core/issues
- **Docs**: https://docs.axionax.org or [`../axionax-docs`](../axionax-docs)
- **Main Repository**: https://github.com/axionaxprotocol/axionaxiues

### Community (Coming Q1 2026)

- **Discord**: https://discord.gg/axionax
- **Twitter**: https://twitter.com/axionaxprotocol

---

**Part of the Axionax Protocol Ecosystem**

Built with ?? by the axionax team

**Last Updated**: November 7, 2025
