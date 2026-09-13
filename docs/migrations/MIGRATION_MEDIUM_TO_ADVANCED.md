# Migration Guide: Medium → Advanced Workflow

This guide helps teams upgrade from the Medium workflow to the Advanced workflow, adding automated rollback, health checks, APM, database migrations, and feature flags.

## Overview of Changes

| Aspect | Medium | Advanced |
|--------|--------|----------|
| Automated Rollback | ❌ | ✅ On health check failure |
| Health Checks | ❌ | ✅ Staging + Production |
| APM / Metrics | ❌ | ✅ Full integration |
| Database Migrations | ❌ | ✅ Automated with backup |
| Feature Flags | ❌ | ✅ Controlled rollouts |
| Blue-Green Deploy | ❌ | ✅ Zero-downtime |
| Multi-arch Docker | ❌ | ✅ amd64 + arm64 |
| Pipeline Stages | 9 | 16 |

## Step-by-Step Migration

### 1. Prerequisites

Before migrating, ensure you have:

- [ ] **Infrastructure**: Kubernetes cluster or VM fleet with load balancer
- [ ] **Monitoring Stack**: Prometheus + Grafana, or Datadog/New Relic
- [ ] **APM Tool**: Elastic APM, Datadog APM, New Relic, or OpenTelemetry
- [ ] **Feature Flag Service**: LaunchDarkly, Unleash, Flagsmith, or custom
- [ ] **Database Migration Tool**: Flyway, Liquibase, Prisma Migrate, or custom
- [ ] **Backup/DR**: Automated database backups tested
- [ ] **Team Training**: On-call rotation, incident response procedures

### 2. Infrastructure Setup

#### Kubernetes (Recommended)
```yaml
# kubernetes/namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
---
# kubernetes/blue-green-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: app
        image: ${CI_REGISTRY_IMAGE}:${CI_COMMIT_SHA}
        ports:
        - containerPort: 3000
        envFrom:
        - secretRef:
            name: app-secrets
---
apiVersion: v1
kind: Service
metadata:
  name: app-production
  namespace: production
spec:
  selector:
    app: myapp
    version: blue  # Switch to green for rollout
  ports:
  - port: 80
    targetPort: 3000
```

#### Load Balancer Configuration
- Configure health check endpoint: `/health`
- Set up traffic splitting for blue-green
- Configure TLS termination

### 3. Update `.gitlab-ci.yml`

Replace your medium pipeline with the advanced template:

```bash
cp examples/advanced/.gitlab-ci.yml .gitlab-ci.yml
```

Key additions in advanced pipeline:

```yaml
# New stages added
stages:
  - feature-flags      # NEW: Feature flag setup
  - db-migration-staging # NEW: DB migrations for staging
  - performance-test   # NEW: k6 performance tests
  - health-check-staging # NEW: Health validation
  - db-migration-production # NEW: DB migrations for prod
  - health-check-production # NEW: Production health checks
  - rollback           # NEW: Automated rollback
  - monitoring         # NEW: APM + alerting setup

# New jobs
feature-flags-setup:
  stage: feature-flags
  # ... Feature flag configuration

db-migration-staging:
  stage: db-migration-staging
  # ... Run migrations on staging

health-check-staging:
  stage: health-check-staging
  # ... Validate staging deployment

deploy-production:
  stage: deploy-production
  # ... Blue-green deployment

db-migration-production:
  stage: db-migration-production
  # ... Run migrations with backup

health-check-production:
  stage: health-check-production
  # ... Validate production deployment

rollback-production:
  stage: rollback
  when: on_failure
  # ... Automated rollback
```

### 4. Configure Health Checks

Add health check endpoint to your application:

