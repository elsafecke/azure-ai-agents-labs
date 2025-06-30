# Lab 1: Troubleshooting Authentication Issues with Azure SDK in Labs

If you encounter authentication errors when running the lab 1, even after successfully running `az login`. This guide explains why these issues occur and how to resolve them.

---

## Common Error Message

```
DefaultAzureCredential failed to retrieve a token from the included credentials.Attempted credentials:
	EnvironmentCredential: EnvironmentCredential authentication unavailable. Environment variables are not fully configured.Visit https://aka.ms/azsdk/python/identity/environmentcredential/troubleshoot to troubleshoot this issue.
	ManagedIdentityCredential: ManagedIdentityCredential authentication unavailable, no response from the IMDS endpoint.
	SharedTokenCacheCredential: SharedTokenCacheCredential authentication unavailable. No accounts were found in the cache.
	AzureCliCredential: Azure CLI not found on path
	AzurePowerShellCredential: Az.Account module >= 2.2.0 is not installed
	AzureDeveloperCliCredential: Azure Developer CLI could not be found. Please visit https://aka.ms/azure-dev for installation instructions and then,once installed, authenticate to your Azure account using 'azd auth login'.To mitigate this issue, please refer to the troubleshooting guidelines here at https://aka.ms/azsdk/python/identity/defaultazurecredential/troubleshoot.
```

---

## Why This Happens

- The Azure SDK's `DefaultAzureCredential` tries several authentication methods in order, including:
  - Environment variables (not set, so skipped)
  - Managed Identity (not available on local machines)
  - Shared Token Cache (not set up)
  - **AzureCliCredential** (should work if Azure CLI is installed and user is logged in)
  - Azure PowerShell (not installed)
  - Azure Developer CLI (not installed)

The key part of the error is:

> `AzureCliCredential: Azure CLI not found on path`

This means that, even though Azure CLI is installed and `az login` works in the terminal, the Python process (VS Code or Jupyter) cannot find the Azure CLI executable in the system PATH.

---

## Common Causes & Solutions

### 1. VS Code or Jupyter was opened before Azure CLI was installed
- **Solution:** Close all VS Code and terminal windows, then reopen VS Code after installing Azure CLI.

### 2. Azure CLI is not in the PATH for the Python process
- **Solution:** Ensure `az` is available in the same terminal where you run Python/Jupyter/VS Code. Run `az --version` to check.

### 3. Python/Jupyter kernel is running in a different environment
- **Solution:** Restart the Jupyter kernel after installing Azure CLI.

### 4. Azure CLI is installed, but not in a standard location
- **Solution:** Make sure Azure CLI is installed in a location included in your system PATH.

---

## Step-by-Step Troubleshooting

1. **Verify Azure CLI is in PATH**
   - Open a new terminal in VS Code and run:
     ```
     az --version
     ```
   - If this fails, Azure CLI is not in your PATH.

2. **Check if you are logged in to Azure CLI**
   - Even if Azure CLI is installed, you must be logged in for authentication to work.
   - Run:
     ```
     az login
     ```
   - If you are not logged in, follow the prompts to sign in. This often resolves the error immediately.

3. **Restart VS Code and Jupyter kernel**
   - Close all VS Code windows and terminals.
   - Reopen VS Code, open your project, and restart the Jupyter kernel.

4. **Check Python environment**
   - Make sure you are using the correct Python interpreter/virtual environment in VS Code.

5. **(Optional) Install Azure Developer CLI**
   - Some users may see errors about AzureDeveloperCliCredential. This is not required if Azure CLI works, but installing it can provide another fallback:
     - Download from: https://aka.ms/azure-dev
     - Run: `azd auth login`

6. **(Optional) Set up Environment Variables**
   - You can also authenticate by setting environment variables for a service principal, but this is not required for most users.

---

## Summary for Lab Users

- Make sure Azure CLI is installed and in your PATH.
- You must be logged in to Azure CLI. If you see authentication errors, try running `az login`.
- Always restart VS Code and the Jupyter kernel after installing Azure CLI or logging in.
- Verify `az --version` works in the same terminal as your Python code.
- If you still see the error, try installing the Azure Developer CLI and running `azd auth login`.

For more details, see the official troubleshooting guide: [DefaultAzureCredential Troubleshooting](https://aka.ms/azsdk/python/identity/defaultazurecredential/troubleshoot)
