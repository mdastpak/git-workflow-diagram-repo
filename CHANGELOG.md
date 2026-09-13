# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- GitLab CI/CD templates for all three workflow tiers (Simple, Medium, Advanced)
- GitHub Actions workflow for Mermaid diagram validation and markdown linting
- Pre-commit hooks configuration with markdownlint, mermaid validation, spell checking
- Commit message validation with Commitizen (conventional commits)
- Examples directory with comprehensive documentation
- Markdown link checker configuration

### Changed
- Updated README with comparison matrix and fixed dead wiki link
- Enhanced workflow files with horizontal diagram variants

## [1.0.0] - 2025-01-15

### Added
- Initial release with three GitLab workflow diagram versions
- Simple workflow: Basic CI/CD with lint, unit tests, staging/production deployment
- Medium workflow: Enhanced security with SAST, dependency scanning, container registry, compliance
- Advanced workflow: Enterprise features with rollback, health checks, APM, DB migrations, feature flags
- Original/base workflow diagram
- BEST_PRACTICES.md with security, performance, cost, and compliance guidelines
- CONTRIBUTING.md with contribution guidelines
- MIT License
- GitHub badges in README

### Features by Tier

#### Simple (v1.0.0)
- Basic Git Flow (main/develop/feature/bugfix/release/hotfix)
- Code review with peer review requirement
- CI Pipeline: Lint + Unit Tests
- Environment progression: Staging → Production
- Stakeholder notifications on deployment
- Automated documentation updates

#### Medium (v1.0.0)
- All Simple features
- SAST (Static Application Security Testing)
- Dependency vulnerability scanning
- Container registry integration (Docker build/push)
- Compliance automation (license + standards)
- Full security suite for production releases
- Integration tests (E2E + Performance)

#### Advanced (v1.0.0)
- All Medium features
- Automated rollback procedures for failed deployments
- Health checks and monitoring integration
- Performance monitoring (APM + metrics collection)
- Database migration management
- Feature flag management for controlled releases
- Comprehensive alerting and stakeholder communication
- Blue-green deployment support
- Multi-arch Docker builds

### Documentation
- Step-by-step workflow explanations for each version
- Implementation checklists and prerequisites
- Recommended next steps for progression
- Security hardening checklists
- Performance optimization tips
- Cost optimization strategies
- Compliance requirements (GDPR, HIPAA, SOC2, PCI DSS)
- Implementation examples (YAML templates)

---

## Release Process

### Version Numbering
- **Major** (X.0.0): Breaking changes to diagram structure or workflow philosophy
- **Minor** (X.Y.0): New workflow tier, significant feature additions
- **Patch** (X.Y.Z): Bug fixes, documentation updates, minor enhancements

### Release Checklist
- [ ] Update version in package.json (if applicable)
- [ ] Update CHANGELOG.md with release notes
- [ ] Create git tag: `git tag -a vX.Y.Z -m "Release vX.Y.Z"`
- [ ] Push tag: `git push origin vX.Y.Z`
- [ ] Create GitHub Release with changelog
- [ ] Update documentation if needed

---

## Migration Guides

### Upgrading from Simple to Medium
See [MIGRATION_SIMPLE_TO_MEDIUM.md](docs/migrations/MIGRATION_SIMPLE_TO_MEDIUM.md)

### Upgrading from Medium to Advanced
See [MIGRATION_MEDIUM_TO_ADVANCED.md](docs/migrations/MIGRATION_MEDIUM_TO_ADVANCED.md)

---

## Support Policy

| Version | Status | Support Until |
|---------|--------|---------------|
| 1.x     | Active | Ongoing       |

For questions about upgrading, please open a [GitHub Discussion](https://github.com/mdastpak/git-workflow-diagram-repo/discussions).