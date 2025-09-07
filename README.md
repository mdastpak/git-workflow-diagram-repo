# GitLab Workflow Diagram

This diagram illustrates the GitLab workflow cycle, including branches, merge requests, and the initial branch for each type of request. It follows the Git Flow strategy.

## Flowchart

```mermaid
graph TD
    A[main<br/>Stable code, ready for production] -->|Clone for hotfix| E[hotfix/<br/>Fix production bugs]
    A -->|Initial branch for develop| B[develop<br/>Integration branch for features]
    B -->|Clone for feature| C[feature/<br/>Develop new features]
    B -->|Clone for bugfix| V[bugfix/<br/>Fix development bugs]
    B -->|Clone for release| D[release/<br/>Prepare for production release]

    C --> F[Develop Feature Code]
    F --> G[Create Merge Request<br/>to develop]
    G --> H[Test & Review<br/>CI/CD Pipeline]
    H --> I[Merge to develop]
    I --> B

    V --> W[Fix Bug<br/>Development bug fix]
    W --> X[Create Merge Request<br/>to develop]
    X --> Y[Test & Review<br/>CI/CD Pipeline]
    Y --> Z[Merge to develop]
    Z --> B

    D --> J[Prepare Release<br/>Final testing]
    J --> K[Create Merge Request<br/>to main]
    K --> L[Test & Review<br/>CI/CD Pipeline]
    L --> M[Merge to main]
    M --> N[Deploy to Production]
    N --> A
    M --> O[Merge back to develop<br/>Optional]
    O --> B

    E --> P[Fix Bug<br/>Quick hotfix]
    P --> Q[Create Merge Request<br/>to main]
    Q --> R[Test & Review<br/>CI/CD Pipeline]
    R --> S[Merge to main]
    S --> T[Merge to develop]
    T --> B
    S --> U[Deploy to Production]
    U --> A

    style A fill:#4CAF50
    style B fill:#2196F3
    style C fill:#FFC107
    style V fill:#FFC107
    style D fill:#FF5722
    style E fill:#E91E63
    style N fill:#8BC34A
    style U fill:#8BC34A
```

## Explanation

- **main**: The primary branch with stable, production-ready code. Initial branch for hotfix requests.
- **develop**: Integration branch for new features. Initial branch for feature, bugfix, and release requests.
- **feature/**: Branches cloned from develop for developing new features.
- **bugfix/**: Branches cloned from develop for fixing bugs discovered during development.
- **release/**: Branches cloned from develop for preparing releases.
- **hotfix/**: Branches cloned from main for fixing critical production bugs that require immediate deployment.

## Difference between Hotfix and Bugfix

- **Hotfix**: Used for urgent, critical bugs in production code (main branch). These are high-priority fixes that need to be deployed immediately to production. After fixing, the hotfix is merged back to both main and develop.
- **Bugfix**: Used for bugs found during development or testing in the develop branch. These are typically lower priority and follow the normal development cycle through develop before reaching production.

The main differences are:
- **Priority**: Hotfixes are for production emergencies, bugfixes are for development issues.
- **Source Branch**: Hotfix from main, bugfix from develop.
- **Urgency**: Hotfixes bypass normal release cycles, bugfixes follow the standard process.

This diagram can be customized based on your project's specific needs.