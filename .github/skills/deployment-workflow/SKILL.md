---
name: deployment-workflow
description: Comprehensive procedures and best practices for safe, reliable, and repeatable deployments to production and other environments.
---

# Deployment Workflow

## Overview

This skill provides systematic procedures for deploying applications safely and reliably. Follow these guidelines to minimize downtime, prevent errors, and ensure successful deployments.

## When to Use

- Before deploying to staging or production
- When setting up new deployment pipelines
- When planning releases
- During incident response and rollbacks

## Deployment Philosophy

### Core Principles

1. **Automate everything**: Manual steps lead to errors
2. **Deploy frequently**: Small, frequent deploys reduce risk
3. **Always be ready to rollback**: Have a plan B
4. **Monitor continuously**: Know when something goes wrong
5. **Test in production-like environments**: Catch issues before prod

## Pre-Deployment Checklist

Complete this checklist **before** any production deployment:

### Code & Tests
- [ ] All tests pass (unit, integration, e2e)
- [ ] Code review approved
- [ ] No known critical bugs
- [ ] Security scan passed
- [ ] Performance tests passed (if applicable)
- [ ] Branch is up-to-date with main/production

### Documentation
- [ ] CHANGELOG updated
- [ ] API documentation updated
- [ ] Deployment notes prepared
- [ ] Rollback procedure documented
- [ ] Known issues documented

### Database
- [ ] Database migrations tested
- [ ] Migrations are reversible
- [ ] Backup created and verified
- [ ] Migration rollback tested
- [ ] Data seeding scripts ready (if needed)

### Configuration
- [ ] Environment variables configured
- [ ] Feature flags set appropriately
- [ ] Third-party service configurations verified
- [ ] SSL/TLS certificates valid
- [ ] DNS records configured

### Infrastructure
- [ ] Server capacity checked
- [ ] Auto-scaling configured
- [ ] Load balancers configured
- [ ] CDN cache invalidation prepared
- [ ] Monitoring and alerts configured

### Communication
- [ ] Stakeholders notified
- [ ] Deployment window scheduled
- [ ] Team availability confirmed
- [ ] Status page prepared (if customer-facing)
- [ ] Support team briefed

## Deployment Process

### Step 1: Final Verification

```bash
# Run all tests one final time
npm run test:all
npm run lint
npm run type-check

# Check for security vulnerabilities
npm audit
# or
pip-audit
# or
bundle audit

# Verify build succeeds
npm run build

# Check bundle size (if web app)
npm run analyze
```

### Step 2: Create Release

```bash
# Tag the release
git tag -a v1.2.3 -m "Release version 1.2.3"
git push origin v1.2.3

# Generate release notes
git log --oneline --decorate $(git describe --tags --abbrev=0)..HEAD

# Create GitHub release (or use UI)
gh release create v1.2.3 --title "Release 1.2.3" --notes "Release notes here"
```

### Step 3: Deploy to Staging

Always deploy to staging first:

```bash
# Deploy to staging environment
./scripts/deploy.sh staging

# Or using CI/CD
git push origin main # Triggers staging deployment
```

**Staging validation:**
```bash
# Smoke tests
curl https://staging.example.com/health
curl https://staging.example.com/api/v1/status

# Run automated tests against staging
npm run test:e2e -- --env=staging

# Manual verification
- [ ] Critical user flows work
- [ ] Database migrations applied correctly
- [ ] New features function as expected
- [ ] Old features still work
```

### Step 4: Database Migration (if needed)

```bash
# Backup production database
./scripts/backup-db.sh production

# Verify backup
./scripts/verify-backup.sh

# Run migrations (with option to rollback)
npm run migrate:up

# Verify migrations
npm run migrate:verify
```

**Migration best practices:**
```sql
-- ✓ Good: Backwards compatible migration
-- Step 1: Add new column (nullable)
ALTER TABLE users ADD COLUMN new_field VARCHAR(255) NULL;

-- Step 2: Deploy code that writes to both old and new fields
-- Step 3: Backfill data
UPDATE users SET new_field = old_field WHERE new_field IS NULL;

-- Step 4: Deploy code that reads from new field
-- Step 5: Make column non-nullable (separate deployment)
ALTER TABLE users MODIFY COLUMN new_field VARCHAR(255) NOT NULL;

-- Step 6: Remove old column (separate deployment)
ALTER TABLE users DROP COLUMN old_field;
```

### Step 5: Deploy to Production

```bash
# Trigger production deployment
./scripts/deploy.sh production

# Or via CI/CD
gh workflow run deploy.yml --ref v1.2.3

# Monitor deployment progress
kubectl rollout status deployment/app-name
# or
aws deploy get-deployment --deployment-id d-XXXXX
```

### Step 6: Post-Deployment Verification

**Immediate checks (first 5 minutes):**

```bash
# Health check
curl https://api.example.com/health

# Check application logs
kubectl logs -f deployment/app-name --tail=100
# or
aws logs tail /aws/ecs/app-name --follow

# Verify key metrics
- [ ] Error rate is normal
- [ ] Response times are acceptable
- [ ] No spike in 5xx errors
- [ ] CPU/Memory usage is normal
```

