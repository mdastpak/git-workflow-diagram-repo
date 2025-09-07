# GitLab Workflow Diagram Repository

[![GitHub stars](https://img.shields.io/github/stars/mdastpak/git-workflow-diagram-repo)](https://github.com/mdastpak/git-workflow-diagram-repo/stargazers)
[![GitHub issues](https://img.shields.io/github/issues/mdastpak/git-workflow-diagram-repo)](https://github.com/mdastpak/git-workflow-diagram-repo/issues)
[![GitHub license](https://img.shields.io/github/license/mdastpak/git-workflow-diagram-repo)](https://github.com/mdastpak/git-workflow-diagram-repo/blob/main/LICENSE)
[![GitHub last commit](https://img.shields.io/github/last-commit/mdastpak/git-workflow-diagram-repo)](https://github.com/mdastpak/git-workflow-diagram-repo/commits/main)
[![GitHub repo size](https://img.shields.io/github/repo-size/mdastpak/git-workflow-diagram-repo)](https://github.com/mdastpak/git-workflow-diagram-repo)

This repository contains multiple versions of GitLab workflow diagrams, each with increasing levels of complexity and features. Choose the version that best fits your team's needs.

## Available Versions

### 1. [Simple Version](gitlab-workflow-simple.md)
- **Target Audience**: Teams starting with basic CI/CD
- **Key Features**:
  - Basic Git Flow with main/develop/feature branches
  - Code review and basic CI pipeline (lint + unit tests)
  - Environment progression (staging → production)
  - Stakeholder notifications on deployment
  - Automated documentation updates
- **Use Case**: Simple projects with basic deployment needs
- **Complexity**: Low - suitable for small teams or proof-of-concept projects

### 2. [Medium Version](gitlab-workflow-medium.md)
- **Target Audience**: Teams with security requirements
- **Key Features**:
  - All simple version features
  - Enhanced security scanning (SAST, dependency checks)
  - Compliance automation and license validation
  - Container registry integration (Docker image building/storage)
  - Full security suite for production releases
- **Use Case**: Projects requiring robust security and container management
- **Complexity**: Medium - ideal for regulated industries or growing teams

### 3. [Advanced Version](gitlab-workflow-advanced.md)
- **Target Audience**: Enterprise teams with complex deployments
- **Key Features**:
  - All medium version features
  - Automated rollback procedures for failed deployments
  - Health checks and monitoring integration
  - Performance monitoring (APM + metrics collection)
  - Database migration management
  - Feature flag management for controlled releases
  - Comprehensive alerting and stakeholder communication
- **Use Case**: Large-scale applications with advanced DevOps practices
- **Complexity**: High - designed for enterprise environments with complex requirements

### Original/Base Version
- The original diagram is available in [`gitlab-workflow-original.md`](gitlab-workflow-original.md)
- This serves as the foundation for all enhanced versions

## How to Choose a Version

- **Start Simple**: Begin with the simple version and add features as your team grows
- **Security First**: Use medium version if compliance and security are priorities
- **Full Automation**: Adopt advanced version for complete CI/CD automation and monitoring

Each version includes Mermaid diagrams that can be viewed directly in GitLab or rendered in documentation tools.

## 📖 **Quick Start**

1. Choose your complexity level based on your team's needs
2. Open the corresponding `.md` file
3. View the Mermaid diagram in GitLab or any Markdown renderer
4. Follow the step-by-step explanations for implementation

## 🤝 **Contributing**

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

## 📄 **License**

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📞 **Support**

- 📧 **Issues**: [GitHub Issues](https://github.com/mdastpak/git-workflow-diagram-repo/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/mdastpak/git-workflow-diagram-repo/discussions)
- 📖 **Documentation**: [Wiki](https://github.com/mdastpak/git-workflow-diagram-repo/wiki)

---

⭐ **Star this repository** if you find it helpful! Your support helps others discover these workflow diagrams.