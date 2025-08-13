# Requirements Processing System

A production-grade, end-to-end system that automatically processes GitHub issues into structured requirements using Google's Gemini CLI, with MCP integration for enrichment and downstream pipeline generation.

## 🎯 System Overview

This system transforms GitHub issues into:
- **Atomic, traceable requirements** in individual markdown files
- **Organized folder structures** for easy navigation
- **Enriched metadata** via MCP services
- **Downstream artifacts** (design, architecture, tests, traceability)

## 🏗️ Architecture

```
GitHub Issue → Requirements Processor → Gemini CLI → File Generation → MCP Enrichment → Downstream Pipeline
     ↓              ↓                    ↓            ↓              ↓              ↓
Issue Events   AI Analysis         Requirements   Enrichment    Design/Test    Traceability
     ↓              ↓                    ↓            ↓              ↓              ↓
Attachments   Decomposition      Markdown Files   Domain/Reg    Architecture   Matrices
     ↓              ↓                    ↓            ↓              ↓              ↓
Screenshots   Categorization     Config Update    Compliance    Code Stubs     Reporting
```

## 🚀 Quick Start

### 1. **Automatic Processing**
- Create a GitHub issue with requirements
- System automatically processes it
- Requirements generated in `docs/requirements/ISSUE-<number>-<slug>/`

### 2. **Manual Processing**
- Go to **Actions** → **Requirements Processor**
- Enter issue number
- Click **Run workflow**

### 3. **Enrichment**
- Go to **Actions** → **MCP Requirements Enrichment**
- Select enrichment type
- Run to enhance requirements

## 📁 File Structure

```
docs/
├── requirements/
│   ├── ISSUE-123-user-authentication/
│   │   ├── README.md                    # Issue overview
│   │   ├── requirements-analysis.json   # Raw analysis
│   │   ├── functional/
│   │   │   ├── RQ-ISSUE-123-001.md     # Individual requirements
│   │   │   └── RQ-ISSUE-123-002.md
│   │   ├── non-functional/
│   │   │   └── RQ-ISSUE-123-003.md
│   │   ├── constraints/
│   │   │   └── RQ-ISSUE-123-004.md
│   │   └── enrichment/
│   │       └── enrichment-data.json    # MCP enrichment data
│   └── ISSUE-124-payment-integration/
├── design-synthesis/                    # Generated design documents
├── architecture-stubs/                  # Code stubs and configs
├── test-scaffolds/                     # Testing frameworks
├── traceability-matrices/              # Compliance matrices
└── config.json                         # System configuration
```

## 🔧 Configuration

### **Repository Variables**
Set these in **Settings > Secrets and variables > Actions > Variables**:

```bash
GOOGLE_CLOUD_PROJECT=your-project-id
GOOGLE_CLOUD_LOCATION=us-central1
GCP_WIF_PROVIDER=projects/YOUR_PROJECT_ID/locations/global/workloadIdentityPools/github-actions-pool/providers/github-provider
SERVICE_ACCOUNT_EMAIL=github-actions@YOUR_PROJECT_ID.iam.gserviceaccount.com
```

### **Gemini Settings**
Configure `.gemini/settings.json` for:
- MCP server endpoints
- Enrichment options
- Output formats
- Processing preferences

## 📋 Workflows

### 1. **Requirements Processor** (`requirements-processor.yml`)
- **Trigger**: Issue events (opened, edited, reopened)
- **Function**: Converts issues to structured requirements
- **Output**: Markdown files, folder structure, config updates

### 2. **MCP Enrichment** (`mcp-enrichment.yml`)
- **Trigger**: Manual workflow dispatch
- **Function**: Enhances requirements with external data
- **Types**: Domain ontology, regulatory, traceability, impact analysis

### 3. **Downstream Pipeline** (`downstream-pipeline.yml`)
- **Trigger**: Requirements updates, manual dispatch
- **Function**: Generates design, architecture, tests, traceability
- **Output**: Comprehensive development artifacts

## 🔍 MCP Integration

### **Available Services**
- **Domain Ontology**: Business domain mapping and vocabulary
- **Regulatory Compliance**: GDPR, SOX, ISO27001 compliance
- **Traceability Engine**: Requirements tracking and cross-references
- **Impact Analyzer**: Business and technical impact assessment

### **Configuration**
```json
{
  "mcp_servers": {
    "domain_ontology": {
      "endpoint": "https://your-domain-service.com/api",
      "enabled": true
    }
  }
}
```

## 📊 Output Formats