**Functional verification:**
```bash
# Test critical paths
curl -X POST https://api.example.com/api/v1/auth/login
curl https://api.example.com/api/v1/users/me

# Run smoke tests
npm run test:smoke -- --env=production
```

**User verification:**
- [ ] Login flow works
- [ ] Key features are accessible
- [ ] Payment processing works (if applicable)
- [ ] Email notifications are sent
- [ ] Third-party integrations work

### Step 7: Monitoring (first 24 hours)

**What to monitor:**

```javascript
// Application metrics
- Request rate
- Error rate (4xx, 5xx)
- Response times (p50, p95, p99)
- Throughput

// Infrastructure metrics
- CPU usage
- Memory usage
- Disk I/O
- Network I/O

// Business metrics
- User signups
- Conversion rates
- Revenue
- Active users
```

**Set up alerts:**
```yaml
# Example: Datadog monitor
name: "High Error Rate"
message: "Error rate above 5% for 5 minutes"
query: "avg(last_5m):sum:requests.error{env:production} / sum:requests.total{env:production} > 0.05"
```

## Deployment Strategies

### 1. Rolling Deployment

Deploy to instances gradually:

```bash
# Update instances one at a time
for instance in $(kubectl get pods -l app=myapp -o name); do
  kubectl set image $instance app=myapp:v1.2.3
  sleep 30  # Wait between updates
  kubectl rollout status deployment/myapp
done
```

**Pros:**
- No downtime
- Gradual rollout reduces risk

**Cons:**
- Slower deployment
- Temporary version inconsistency

### 2. Blue-Green Deployment

Maintain two identical environments:

```bash
# Deploy to green (inactive) environment
./deploy.sh green

# Test green environment
./test.sh green

# Switch traffic from blue to green
./switch-traffic.sh blue->green

# Keep blue as fallback
# After 24h, decommission blue
```

**Pros:**
- Instant rollback
- Zero downtime
- Full testing before switch

**Cons:**
- Double infrastructure cost
- Complex setup

### 3. Canary Deployment

Deploy to small percentage of users first:

```yaml
# Example: Route 10% to new version
apiVersion: v1
kind: Service
spec:
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 80
---
# New version deployment with 10% traffic
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-canary
spec:
  replicas: 1  # 10% of total
  selector:
    matchLabels:
      app: myapp
      version: v1.2.3
```

**Monitoring canary:**
```bash
# Compare metrics between versions
- Error rate: canary vs stable
- Response time: canary vs stable
- Resource usage: canary vs stable

# Gradually increase canary traffic
10% -> 25% -> 50% -> 100%
```

**Pros:**
- Low-risk progressive rollout
- Real user testing

**Cons:**
- Complex traffic routing
- Requires sophisticated monitoring

### 4. Feature Flags

Deploy code but control feature activation:

```javascript
// Backend
const useNewAlgorithm = await featureFlags.isEnabled('new-algorithm', userId);
if (useNewAlgorithm) {
  return newAlgorithm(data);
} else {
  return oldAlgorithm(data);
}

// Frontend
import { useFeatureFlag } from '@/utils/feature-flags';

function MyComponent() {
  const showNewUI = useFeatureFlag('new-ui-design');
  
  if (showNewUI) {
    return <NewUI />;
  }
  return <OldUI />;
}
```

**Pros:**
- Deploy anytime, enable later
- Easy rollback (just toggle flag)
- Progressive rollout
- A/B testing

**Cons:**
- Code complexity
- Technical debt if flags not removed

## Rollback Procedures

### When to Rollback

Rollback immediately if:
- Error rate increases significantly (>5%)
- Critical feature is broken
- Data corruption detected
- Security vulnerability discovered
- Performance degrades severely

### Rollback Methods

#### 1. Revert to Previous Version

```bash
# Kubernetes
kubectl rollout undo deployment/myapp

# Verify rollback
kubectl rollout status deployment/myapp

# Or rollback to specific revision
kubectl rollout undo deployment/myapp --to-revision=2
```

#### 2. Database Rollback

```bash
# Rollback migrations
npm run migrate:down

# Restore from backup (if necessary)
./scripts/restore-db.sh backup-2024-01-15-10-00.sql

# Verify data integrity
npm run db:verify
```

#### 3. Traffic Rerouting

```bash
# Blue-green: Switch back to previous environment
./switch-traffic.sh green->blue

# Canary: Remove canary deployment
kubectl delete deployment myapp-canary
```

#### 4. Feature Flag Toggle

```bash
# Disable problematic feature
curl -X POST https://api.example.com/admin/feature-flags \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -d '{"flag": "new-feature", "enabled": false}'
```

### Post-Rollback

- [ ] Verify system is stable
- [ ] Notify stakeholders
- [ ] Create incident report
- [ ] Analyze root cause
- [ ] Fix issues in development
- [ ] Plan next deployment attempt

