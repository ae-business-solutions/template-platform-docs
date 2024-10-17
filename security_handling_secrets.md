# Handling Secrets with GitHub and Azure Key Vault

This document outlines our organization's best practices for managing secrets in GitHub and Azure Key Vault.

## Overview

Proper secret management is crucial for maintaining the security of our applications and infrastructure. We use Azure Key Vault to securely store secrets and GitHub Actions for CI/CD, leveraging the Azure Key Vault integration to access secrets during builds and deployments.

## Azure Key Vault

Each Azure subscription comes with a pre-configured Azure Key Vault. This Key Vault should be used to store all secrets related to projects within that subscription.

### Types of Secrets to Store in Key Vault

- API keys
- Connection strings
- Passwords
- Certificates
- Any sensitive configuration data

## Best Practices

1. **Never commit secrets to source control**
   - Avoid storing secrets in your GitHub repository, even in private repos
   - Use environment variables or configuration files for local development, and ensure these are added to `.gitignore`

2. **Rotate secrets regularly**
   - Implement a process for regular secret rotation
   - Use Azure Key Vault's built-in rotation policies where possible

3. **Use managed identities**
   - When possible, use Azure managed identities to access Key Vault from your applications

## GitHub Integration

To securely use Azure Key Vault secrets in your GitHub Actions workflows:

1. **Confirm Azure Credentials are Setup**
   - Your repository should have already been setup with access to your Azure subscription(s)
   - Go to your GitHub repository settings
   - Navigate to "Secrets and variables" > "Actions"
   - Confirm that the `ARM_*` variables are present
   - If not, please contac the platform team

2. **Use Azure Login Action in your workflow**
   ```yaml
   - name: 'Az CLI login'
     uses: azure/login@v1
     with:
       creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

3. **Access Key Vault secrets in your workflow**
   ```yaml
   - name: Get secrets
     uses: azure/get-keyvault-secrets@v1
     with:
       keyvault: "myKeyVault"
       secrets: 'secret1, secret2'
     id: myGetSecretAction
   
   - name: Use the secret
     run: |
       echo "My secret value is ${{ steps.myGetSecretAction.outputs.secret1 }}"
   ```

## Local Development

For local development, use a `.env` file to store environment variables. Ensure this file is listed in your `.gitignore`.

Example `.env` file:
```
DB_CONNECTION_STRING=your_local_db_connection_string
API_KEY=your_local_api_key
```

In your application, use a library like `dotenv` (for Node.js) or `python-dotenv` (for Python) to load these environment variables.


## Support

Remember, security is everyone's responsibility. If you're unsure about how to handle a particular secret or have any questions about this process, please reach out to the security team.