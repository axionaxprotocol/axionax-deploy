# Axionax Public Testnet Launch Checklist

## 🎯 Pre-Launch (Current Phase)

### Genesis Configuration
- [ ] Generate genesis.json with 4 validators
- [ ] Allocate initial tokens (Faucet: 10M, Team: 100M, Rewards: 200M)
- [ ] Set PoPC consensus parameters
- [ ] Create validator keypairs
- [ ] **Action**: Run `python scripts/generate-genesis.py`

### Infrastructure Setup
- [ ] Setup 4 validator VPS nodes (minimum specs)
- [ ] Configure firewalls and security
- [ ] Setup monitoring (Prometheus + Grafana)
- [ ] Deploy RPC node
- [ ] Deploy Block Explorer
- [ ] Deploy Faucet service
- [ ] **Action**: Run `./setup-vps.sh` on each VPS

### GitHub Configuration
- [ ] Enable GitHub Pages (axionax-web)
  - Repository: axionaxprotocol/axionax-web
  - Settings → Pages → Source: main/public
  
- [ ] Configure GitHub Secrets (axionax-web)
  - `VERCEL_TOKEN` (if using Vercel)
  - `VERCEL_ORG_ID`
  - `VERCEL_PROJECT_ID`
  
- [ ] Configure GitHub Secrets (axionax-sdk-ts)
  - `NPM_TOKEN` (for package publishing)
  - `TEST_PRIVATE_KEY` (for integration tests)
  - `DISCORD_WEBHOOK` (for notifications)
  
- [ ] Configure GitHub Secrets (axionax-deploy)
  - `VPS_SSH_KEY` (for deployment)
  - `DOCKER_USERNAME`
  - `DOCKER_PASSWORD`

### DNS Configuration
- [ ] Point main domain to GitHub Pages
  ```
  Type: A, Name: @, Value: 185.199.108.153
  Type: A, Name: @, Value: 185.199.109.153
  Type: A, Name: @, Value: 185.199.110.153
  Type: A, Name: @, Value: 185.199.111.153
  Type: CNAME, Name: www, Value: axionaxprotocol.github.io
  ```

- [ ] Point subdomains to VPS
  ```
  Type: A, Name: rpc, Value: YOUR_VPS_IP
  Type: A, Name: explorer, Value: YOUR_VPS_IP
  Type: A, Name: faucet, Value: YOUR_VPS_IP
  Type: A, Name: validator1, Value: VALIDATOR1_IP
  Type: A, Name: validator2, Value: VALIDATOR2_IP
  Type: A, Name: validator3, Value: VALIDATOR3_IP
  Type: A, Name: validator4, Value: VALIDATOR4_IP
  ```

### Security Audit
- [ ] Review all smart contracts
- [ ] Check for hardcoded secrets
- [ ] Verify firewall rules
- [ ] Setup fail2ban on all servers
- [ ] Enable 2FA on all accounts
- [ ] Create backup strategy

---

## 🚀 Launch Day (Day 0)

### Morning (08:00 UTC)

#### 1. Deploy Core Infrastructure (08:00-09:00)
```bash
# On each validator node
cd /opt/axionax-core
./target/release/axionax-node init --chain-id axionax-testnet-1
cp genesis.json ~/.axionax/config/
```

- [ ] Start Validator 1
- [ ] Start Validator 2
- [ ] Start Validator 3
- [ ] Start Validator 4
- [ ] Verify all validators are connected
- [ ] Check block production (first block!)

#### 2. Deploy Services (09:00-10:00)
```bash
# On services VPS
cd /opt/axionax-deploy
docker-compose -f docker-compose.vps.yml up -d
```

- [ ] Start RPC node
- [ ] Start Explorer backend
- [ ] Start Faucet service
- [ ] Start Monitoring stack
- [ ] Verify SSL certificates

#### 3. Health Checks (10:00-10:30)
- [ ] RPC endpoint: `curl https://rpc.axionax.org/health`
- [ ] Explorer: Visit https://explorer.axionax.org
- [ ] Faucet: Visit https://faucet.axionax.org
- [ ] Grafana: Check all metrics green
- [ ] Test transaction submission
- [ ] Verify block times (~6 seconds)

