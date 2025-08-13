# Quick Start Guide

Get up and running with the official Google Gemini CLI action using Workload Identity Federation in under 15 minutes!

## 🚀 Quick Setup (10 minutes)

### 1. Add the Action to Your Repository

Copy this workflow to `.github/workflows/gemini.yml`:

```yaml
name: 'Gemini AI Assistant'

on:
  issue_comment:
    types: [created, edited]
  pull_request_review_comment:
    types: [created, edited]

permissions:
  contents: read
  issues: write
  pull-requests: write
  id-token: write  # Required for Workload Identity Federation

jobs:
  assist:
    runs-on: ubuntu-latest
    steps:
      - name: 'Checkout code'
        uses: actions/checkout@v4
        
      - name: 'Get AI assistance'
        id: gemini-assist
        uses: google-github-actions/run-gemini-cli@v0.1.10
        with:
          prompt: |
            You are a helpful AI coding assistant. Help the user with their request.
            User request: ${{ github.event.comment.body }}
          gcp_project_id: ${{ vars.GOOGLE_CLOUD_PROJECT }}
          gcp_location: ${{ vars.GOOGLE_CLOUD_LOCATION }}
          gcp_workload_identity_provider: ${{ vars.GCP_WIF_PROVIDER }}
          gcp_service_account: ${{ vars.SERVICE_ACCOUNT_EMAIL }}
          
      - name: 'Respond with AI help'
        uses: actions/github-script@v7
        with:
          script: |
            const summary = '${{ steps.gemini-assist.outputs.summary }}';
            await github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `## 🤖 Gemini AI Response\n\n${summary}`
            });
```

### 2. Set Up Google Cloud

1. **Enable required APIs:**
   ```bash
   gcloud services enable aiplatform.googleapis.com
   gcloud services enable iamcredentials.googleapis.com
   ```

2. **Create service account:**
   ```bash
   gcloud iam service-accounts create github-actions \
     --display-name="GitHub Actions"
   ```

3. **Grant permissions:**
   ```bash
   gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
     --member="serviceAccount:github-actions@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
     --role="roles/aiplatform.user"
   ```

### 3. Configure Workload Identity Federation

1. **Create Workload Identity Pool:**
   ```bash
   gcloud iam workload-identity-pools create "github-actions-pool" \
     --project="YOUR_PROJECT_ID" \
     --location="global" \
     --display-name="GitHub Actions Pool"
   ```

2. **Create Provider:**
   ```bash
   gcloud iam workload-identity-pools providers create-oidc "github-provider" \
     --project="YOUR_PROJECT_ID" \
     --location="global" \
     --workload-identity-pool="github-actions-pool" \
     --issuer-uri="https://token.actions.githubusercontent.com" \
     --attribute-mapping="google.subject=assertion.sub,attribute.actor=assertion.actor,attribute.repository=assertion.repository"
   ```

3. **Allow GitHub access:**
   ```bash
   gcloud iam service-accounts add-iam-policy-binding \
     "github-actions@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
     --project="YOUR_PROJECT_ID" \
     --role="roles/iam.workloadIdentityUser" \
     --member="principalSet://iam.googleapis.com/projects/YOUR_PROJECT_ID/locations/global/workloadIdentityPools/github-actions-pool/attribute.repository/YOUR_ORG/YOUR_REPO"
   ```

### 4. Add Repository Variables

Go to your repository's **Settings > Secrets and variables > Actions > Variables**:

```bash
GOOGLE_CLOUD_PROJECT=your-project-id
GOOGLE_CLOUD_LOCATION=us-central1
GCP_WIF_PROVIDER=projects/YOUR_PROJECT_ID/locations/global/workloadIdentityPools/github-actions-pool/providers/github-provider
SERVICE_ACCOUNT_EMAIL=github-actions@YOUR_PROJECT_ID.iam.gserviceaccount.com
```

### 5. Test It!

1. Go to any issue or pull request in your repository
2. Comment: `@gemini-cli help me understand this code`
3. Wait for the AI response! 🤖

## 🔧 Advanced Setup (5 more minutes)

### Customize Prompts

Update the prompt in your workflow:

```yaml
prompt: |
  You are a senior software engineer. Review this code and provide:
  1. Code quality assessment
  2. Security considerations
  3. Performance improvements
  4. Best practices suggestions
  
  Code: ${{ github.event.comment.body }}
```

### Add More Workflows

Copy additional workflows from this repository:
- **PR Review**: `gemini-pr-review.yml`
- **Issue Triage**: `gemini-issue-triage.yml`
- **Testing**: `test-action.yml`

## 📱 Usage Examples

### Get Code Help
```
@gemini-cli help me debug this error
@gemini-cli explain this function
@gemini-cli suggest improvements for this code
```

### Code Review
```
@gemini-cli review this pull request
@gemini-cli check for security issues
@gemini-cli suggest tests for this code
```

### Documentation
```
@gemini-cli write documentation for this function
@gemini-cli create a README for this project
@gemini-cli explain this API endpoint
```

## 🎯 Common Use Cases

### 1. **Code Review Assistant**
- Automatically review pull requests
- Identify potential issues
- Suggest improvements

### 2. **Issue Triage**
- Categorize and prioritize issues
- Suggest labels and assignees
- Provide initial guidance

### 3. **Learning Tool**
- Explain complex code
- Provide examples and alternatives
- Answer coding questions

### 4. **Documentation Helper**
- Generate documentation
- Create examples
- Improve existing docs

## 🚨 Troubleshooting

### Action Not Responding?
1. Check if the workflow ran (Actions tab)
2. Verify Workload Identity Federation setup
3. Check the workflow logs for errors
4. Validate Google Cloud permissions

### Permission Errors?
1. Ensure the workflow has `id-token: write` permission
2. Check if repository variables are accessible
3. Verify Google Cloud IAM settings
4. Check repository allowlist in Workload Identity

### Authentication Issues?
1. Verify service account exists and has correct permissions
2. Check Workload Identity Pool and Provider configuration
3. Validate repository mapping in IAM policy
4. Ensure APIs are enabled in Google Cloud

## 🔗 Next Steps

- **Read the full [README](README.md)** for detailed documentation
- **Check [DEPLOYMENT.md](DEPLOYMENT.md)** for production setup
- **Review [SECURITY.md](SECURITY.md)** for security best practices
- **Explore [examples/](examples/)** for more workflow templates

## 💡 Pro Tips

1. **Use specific prompts** for better results
2. **Combine with other actions** for powerful workflows
3. **Monitor usage** to optimize costs
4. **Customize prompts** for your team's needs
5. **Test thoroughly** before production use
6. **Use organization-level variables** for cross-repo access

## 🔒 Security Benefits

- **No API keys** stored in GitHub secrets
- **Short-lived tokens** with automatic rotation
- **Repository isolation** for access control
- **Audit logging** for compliance
- **Enterprise-grade** security

---

**Need help?** Check the [full documentation](README.md) or [open an issue](https://github.com/your-org/your-repo/issues)!

Happy coding with AI! 🚀🤖 