## Environment-Specific Guidelines

### Development
- Deploy automatically on commit
- No approval required
- Can be unstable

### Staging
- Deploy automatically when tests pass
- Should mirror production
- Used for QA and testing

### Production
- Deploy during low-traffic windows
- Require manual approval
- Must be stable and tested
- Communication to stakeholders required

## CI/CD Pipeline Example

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        run: npm test
      
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build
        run: npm run build
      - name: Upload artifact
        uses: actions/upload-artifact@v3
        with:
          name: build
          path: dist/
  
  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - name: Deploy to staging
        run: ./scripts/deploy.sh staging
      - name: Run smoke tests
        run: npm run test:smoke -- --env=staging
  
  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to production
        run: ./scripts/deploy.sh production
      - name: Verify deployment
        run: ./scripts/verify-deployment.sh production
      - name: Notify team
        run: ./scripts/notify-slack.sh "Deployment complete"
```

## Deployment Metrics

Track these metrics for continuous improvement:

### Deployment Frequency
- How often you deploy
- Goal: Multiple times per day

### Lead Time
- Time from commit to production
- Goal: < 1 hour

### Mean Time to Recovery (MTTR)
- Time to recover from failure
- Goal: < 1 hour

### Change Failure Rate
- Percentage of deployments causing issues
- Goal: < 15%

## Troubleshooting Common Issues

### Deployment Fails

```bash
# Check deployment logs
kubectl logs deployment/myapp --previous

# Check events
kubectl describe deployment myapp

# Verify image exists
docker pull myapp:v1.2.3
```

### Application Won't Start

```bash
# Check application logs
kubectl logs -f pod/myapp-xxxxx

# Check environment variables
kubectl exec pod/myapp-xxxxx -- env

# Check resource limits
kubectl describe pod myapp-xxxxx
```

### Database Migration Fails

```bash
# Check migration logs
cat /var/log/migrations.log

# Verify database connectivity
psql -h db-host -U user -d database -c "SELECT 1"

# Check for locks
SELECT * FROM pg_locks WHERE NOT granted;
```

## Best Practices

### DO:
✓ Deploy during low-traffic periods
✓ Keep deployments small and frequent
✓ Automate everything possible
✓ Have monitoring in place before deploying
✓ Test rollback procedures regularly
✓ Communicate with stakeholders
✓ Use version tags for releases
✓ Document the deployment process

### DON'T:
✗ Deploy on Fridays (unless necessary)
✗ Deploy multiple changes at once
✗ Skip testing in staging
✗ Deploy without a rollback plan
✗ Make manual changes in production
✗ Deploy during peak hours
✗ Ignore monitoring after deployment
✗ Deploy when key team members are unavailable

## Security Considerations

- [ ] Secrets are stored securely (not in code)
- [ ] Access controls are properly configured
- [ ] SSL/TLS certificates are valid
- [ ] Dependencies are up-to-date
- [ ] Security headers are configured
- [ ] Audit logs are enabled
- [ ] Compliance requirements are met

## Documentation Templates

### Deployment Plan
```markdown
# Deployment Plan: Release v1.2.3

**Date**: 2024-01-15
**Time**: 10:00 AM UTC (low traffic period)
**Duration**: ~30 minutes
**Rollback Time**: ~10 minutes

## Changes
- Feature: New user dashboard
- Fix: Payment processing bug
- Update: Security patches

## Database Changes
- Add column: users.preferences
- Add index: users.email
- Migration time: ~5 minutes

## Rollback Plan
1. Revert deployment: `kubectl rollout undo`
2. Rollback migrations: `npm run migrate:down`
3. Estimated time: 10 minutes

## Verification Steps
1. Check health endpoint
2. Test login flow
3. Verify new dashboard loads
4. Test payment processing

## Team
- Deployment lead: @engineer1
- Database: @dba1
- On-call: @engineer2
- Communication: @pm1
```

### Incident Report
```markdown
# Deployment Incident Report

**Date**: 2024-01-15
**Severity**: High
**Duration**: 45 minutes

## What Happened
Brief description of the incident.

## Impact
- 5% of users experienced errors
- No data loss
- No security breach

## Root Cause
Detailed explanation of what went wrong.

## Timeline
- 10:00: Deployment started
- 10:15: Error rate increased
- 10:20: Issue detected
- 10:25: Rollback initiated
- 10:45: Service restored

## Resolution
How the issue was resolved.

## Action Items
- [ ] Fix the bug (TICKET-123)
- [ ] Add test coverage (TICKET-124)
- [ ] Improve monitoring (TICKET-125)
- [ ] Update deployment checklist

## Lessons Learned
What we learned and how to prevent similar issues.
```

## Resources

- [The Twelve-Factor App](https://12factor.net/)
- [Continuous Delivery Book](https://continuousdelivery.com/)
- [AWS Deployment Best Practices](https://aws.amazon.com/architecture/well-architected/)
- [Google SRE Book](https://sre.google/books/)
- [Kubernetes Deployment Strategies](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
