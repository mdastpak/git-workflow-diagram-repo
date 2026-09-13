# Migration Guide: Simple → Medium Workflow

This guide helps teams upgrade from the Simple workflow to the Medium workflow, adding security scanning, container registry, and compliance automation.

## Overview of Changes

| Aspect | Simple | Medium |
|--------|--------|--------|
| Security Scanning | ❌ | ✅ SAST + Dependency |
| Container Registry | ❌ | ✅ Docker build/push |
| Compliance | ❌ | ✅ License + Standards |
| Integration Tests | Basic | ✅ E2E + Performance |
| Pipeline Stages | 5 | 9 |

## Step-by-Step Migration

### 1. Prerequisites

Before migrating, ensure you have:

- [ ] GitLab Premium/Ultimate (required for security scanning features)
- [ ] Container Registry enabled in GitLab project settings
- [ ] Security scanning tools configured (SAST, Dependency Scanning)
- [ ] Compliance frameworks identified (GDPR, HIPAA, SOC2, PCI-DSS)
- [ ] Team trained on security best practices

### 2. Enable GitLab Security Features

In your GitLab project:

1. **Navigate to Security & Compliance → Configuration**
2. **Enable SAST**: Auto-detects language, or configure `.gitlab-sast.yml`
3. **Enable Dependency Scanning**: Auto-detects package managers
4. **Enable Container Scanning**: Requires Container Registry
5. **Enable License Compliance**: Configure license policies

### 3. Update `.gitlab-ci.yml`

Replace your simple pipeline with the medium template:

```bash
cp examples/medium/.gitlab-ci.yml .gitlab-ci.yml
```

Key additions in medium pipeline:

```yaml
# New stages added
stages:
  - security-sast      # NEW: SAST scanning
  - security-dependency # NEW: Dependency scanning
  - security-container  # NEW: Container scanning
  - compliance          # NEW: License + standards check
  - container-build     # NEW: Docker build/push
  - integration-test    # NEW: E2E tests against staging

# New jobs
sast:
  stage: security-sast
  # ... SAST configuration

dependency-scanning:
  stage: security-dependency
  # ... Dependency scanning

container-build:
  stage: container-build
  # ... Docker build & push to registry
```

### 4. Configure Container Registry

1. **Enable in GitLab**: Settings → Packages & Registries → Container Registry
2. **Add registry variables** (Settings → CI/CD → Variables):
   - `CI_REGISTRY`: Your registry URL (e.g., `registry.example.com`)
   - `CI_REGISTRY_USER`: `gitlab-ci-token`
   - `CI_REGISTRY_PASSWORD`: `$CI_JOB_TOKEN`
3. **Update deploy scripts** to use registry images:
   ```bash
   IMAGE_TAG="${CI_REGISTRY_IMAGE}:${CI_COMMIT_SHA}" deploy.sh staging
   ```

### 5. Add Compliance Checks

Create compliance scripts referenced in the pipeline:

```bash
# scripts/compliance-check.js
const fs = require('fs');
const report = JSON.parse(fs.readFileSync('license-report.json'));

// Check for prohibited licenses
const prohibited = ['GPL-3.0', 'AGPL-3.0'];
const violations = [];

for (const pkg of Object.values(report)) {
  if (prohibited.includes(pkg.licenses)) {
    violations.push(`${pkg.name}: ${pkg.licenses}`);
  }
}

if (violations.length > 0) {
  console.error('License violations found:', violations);
  process.exit(1);
}
console.log('Compliance check passed');
```

### 6. Update Deployment Scripts

Modify your deploy scripts to:
- Pull images from Container Registry
- Run integration tests against staging
- Include compliance verification gates

### 7. Test the Migration

1. **Create a feature branch**: `git checkout -b migrate-to-medium`
2. **Apply changes** and push
3. **Verify pipeline runs** all new stages
4. **Check security reports** in GitLab: Security & Compliance → Vulnerability Report
5. **Verify container images** in Packages & Registries → Container Registry
6. **Merge to develop** after validation

### 8. Rollback Plan

If issues arise:
```bash
git checkout main
git revert <merge-commit>
```
Or use GitLab's pipeline rollback feature.

## New Files to Add

```
your-project/
├── .gitlab-ci.yml           # Updated from examples/medium/
├── scripts/
│   ├── compliance-check.js  # License compliance
│   ├── gdpr-check.js        # GDPR compliance (optional)
│   └── soc2-check.js        # SOC2 compliance (optional)
├── Dockerfile               # For container builds
└── .dockerignore
```

## Timeline Estimate

| Task | Effort |
|------|--------|
| Enable GitLab security features | 1-2 hours |
| Update CI/CD configuration | 2-4 hours |
| Configure container registry | 1-2 hours |
| Add compliance scripts | 2-4 hours |
| Test and validate | 4-8 hours |
| **Total** | **1-2 days** |

## Next Steps After Migration

1. **Monitor security reports** weekly
2. **Tune SAST rules** to reduce false positives
3. **Set up compliance dashboards**
4. **Plan upgrade to Advanced** when ready for:
   - Automated rollback
   - Health checks & APM
   - Database migrations
   - Feature flags

## Resources

- [GitLab SAST Documentation](https://docs.gitlab.com/ee/user/application_security/sast/)
- [Dependency Scanning](https://docs.gitlab.com/ee/user/application_security/dependency_scanning/)
- [Container Scanning](https://docs.gitlab.com/ee/user/application_security/container_scanning/)
- [License Compliance](https://docs.gitlab.com/ee/user/application_security/license_compliance/)
- [Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/)