### **Requirement Files**
Each requirement includes:
- **Metadata**: Type, priority, complexity
- **Description**: Detailed requirement text
- **Acceptance Criteria**: Testable criteria
- **Dependencies**: Related requirements
- **Stakeholders**: Responsible parties
- **Tags**: Categorization labels

### **Enrichment Data**
- **Domain Ontology**: Business context and vocabulary
- **Regulatory Analysis**: Compliance requirements
- **Traceability**: Upstream/downstream links
- **Impact Analysis**: Business and technical impact

## 🚀 Usage Examples

### **Create Requirements**
1. **Open GitHub issue** with requirements description
2. **Add attachments** (screenshots, PDFs, specs)
3. **System automatically processes** the issue
4. **Requirements generated** in organized folders

### **Enrich Requirements**
1. **Go to Actions** → **MCP Requirements Enrichment**
2. **Select enrichment type** (all, domain, regulatory, etc.)
3. **Enter issue number**
4. **Run workflow** to enhance requirements

### **Generate Downstream Artifacts**
1. **Go to Actions** → **Downstream Pipeline**
2. **Select artifact type** (design, tests, etc.)
3. **Run workflow** to generate artifacts

## 🔒 Security Features

- **Workload Identity Federation**: No API keys in secrets
- **Repository isolation**: Access limited to specific repos
- **Audit logging**: Comprehensive access tracking
- **Permission scoping**: Minimal required permissions

## 📈 Monitoring and Observability

### **Built-in Logging**
- **Workflow execution logs**: Available in GitHub Actions
- **Processing metrics**: Requirements count, processing time
- **Error tracking**: Detailed error messages and debugging

### **Configuration Tracking**
- **docs/config.json**: Centralized metadata and tracking
- **Execution history**: Pipeline run counts and timestamps
- **Artifact status**: Generated file tracking

## 🧪 Testing

### **Local Testing**
```bash
# Test workflows locally
act -j process-requirements
act -j enrich-requirements
act -j downstream-pipeline
```

### **Integration Testing**
1. **Create test issue** with sample requirements
2. **Run processing workflow** manually
3. **Verify output** files and structure
4. **Test enrichment** with different types
5. **Validate downstream** pipeline generation

## 🚨 Troubleshooting

### **Common Issues**

#### **Workflow Not Triggering**
- Check issue event types in workflow
- Verify repository permissions
- Check workflow file location

#### **Requirements Not Generated**
- Verify Gemini CLI authentication
- Check Google Cloud configuration
- Review workflow logs for errors

#### **Enrichment Failing**
- Validate MCP server configuration
- Check service endpoints and authentication
- Verify enrichment type selection

### **Debug Mode**
```yaml
# Enable debug logging
env:
  ACTIONS_STEP_DEBUG: true
```

## 🔄 Customization

### **Requirement Templates**
Modify `docs/config.json` to customize:
- **Section structure**: Add/remove requirement sections
- **Metadata fields**: Customize requirement metadata
- **Output formats**: Configure markdown templates

### **MCP Services**
Configure `.gemini/settings.json` for:
- **Custom endpoints**: Your MCP service URLs
- **Authentication**: API keys, OAuth, etc.
- **Processing options**: Timeout, retry logic

### **Workflow Triggers**
Customize workflow triggers:
- **Issue events**: Specific issue types
- **File paths**: Monitor specific directories
- **Manual inputs**: Custom workflow parameters

## 📞 Support

### **Documentation**
- **This README**: System overview and usage
- **Workflow files**: Detailed workflow configuration
- **Configuration files**: Settings and options

### **Issues and Questions**
- **GitHub Issues**: Report bugs and request features
- **Workflow logs**: Debug execution problems
- **Configuration**: Validate settings and setup

## 🎉 Benefits

### **Automation**
- **Zero manual work**: Automatic issue processing
- **Consistent output**: Standardized requirement format
- **Scalable processing**: Handle multiple issues simultaneously

### **Quality**
- **AI-powered analysis**: Gemini CLI requirement decomposition
- **Structured output**: Organized, navigable requirements
- **Traceability**: Complete requirement tracking

### **Integration**
- **MCP services**: External data enrichment
- **Downstream pipelines**: Design, test, architecture generation
- **GitHub native**: Seamless repository integration

---

**Ready to automate your requirements processing?** 

1. **Deploy the workflows** to your repository
2. **Configure Google Cloud** Workload Identity Federation
3. **Create your first issue** and watch the magic happen! 🚀

*This system transforms GitHub issues into production-ready requirements in minutes, not hours.* 