# GitLab CI/CD Examples

This directory contains ready-to-use `.gitlab-ci.yml` templates for each workflow tier.

## Structure

```
examples/
├── simple/          # Basic CI/CD pipeline
│   └── .gitlab-ci.yml
├── medium/          # Security & compliance enhanced
│   └── .gitlab-ci.yml
└── advanced/        # Enterprise-grade with full automation
    └── .gitlab-ci.yml
```

## Quick Start

### 1. Choose Your Tier

| Tier | Best For | Key Features |
|------|----------|--------------|
| **Simple** | Small teams, PoC, learning | Lint, Unit Tests, Staging/Prod Deploy |
| **Medium** | Regulated industries, growing teams | + SAST, Dependency Scanning, Container Registry, Compliance |
| **Advanced** | Enterprise, complex deployments | + Rollback, Health Checks, APM, DB Migrations, Feature Flags |

### 2. Copy to Your Project

```bash
# For simple tier
cp examples/simple/.gitlab-ci.yml .gitlab-ci.yml

# For medium tier
cp examples/medium/.gitlab-ci.yml .gitlab-ci.yml

# For advanced tier
cp examples/advanced/.gitlab-ci.yml .gitlab-ci.yml
```

### 3. Configure Required Variables

Add these variables in **GitLab Project Settings → CI/CD → Variables**:

#### Required for All Tiers
| Variable | Description | Example |
|----------|-------------|---------|
| `SLACK_WEBHOOK_URL` | Slack webhook for notifications | `https://hooks.slack.com/services/...` |

#### Required for Medium & Advanced
| Variable | Description | Example |
|----------|-------------|---------|
| `CI_REGISTRY` | Container registry URL | `registry.example.com` |
| `CI_REGISTRY_USER` | Registry username | `gitlab-ci-token` |
| `CI_REGISTRY_PASSWORD` | Registry password/token | `$CI_JOB_TOKEN` |

#### Required for Advanced Only
| Variable | Description | Example |
|----------|-------------|---------|
| `FEATURE_FLAG_SERVICE_URL` | Feature flag service endpoint | `https://flags.example.com` |
| `APM_SERVER_URL` | APM server URL | `https://apm.example.com` |
| `APM_TOKEN` | APM API token | `apm-token-xxx` |

### 4. Customize for Your Project

Edit the copied `.gitlab-ci.yml`:
- Update `image` names to match your tech stack
- Modify `script` commands for your build/test tools
- Adjust `environment` URLs to your domains
- Update `rules`/`only`/`except` for your branch strategy

## Template Features by Tier

### Simple (`examples/simple/.gitlab-ci.yml`)
- ✅ ESLint + Prettier linting
- ✅ Unit tests with coverage reporting
- ✅ Build artifact generation
- ✅ Staging deployment (auto on `develop`)
- ✅ Production deployment (manual on `main`)
- ✅ Slack notifications
- ✅ Caching for faster builds

### Medium (`examples/medium/.gitlab-ci.yml`)
- ✅ All Simple features
- ✅ **SAST** (Static Application Security Testing)
- ✅ **Dependency Scanning** (vulnerable packages)
- ✅ **Container Scanning** (Docker image vulnerabilities)
- ✅ **Container Registry** integration (build + push Docker images)
- ✅ **License Compliance** scanning
- ✅ Integration tests against staging
- ✅ Compliance automation (GDPR, SOC2 ready)

### Advanced (`examples/advanced/.gitlab-ci.yml`)
- ✅ All Medium features
- ✅ **Automated Rollback** on health check failure
- ✅ **Blue-Green Deployment** for zero-downtime
- ✅ **Database Migrations** with backup (staging + prod)
- ✅ **Health Checks** with retry logic
- ✅ **Performance Testing** (k6)
- ✅ **Feature Flags** management
- ✅ **APM Integration** (deployment tracking)
- ✅ **Multi-arch Docker builds** (amd64/arm64)
- ✅ Comprehensive monitoring setup

## Required Scripts

Add these scripts to your `package.json`:

```json
{
  "scripts": {
    "lint": "eslint . --ext .js,.ts",
    "test:unit": "jest --coverage",
    "test:integration": "jest --config jest.integration.config.js",
    "build": "npm run build:prod",
    "build:prod": "webpack --mode production"
  }
}
```

## Additional Files Needed

Create these files in your project root:

### `scripts/compliance-check.js` (Medium/Advanced)
```javascript
// Check licenses, security policies, access logs
const fs = require('fs');
const report = JSON.parse(fs.readFileSync('license-report.json'));
// Add your compliance logic
console.log('Compliance check completed');
```

### `scripts/gdpr-check.js` (Advanced)
```javascript
// GDPR-specific compliance checks
console.log('GDPR check completed');
```

### `scripts/soc2-check.js` (Advanced)
```javascript
// SOC2-specific compliance checks
console.log('SOC2 check completed');
```

### `scripts/setup-feature-flags.js` (Advanced)
```javascript
// Setup feature flags for release
const commitSha = process.argv.find(a => a.startsWith('--commit-sha=')).split('=')[1];
console.log(`Setting up feature flags for ${commitSha}`);
```

### `scripts/setup-monitoring.js` (Advanced)
```javascript
// Setup monitoring alerts
const commitSha = process.argv.find(a => a.startsWith('--commit-sha=')).split('=')[1];
console.log(`Setting up monitoring for ${commitSha}`);
```

### `scripts/performance-test.js` (Advanced)
```javascript
// k6 performance test script
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 100 },
    { duration: '5m', target: 100 },
    { duration: '2m', target: 0 },
  ],
};

export default function () {
  const res = http.get(`${__ENV.BASE_URL}/api/health`);
  check(res, { 'status is 200': (r) => r.status === 200 });
  sleep(1);
}
```

## Migration Path

```
Simple → Medium → Advanced
   │         │          │
   │         │          └─ Add: rollback, health checks, APM, DB migrations, feature flags
   │         └─ Add: security scanning, container registry, compliance
   └─ Start here
```

## Validation

Test your configuration locally:

```bash
# Install GitLab CI Lint (requires Docker)
docker run --rm -v $(pwd):/app gitlab/gitlab-ce:latest gitlab-ci-lint /app/.gitlab-ci.yml

# Or use online validator at https://gitlab.com/ci/lint
```

## Support

- [GitLab CI/CD Documentation](https://docs.gitlab.com/ee/ci/)
- [Pipeline Architecture](https://docs.gitlab.com/ee/ci/pipelines/pipeline_architectures.html)
- [Security Scanning](https://docs.gitlab.com/ee/user/application_security/)