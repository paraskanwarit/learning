# Gemini CLI GitHub Action Workflows

Production-grade GitHub Action workflows using the official [Google Gemini CLI action](https://github.com/marketplace/actions/run-gemini-cli) with **Workload Identity Federation** and **Vertex AI** integration.

## 🚀 **Repository Configuration**

- **Repository**: [paraskanwarit/learning](https://github.com/paraskanwarit/learning)
- **GCP Project**: `extreme-gecko-466211-t1`
- **Authentication**: Workload Identity Federation (WIF) + Vertex AI
- **Account**: `paraskanwar.it1@gmail.com`

## 🔧 **Key Features**

- **🔐 Secure Authentication**: Uses Google Cloud Workload Identity Federation (no API keys)
- **☁️ Vertex AI Integration**: Routes all Gemini requests through Vertex AI for enterprise-grade AI services
- **🤖 Automated Requirements Processing**: Converts GitHub issues into structured requirements
- **🔍 MCP Enrichment**: Model Context Protocol integration for advanced requirement analysis
- **🚀 Downstream Pipelines**: Auto-generates design, architecture, tests, and traceability matrices
- **📊 Centralized Configuration**: Single `docs/config.json` for system management

## 🏗️ **System Architecture**

```
GitHub Issues → Requirements Processor → MCP Enrichment → Downstream Pipelines
     ↓                    ↓                    ↓                    ↓
Issue Creation    Structured Requirements   Enhanced Data    Development Artifacts
     ↓                    ↓                    ↓                    ↓
Auto-trigger      Gemini CLI (Vertex AI)   External Services   Design/Test/Arch
```

## 📋 **Available Workflows**

### 1. **Requirements Processor** (`.github/workflows/requirements-processor.yml`)
- **Trigger**: Issue creation/editing
- **Function**: Converts issues into atomic, structured requirements
- **Output**: `docs/requirements/ISSUE-<number>-<slug>/` folder with markdown files
- **AI Model**: Gemini 1.5 Pro via Vertex AI

### 2. **MCP Enrichment** (`.github/workflows/mcp-enrichment.yml`)
- **Trigger**: Manual workflow dispatch
- **Function**: Enriches requirements using external MCP servers
- **Types**: Domain ontology, regulatory compliance, traceability, impact analysis
- **AI Model**: Gemini 1.5 Pro via Vertex AI

### 3. **Downstream Pipeline** (`.github/workflows/downstream-pipeline.yml`)
- **Trigger**: Requirements changes or manual dispatch
- **Function**: Generates development artifacts
- **Outputs**: Design synthesis, architecture stubs, test scaffolds, traceability matrices
- **AI Model**: Gemini 1.5 Pro via Vertex AI

## 🚀 **Quick Setup**

### **1. Google Cloud Configuration**

```bash
# Set your project
gcloud config set project extreme-gecko-466211-t1

# Enable required APIs
gcloud services enable aiplatform.googleapis.com
gcloud services enable iamcredentials.googleapis.com
gcloud services enable iam.googleapis.com

# Create service account
gcloud iam service-accounts create github-actions \
  --display-name="GitHub Actions Service Account" \
  --description="Service account for GitHub Actions with Vertex AI access"

# Grant permissions
gcloud projects add-iam-policy-binding extreme-gecko-466211-t1 \
  --member="serviceAccount:github-actions@extreme-gecko-466211-t1.iam.gserviceaccount.com" \
  --role="roles/aiplatform.user"
```

### **2. Workload Identity Federation**

```bash
# Create Workload Identity Pool
gcloud iam workload-identity-pools create "github-actions-pool" \
  --project="extreme-gecko-466211-t1" \
  --location="global" \
  --display-name="GitHub Actions Pool"

# Create Provider
gcloud iam workload-identity-pools providers create-oidc "github-provider" \
  --project="extreme-gecko-466211-t1" \
  --location="global" \
  --workload-identity-pool="github-actions-pool" \
  --issuer-uri="https://token.actions.githubusercontent.com" \
  --attribute-mapping="google.subject=assertion.sub,attribute.actor=assertion.actor,attribute.repository=assertion.repository"

# Allow GitHub access
gcloud iam service-accounts add-iam-policy-binding \
  "github-actions@extreme-gecko-466211-t1.iam.gserviceaccount.com" \
  --project="extreme-gecko-466211-t1" \
  --role="roles/iam.workloadIdentityUser" \
  --member="principalSet://iam.googleapis.com/projects/extreme-gecko-466211-t1/locations/global/workloadIdentityPools/github-actions-pool/attribute.repository/paraskanwarit/learning"
```

### **3. Repository Variables**

Go to [Settings → Secrets and variables → Actions → Variables](https://github.com/paraskanwarit/learning/settings/variables/actions) and add:

| Variable | Value |
|----------|-------|
| `GOOGLE_CLOUD_PROJECT` | `extreme-gecko-466211-t1` |
| `GOOGLE_CLOUD_LOCATION` | `us-central1` |
| `GCP_WIF_PROVIDER` | `projects/extreme-gecko-466211-t1/locations/global/workloadIdentityPools/github-actions-pool/providers/github-provider` |
| `SERVICE_ACCOUNT_EMAIL` | `github-actions@extreme-gecko-466211-t1.iam.gserviceaccount.com` |

## 🎯 **Usage Examples**

### **Automatic Requirements Processing**
1. Create a new GitHub issue
2. Workflow automatically triggers
3. Requirements are processed and structured
4. Files created in `docs/requirements/ISSUE-<number>-<slug>/`

### **Manual Enrichment**
```bash
# Trigger MCP enrichment for issue #123
gh workflow run mcp-enrichment.yml -f issue_number=123 -f enrichment_type=all
```

### **Downstream Pipeline Generation**
```bash
# Generate all artifacts
gh workflow run downstream-pipeline.yml -f trigger_type=all

# Generate specific artifacts
gh workflow run downstream-pipeline.yml -f trigger_type=design_synthesis
```

## 🔍 **Generated File Structure**

```
docs/
├── requirements/
│   └── ISSUE-123-feature-request/
│       ├── README.md
│       ├── requirements-analysis.json
│       ├── functional/
│       │   ├── RQ-ISSUE-123-001.md
│       │   └── RQ-ISSUE-123-002.md
│       ├── non-functional/
│       │   └── RQ-ISSUE-123-003.md
│       └── constraints/
│           └── RQ-ISSUE-123-004.md
├── design-synthesis/
│   └── design-synthesis.md
├── architecture-stubs/
│   └── architecture-stubs.md
├── test-scaffolds/
│   └── test-scaffolds.md
├── traceability-matrices/
│   └── traceability-matrices.md
└── config.json
```

## 🔐 **Security Features**

- **Workload Identity Federation**: No API keys stored in GitHub
- **Least Privilege Access**: Service account with minimal required permissions
- **Repository Scoped**: Actions only work on authorized repositories
- **Audit Logging**: All actions logged in Google Cloud

## 📊 **Monitoring & Observability**

- **Vertex AI Integration**: All AI requests routed through Google Cloud
- **Workflow Logs**: Available in GitHub Actions and Google Cloud
- **Performance Metrics**: Tracked via Vertex AI monitoring
- **Cost Management**: Centralized billing through GCP project

## 🚀 **Production Deployment**

### **Multi-Organization Setup**
1. **Organization Variables**: Set at org level for shared configuration
2. **Repository Access**: Control which repos can use the workflows
3. **Service Account Management**: Centralized IAM management
4. **Cost Allocation**: Track usage by organization/repository

### **Scaling Considerations**
- **Concurrent Workflows**: GitHub Actions limits apply
- **Vertex AI Quotas**: Monitor API usage and costs
- **Storage Growth**: `docs/` folder will grow with usage
- **Backup Strategy**: Consider backing up generated artifacts

## 🔧 **Customization**

### **Gemini Settings** (`.gemini/settings.json`)
- Model selection and parameters
- MCP server configurations
- Output format preferences
- Enrichment options

### **Workflow Parameters**
- Trigger conditions
- AI model settings
- Output formats
- Integration points

## 📚 **Documentation & Support**

- **Official Action**: [google-github-actions/run-gemini-cli](https://github.com/marketplace/actions/run-gemini-cli)
- **Workload Identity**: [Google Cloud Documentation](https://cloud.google.com/iam/docs/workload-identity-federation)
- **Vertex AI**: [Google Cloud AI Platform](https://cloud.google.com/vertex-ai)
- **GitHub Actions**: [GitHub Documentation](https://docs.github.com/en/actions)

## 🤝 **Contributing**

This repository provides production-ready workflow templates. For customizations:

1. Fork the repository
2. Modify workflows as needed
3. Test with your GCP project
4. Share improvements via pull requests

## 📄 **License**

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 **Acknowledgments**

- **Google Gemini Team**: For the official CLI action
- **GitHub Actions**: For the automation platform
- **Google Cloud**: For Workload Identity Federation and Vertex AI

---

**Note**: These workflows use the official `google-github-actions/run-gemini-cli` action with Vertex AI integration via Workload Identity Federation. They are production-ready and designed for enterprise use across multiple GitHub organizations.