### Afternoon (12:00 UTC)

#### 4. Public Announcement (12:00-13:00)
- [ ] Publish launch announcement on website
- [ ] Post on Twitter/X
- [ ] Announce in Discord
- [ ] Update GitHub README
- [ ] Send email to early testers

#### 5. Documentation Update (13:00-14:00)
- [ ] Update docs with testnet endpoints
- [ ] Publish faucet instructions
- [ ] Create validator onboarding guide
- [ ] Update API reference
- [ ] Publish integration examples

#### 6. Community Engagement (14:00-18:00)
- [ ] Monitor Discord for questions
- [ ] Help users connect to testnet
- [ ] Debug any issues reported
- [ ] Collect feedback
- [ ] Update FAQ

---

## 📊 Post-Launch Monitoring (Day 1-7)

### Daily Checks
- [ ] Check validator uptime (all >99%)
- [ ] Monitor block production (no gaps)
- [ ] Review error logs
- [ ] Check faucet distribution
- [ ] Monitor RPC request volume
- [ ] Review security alerts

### Weekly Metrics
- [ ] Total transactions
- [ ] Active addresses
- [ ] Validator performance
- [ ] Network uptime
- [ ] Bug reports count
- [ ] Community growth

---

## 🎯 Success Criteria

### Technical Metrics
- ✅ All 4 validators online
- ✅ Block time: ~6 seconds
- ✅ Network uptime: >99%
- ✅ RPC response time: <500ms
- ✅ Zero consensus failures

### User Metrics
- ✅ >100 faucet requests in first week
- ✅ >50 unique wallet addresses
- ✅ >1000 total transactions
- ✅ <5 critical bug reports

### Community Metrics
- ✅ >200 Discord members
- ✅ >10 community validators
- ✅ >5 dApps building on testnet
- ✅ Positive community sentiment

---

## 🚨 Emergency Procedures

### Network Halt
1. Stop all validators immediately
2. Identify root cause
3. Fix issue and create new genesis if needed
4. Communicate with community
5. Restart coordinated launch

### Security Breach
1. Halt affected services
2. Revoke compromised keys
3. Audit all systems
4. Deploy fixes
5. Communicate transparently

### RPC Overload
1. Enable rate limiting
2. Scale up resources
3. Deploy additional RPC nodes
4. Implement caching layer

---

## 📞 Contact Information

### Team Roles
- **Launch Coordinator**: [Name]
- **DevOps Lead**: [Name]
- **Security Lead**: [Name]
- **Community Manager**: [Name]

### Emergency Contacts
- Technical Issues: tech@axionax.org
- Security: security@axionax.org
- General: hello@axionax.org

### Communication Channels
- Discord: https://discord.gg/axionax
- Twitter: @AxionaxProtocol
- Telegram: https://t.me/axionax
- Status Page: https://status.axionax.org

---

## 📝 Post-Launch Report Template

After 7 days, create report with:

### Network Statistics
- Total blocks produced
- Average block time
- Total transactions
- Unique addresses
- Validator uptime

### Issues Encountered
- List all bugs/issues
- Resolution status
- Time to resolution

### Community Feedback
- Top feature requests
- Common pain points
- Positive feedback

### Next Steps
- Immediate fixes needed
- Short-term improvements
- Long-term roadmap updates

---

## ✅ Final Pre-Launch Verification

**24 hours before launch:**

- [ ] All team members briefed
- [ ] Emergency procedures documented
- [ ] Backup systems tested
- [ ] Monitoring alerts configured
- [ ] Community announcement drafted
- [ ] Support team ready
- [ ] Rollback plan documented

**1 hour before launch:**

- [ ] All services green
- [ ] DNS propagated
- [ ] SSL certificates valid
- [ ] Genesis file distributed
- [ ] Team in communication channel
- [ ] Monitoring dashboards open

**T-0 (Launch!):**

- [ ] Start validator nodes
- [ ] Verify block production
- [ ] Start services
- [ ] Publish announcement
- [ ] Monitor closely

---

**Good luck! 🚀**