```javascript
// src/health.js (Express.js example)
app.get('/health', async (req, res) => {
  const checks = {
    status: 'healthy',
    timestamp: new Date().toISOString(),
    checks: {}
  };
  
  // Database connectivity
  try {
    await db.query('SELECT 1');
    checks.checks.database = 'healthy';
  } catch (e) {
    checks.checks.database = 'unhealthy';
    checks.status = 'unhealthy';
  }
  
  // Redis connectivity
  try {
    await redis.ping();
    checks.checks.redis = 'healthy';
  } catch (e) {
    checks.checks.redis = 'unhealthy';
    checks.status = 'degraded';
  }
  
  // External dependencies
  checks.checks.externalApi = await checkExternalApi();
  
  const statusCode = checks.status === 'healthy' ? 200 : 503;
  res.status(statusCode).json(checks);
});
```

### 5. Set Up Database Migrations

Create migration scripts:

```bash
# scripts/migrate.sh
#!/bin/bash
set -e

ENVIRONMENT=$1
TAG=$2
BACKUP_FLAG=$3

echo "Running migrations for $ENVIRONMENT with tag $TAG"

# Pre-migration backup (production only)
if [ "$ENVIRONMENT" = "production" ] && [ "$BACKUP_FLAG" = "--backup" ]; then
  echo "Creating pre-migration backup..."
  pg_dump -h $DB_HOST -U $DB_USER $DB_NAME > "/backups/pre-migration-$(date +%Y%m%d-%H%M%S).sql"
fi

# Run migrations
docker run --rm \
  -e DATABASE_URL=$DATABASE_URL \
  $TAG \
  migrate up

# Verify migration
docker run --rm \
  -e DATABASE_URL=$DATABASE_URL \
  $TAG \
  migrate version

echo "Migrations completed successfully"
```

### 6. Configure Feature Flags

Set up feature flag service integration:

```javascript
// scripts/setup-feature-flags.js
const fetch = require('node-fetch');

async function setupFeatureFlags(commitSha, branch) {
  const flags = [
    { key: 'new-checkout-flow', defaultVariation: false },
    { key: 'dark-mode', defaultVariation: true },
    { key: 'beta-features', defaultVariation: false }
  ];
  
  for (const flag of flags) {
    await fetch(`${process.env.FEATURE_FLAG_SERVICE_URL}/api/flags`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.FEATURE_FLAG_TOKEN}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        project: 'myapp',
        environment: branch === 'main' ? 'production' : 'staging',
        flag: flag.key,
        ...flag
      })
    });
  }
  
  console.log('Feature flags configured for commit:', commitSha);
}

setupFeatureFlags(process.argv[2], process.argv[3]);
```

### 7. Integrate APM

Add APM agent to your application:

```dockerfile
# Dockerfile (add to existing)
# For Node.js with Elastic APM
RUN npm install elastic-apm-node --save

# For Python with Elastic APM
RUN pip install elastic-apm[flask]

# For Java
# ADD https://github.com/elastic/apm-agent-java/releases/download/v1.38.0/elastic-apm-agent-1.38.0.jar /elastic-apm-agent.jar
```

```javascript
// src/apm.js (Node.js example)
const apm = require('elastic-apm-node').start({
  serviceName: 'myapp',
  serverUrl: process.env.APM_SERVER_URL,
  secretToken: process.env.APM_TOKEN,
  environment: process.env.NODE_ENV,
  captureBody: 'transactions',
  metricsInterval: '30s',
  centralConfig: true
});

module.exports = apm;
```

### 8. Add Performance Tests

Create k6 performance test:

```javascript
// scripts/performance-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate } from 'k6/metrics';

const errorRate = new Rate('errors');

export const options = {
  stages: [
    { duration: '2m', target: 50 },   // Ramp up
    { duration: '5m', target: 100 },  // Stay at 100 users
    { duration: '2m', target: 200 },  // Stress test
    { duration: '2m', target: 0 },    // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'], // 95% of requests < 500ms
    errors: ['rate<0.01'],             // Error rate < 1%
  },
};

export default function () {
  const baseUrl = __ENV.BASE_URL || 'https://staging.example.com';
  
  // Test critical endpoints
  const endpoints = [
    '/api/health',
    '/api/users',
    '/api/products',
    '/api/checkout',
  ];
  
  for (const endpoint of endpoints) {
    const res = http.get(`${baseUrl}${endpoint}`);
    check(res, { 
      [`${endpoint} status 200`]: (r) => r.status === 200,
      [`${endpoint} response time < 500ms`]: (r) => r.timings.duration < 500,
    });
    errorRate.add(res.status !== 200);
    sleep(1);
  }
}
```

