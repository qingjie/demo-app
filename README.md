Steps will be:

1. Create a resource group
2. Create ACR
3. Create a service principal ID for our deployments
4. Build our container
5. Configure Azure Container App 
6. Enable CI/CD to build on a push to the GitHub repo
7. Review and update revisions.

---
az account list --output table
az account set --subscription "AMP Microsoft Azure Sandbox"

az group create --name qzhaodemo --location eastus    
az acr create --name qzhaodemo --resource-group qzhaodemo --sku Standard --admin-enabled
az ad sp create-for-rbac --name demoappqzhao01 --role contributor



az acr build --image demo-app:latest --registry qzhaodemo --platform linux https://github.com/qingjie/demo-app
