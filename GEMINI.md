# Gemini CLI Configuration

This file provides project-specific context and instructions for the Gemini CLI when working with this repository.

## Project Context

This is a production-grade GitHub Action that integrates Google's Gemini AI into development workflows. The action provides automated code review, issue triaging, and AI assistance capabilities.

## Coding Standards

### General Guidelines
- Follow GitHub Actions best practices
- Use composite actions for maintainability
- Implement proper error handling and logging
- Follow security-first principles

### YAML Standards
- Use consistent indentation (2 spaces)
- Quote string values when appropriate
- Use descriptive names for steps and jobs
- Group related inputs and outputs logically

### Security Requirements
- Never expose sensitive information in logs
- Use least-privilege permissions
- Implement proper authentication methods
- Validate all inputs and outputs

## Architecture Patterns

### Composite Actions
- Use `runs.using: composite` for maintainable actions
- Break complex logic into logical steps
- Use shell scripts for complex operations
- Implement proper error handling

### Workflow Design
- Separate concerns into different workflows
- Use conditional execution for efficiency
- Implement proper permission scoping
- Add monitoring and observability

### Error Handling
- Use proper exit codes
- Implement retry logic where appropriate
- Provide meaningful error messages
- Log errors for debugging

## Testing Guidelines

### Local Testing
- Use `act` for local workflow testing
- Create test events for different scenarios
- Validate outputs and error conditions
- Test with various input combinations

### Integration Testing
- Test in isolated environments
- Validate cross-platform compatibility
- Test permission and authentication flows
- Verify error handling and recovery

## Performance Considerations

### Resource Usage
- Minimize GitHub Actions minutes
- Use appropriate runner types
- Implement caching where possible
- Optimize API calls and external requests

### Scalability
- Design for concurrent execution
- Implement rate limiting where needed
- Use efficient data structures
- Monitor resource consumption

## Security Best Practices

### Authentication
- Prefer Workload Identity Federation over API keys
- Implement proper secret management
- Use least-privilege service accounts
- Rotate credentials regularly

### Input Validation
- Validate all user inputs
- Sanitize data before processing
- Implement proper escaping
- Use allowlists over blocklists

### Output Security
- Never expose sensitive data
- Implement proper logging levels
- Use secure communication channels
- Validate all external responses

## Monitoring and Observability

### Logging
- Use structured logging where possible
- Include relevant context in log messages
- Implement proper log levels
- Add correlation IDs for tracing

### Metrics
- Track execution times
- Monitor success/failure rates
- Track resource usage
- Implement alerting for anomalies

### Error Tracking
- Capture detailed error information
- Include stack traces when available
- Add context for debugging
- Implement proper error reporting

## Deployment Considerations

### Version Management
- Use semantic versioning
- Tag releases appropriately
- Maintain changelog
- Document breaking changes

### Rollback Strategy
- Implement quick rollback procedures
- Test rollback processes
- Maintain previous versions
- Document rollback steps

### Environment Management
- Use environment-specific configurations
- Implement proper secret management
- Test in staging environments
- Validate production deployments

## Compliance and Governance

### Audit Requirements
- Enable comprehensive logging
- Implement access controls
- Track all changes and deployments
- Maintain audit trails

### Policy Enforcement
- Implement coding standards
- Enforce security requirements
- Validate configurations
- Monitor compliance status

### Documentation
- Maintain up-to-date documentation
- Document all configurations
- Provide usage examples
- Include troubleshooting guides

## Support and Maintenance

### Issue Management
- Use proper issue templates
- Categorize issues appropriately
- Prioritize based on impact
- Track resolution times

### Release Management
- Plan releases in advance
- Test thoroughly before release
- Communicate changes clearly
- Monitor post-release metrics

### User Support
- Provide clear documentation
- Create troubleshooting guides
- Respond to issues promptly
- Gather user feedback regularly 