### 9. Configure Monitoring & Alerting

```yaml
# monitoring/prometheus-rules.yaml
groups:
- name: deployment-alerts
  rules:
  - alert: DeploymentFailed
    expr: increase(gitlab_ci_job_status{status="failed"}[5m]) > 0
    for: 1m
    labels:
      severity: critical
    annotations:
      summary: "Deployment failed for {{ $labels.project }}"
      
  - alert: HealthCheckFailing
    expr: up{job="myapp"} == 0
    for: 2m
    labels:
      severity: critical
    annotations:
      summary: "Health check failing for {{ $labels.instance }}"
      
  - alert: HighErrorRate
    expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
    for: 3m
    labels:
      severity: warning
    annotations:
      summary: "High error rate on {{ $labels.instance }}"
```

### 10. Test the Migration

1. **Create feature branch**: `git checkout -b migrate-to-advanced`
2. **Apply all changes** and push
3. **Verify pipeline runs** all new stages
4. **Test rollback manually**: Trigger manual rollback job
5. **Verify feature flags** work in staging
6. **Run performance tests** and review results
7. **Check APM dashboard** for deployment tracking
8. **Merge to main** after validation

### 11. Rollback Plan

The advanced pipeline includes automated rollback, but you can also:

```bash
# Manual rollback via GitLab UI
# Go to CI/CD → Pipelines → Run manual-rollback job
# Or via API:
curl -X POST "https://gitlab.com/api/v4/projects/$CI_PROJECT_ID/jobs/$JOB_ID/play" \
  -H "PRIVATE-TOKEN: $GITLAB_TOKEN"
```

## New Files to Add

```
your-project/
├── .gitlab-ci.yml              # Updated from examples/advanced/
├── scripts/
│   ├── migrate.sh              # Database migrations
│   ├── rollback.sh             # Rollback script
│   ├── setup-feature-flags.js  # Feature flag setup
│   ├── setup-monitoring.js     # Monitoring configuration
│   ├── performance-test.js     # k6 performance test
│   └── health.js               # Health check endpoint
├── kubernetes/
│   ├── namespace.yaml
│   ├── blue-green-deploy.yaml
│   └── service.yaml
├── monitoring/
│   ├── prometheus-rules.yaml
│   └── grafana-dashboard.json
├── Dockerfile                  # Updated with APM agent
└── .dockerignore
```

## Timeline Estimate

| Task | Effort |
|------|--------|
| Infrastructure setup (K8s, monitoring) | 2-5 days |
| Update CI/CD configuration | 4-8 hours |
| Health checks & DB migrations | 1-2 days |
| Feature flags setup | 4-8 hours |
| APM integration | 4-8 hours |
| Performance tests | 4-8 hours |
| Testing & validation | 1-2 days |
| **Total** | **1-2 weeks** |

## Next Steps After Migration

1. **Fine-tune alerting thresholds** based on baseline metrics
2. **Implement chaos engineering** (Gremlin, Chaos Mesh)
3. **Add distributed tracing** (Jaeger, Zipkin)
4. **Set up SLO/SLI dashboards**
5. **Document runbooks** for common incidents
6. **Conduct disaster recovery drills**

## Resources

- [GitLab Auto Rollback](https://docs.gitlab.com/ee/ci/environments/#auto-rollback)
- [Kubernetes Blue-Green Deploy](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#blue-green-deployment)
- [k6 Performance Testing](https://k6.io/docs/)
- [Elastic APM](https://www.elastic.co/observability/application-performance-monitoring)
- [OpenTelemetry](https://opentelemetry.io/)
- [LaunchDarkly Feature Flags](https://launchdarkly.com/)
- [Unleash Feature Flags](https://www.getunleash.io/)
- [Flyway Migrations](https://flywaydb.org/)