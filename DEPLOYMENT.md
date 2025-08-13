# Multi-Organization Deployment Guide

This guide provides step-by-step instructions for deploying workflows that use the official [Google Gemini CLI action](https://github.com/marketplace/actions/run-gemini-cli) across multiple GitHub organizations in a production environment.

## 🏗️ Architecture Overview

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Primary Org   │    │   Secondary     │    │   Secondary     │
│                 │    │     Org 1       │    │     Org 2       │
│ ┌─────────────┐ │    │                 │    │                 │
│ │Workflow     │ │    │ ┌─────────────┐ │    │ ┌─────────────┐ │
│ │Repository   │ │    │ │Workflow     │ │    │ │Workflow     │ │
│ │             │ │    │ │Repository   │ │    │ │Repository   │ │
│ └─────────────┘ │    │ └─────────────┘ │    │ └─────────────┘ │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────┴─────────────┐
                    │    Google Cloud          │
                    │    Project               │
                    │                          │
                    │ ┌─────────────────────┐  │
                    │ │Gemini API           │  │
                    │ │Workload Identity    │  │
                    │ │Service Accounts     │  │
                    │ └─────────────────────┘  │
                    └──────────────────────────┘
```

## 📋 Prerequisites

### 1. GitHub Organizations
- **Primary Organization**: Where the workflow repository will be hosted
- **Secondary Organizations**: Where the workflows will be consumed
- **Admin Access**: Required in all organizations for setup

### 2. Google Cloud Project
- **Project ID**: Centralized project for Gemini API access
- **Billing**: Enabled for Gemini API usage
- **APIs**: Gemini API and Workload Identity Federation enabled

### 3. Infrastructure Requirements
- **GitHub Enterprise**: If using GitHub Enterprise Server
- **Network Access**: Proper firewall rules for GitHub Actions
- **Monitoring**: Logging and alerting infrastructure

## 🔧 Step-by-Step Deployment

### Phase 1: Primary Organization Setup

#### 1.1 Create Workflow Repository
```bash
# In your primary organization
git clone https://github.com/your-org/gemini-cli-workflows
cd gemini-cli-workflows

# Customize workflows and documentation
# Update organization names and branding
```

#### 1.2 Configure Google Cloud
```bash
# Enable required APIs
gcloud services enable aiplatform.googleapis.com
gcloud services enable iamcredentials.googleapis.com

# Create service account for GitHub Actions
gcloud iam service-accounts create github-actions \
  --display-name="GitHub Actions Service Account"

# Grant necessary permissions
gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
  --member="serviceAccount:github-actions@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/aiplatform.user"
```

#### 1.3 Set Up Workload Identity Federation
```bash
# Create Workload Identity Pool
gcloud iam workload-identity-pools create "github-actions-pool" \
  --project="YOUR_PROJECT_ID" \
  --location="global" \
  --display-name="GitHub Actions Pool"

# Create Workload Identity Provider
gcloud iam workload-identity-pools providers create-oidc "github-provider" \
  --project="YOUR_PROJECT_ID" \
  --location="global" \
  --workload-identity-pool="github-actions-pool" \
  --issuer-uri="https://token.actions.githubusercontent.com" \
  --attribute-mapping="google.subject=assertion.sub,attribute.actor=assertion.actor,attribute.repository=assertion.repository"

# Allow authentication from GitHub
gcloud iam service-accounts add-iam-policy-binding \
  "github-actions@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
  --project="YOUR_PROJECT_ID" \
  --role="roles/iam.workloadIdentityUser" \
  --member="principalSet://iam.googleapis.com/projects/YOUR_PROJECT_ID/locations/global/workloadIdentityPools/github-actions-pool/attribute.repository/YOUR_ORG/gemini-cli-workflows"
```

#### 1.4 Configure Repository Variables
```bash
# Add to repository variables (Settings > Secrets and variables > Actions > Variables)
GOOGLE_CLOUD_PROJECT=your-project-id
GOOGLE_CLOUD_LOCATION=us-central1
GCP_WIF_PROVIDER=projects/YOUR_PROJECT_ID/locations/global/workloadIdentityPools/github-actions-pool/providers/github-provider
SERVICE_ACCOUNT_EMAIL=github-actions@YOUR_PROJECT_ID.iam.gserviceaccount.com
```

#### 1.5 Test the Workflows
```bash
# Create a test workflow
# Verify authentication and functionality
# Check logs and outputs
```

### Phase 2: Secondary Organization Setup

#### 2.1 Create Organization-Level Variables
```bash
# In each secondary organization
# Go to Organization Settings > Secrets and variables > Actions
# Add organization-level variables:

GOOGLE_CLOUD_PROJECT=your_project_id
GOOGLE_CLOUD_LOCATION=us-central1
GCP_WIF_PROVIDER=your_wif_provider
SERVICE_ACCOUNT_EMAIL=your_service_account
```

#### 2.2 Configure Organization Permissions
```bash
# Set organization-level permissions for GitHub Actions
# Ensure repositories can access organization variables
# Configure branch protection policies
```

#### 2.3 Deploy Workflows
```bash
# Copy workflow files to each repository
# Update action references to use official action: google-github-actions/run-gemini-cli@v0.1.10
# Test functionality in each repository
```

### Phase 3: Cross-Organization Authentication

#### 3.1 Update Workload Identity Federation
```bash
# Add each organization to the allowlist
gcloud iam service-accounts add-iam-policy-binding \
  "github-actions@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
  --project="YOUR_PROJECT_ID" \
  --role="roles/iam.workloadIdentityUser" \
  --member="principalSet://iam.googleapis.com/projects/YOUR_PROJECT_ID/locations/global/workloadIdentityPools/github-actions-pool/attribute.repository/ORG_NAME/REPO_NAME"
```

#### 3.2 Test Cross-Organization Access
```bash
# Verify authentication works across organizations
# Test API access and functionality
# Monitor logs for any issues
```

## 🔒 Security Configuration

### 1. Access Control
```yaml
# Repository-level permissions
permissions:
  contents: read
  issues: write
  pull-requests: write
  id-token: write  # Required for Workload Identity
```

### 2. Variable Management
```bash
# Use organization-level variables for cross-repo access
# Implement variable rotation policies
# Monitor variable usage and access
```

### 3. Network Security
```yaml
# Restrict network access if needed
# Use private runners for sensitive repositories
# Implement proper firewall rules
```

## 📊 Monitoring and Observability

### 1. Centralized Logging
```yaml
# Configure logging to central location
# Use structured logging for analysis
# Implement log retention policies
```

### 2. Metrics Collection
```yaml
# Track usage across organizations
# Monitor API costs and quotas
# Alert on unusual activity
```

### 3. Health Checks
```yaml
# Implement health check workflows
# Monitor action execution success rates
# Track response times and performance
```

## 🚀 Scaling Considerations

### 1. Rate Limiting
```yaml
# Implement per-organization rate limits
# Use GitHub's built-in rate limiting
# Monitor and adjust limits as needed
```

### 2. Resource Management
```yaml
# Use appropriate runner types
# Implement caching strategies
# Optimize workflow execution
```

### 3. Cost Optimization
```yaml
# Monitor Gemini API usage
# Use appropriate model tiers
# Implement usage quotas
```

## 🔄 Update and Maintenance

### 1. Version Management
```bash
# Use semantic versioning for workflow updates
# Tag releases appropriately
# Maintain changelog
```

### 2. Rollout Strategy
```bash
# Test in staging environment
# Roll out gradually across organizations
# Monitor for issues during rollout
```

### 3. Rollback Procedures
```bash
# Maintain previous versions
# Document rollback steps
# Test rollback procedures
```

## 🧪 Testing Strategy

### 1. Local Testing
```bash
# Use act for local workflow testing
# Test with various input combinations
# Validate error handling
```

### 2. Integration Testing
```bash
# Test in isolated environments
# Validate cross-organization functionality
# Test authentication flows
```

### 3. Production Testing
```bash
# Test in production-like environments
# Validate performance under load
# Monitor for issues
```

## 📋 Deployment Checklist

### Pre-Deployment
- [ ] Google Cloud project configured
- [ ] Workload Identity Federation set up
- [ ] Service accounts created and configured
- [ ] Primary workflow repository tested
- [ ] Security review completed

### Organization Setup
- [ ] Organization variables configured
- [ ] Permissions set correctly
- [ ] Workflows deployed
- [ ] Authentication tested
- [ ] Functionality verified

### Post-Deployment
- [ ] Monitoring configured
- [ ] Alerts set up
- [ ] Documentation updated
- [ ] Team training completed
- [ ] Support procedures established

## 🚨 Troubleshooting

### Common Issues

#### 1. Authentication Errors
```bash
# Check Workload Identity Federation setup
# Verify service account permissions
# Check repository allowlist
```

#### 2. Permission Errors
```bash
# Verify workflow permissions
# Check organization variable access
# Validate repository settings
```

#### 3. API Rate Limiting
```bash
# Implement exponential backoff
# Use appropriate quotas
# Monitor usage patterns
```

### Debug Mode
```yaml
# Enable debug logging
env:
  ACTIONS_STEP_DEBUG: true
```

## 📞 Support and Maintenance

### 1. Documentation
- **User guides**: For each organization
- **Troubleshooting**: Common issues and solutions
- **API reference**: Action inputs and outputs

### 2. Training
- **Admin training**: For organization administrators
- **User training**: For developers and teams
- **Best practices**: Security and usage guidelines

### 3. Support Channels
- **Official Action**: [Google Gemini CLI Action](https://github.com/marketplace/actions/run-gemini-cli)
- **Documentation**: [Official Documentation](https://github.com/google-github-actions/run-gemini-cli)
- **Issues**: [GitHub Issues](https://github.com/your-org/your-repo/issues)

---

**Note**: This deployment guide is for workflows that use the official Google Gemini CLI action. Always test thoroughly in staging environments before deploying to production. 