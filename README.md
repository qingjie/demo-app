Steps will be:

1. Create a resource group
2. Create ACR
3. Create a service principal ID for our deployments
4. Build our container
5. Configure Azure Container App 
6. Enable CI/CD to build on a push to the GitHub repo
7. Review and update revisions.

---
```
az account list --output table
az account set --subscription "AMP Microsoft Azure Sandbox"

az group create --name qzhaodemo --location eastus    
az acr create --name qzhaodemo --resource-group qzhaodemo --sku Standard --admin-enabled
az ad sp create-for-rbac --name demoappqzhao01 --role contributor



az acr build --image demo-app:latest --registry qzhaodemo --platform linux https://github.com/qingjie/demo-app
```
---
```
az login
az extension add --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.4-py2.py3-none-any.whl

az provider register --namespace ns-tst

RESOURCE_GROUP="homeport-tst-rg"
LOCATION="canadacentral"
LOG_ANALYTICS_WORKSPACE="homeport-tst-logs"
CONTAINERAPPS_ENVIRONMENT="homeport-tst"

az group create --name $RESOURCE_GROUP --location $LOCATION

Create a Log Analytics workspace:
az monitor log-analytics workspace create --resource-group $RESOURCE_GROUP --workspace-name $LOG_ANALYTICS_WORKSPACE

Retrieve the Log Analytics Client ID and client secret:
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE -o tsv | tr -d '[:space:]'`
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE -o tsv | tr -d '[:space:]'`

Create the environment:
az containerapp env create \
  --name $CONTAINERAPPS_ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET \
  --location $LOCATION

Create a container app:
az containerapp create \
  --name tst \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
  --target-port 80 \
  --ingress 'external' \
  --query configuration.ingress.fqdn


Clean up resources:
az group delete \
  --name $RESOURCE_GROUP
```
#### https://docs.microsoft.com/en-us/azure/container-apps/get-started?tabs=bash
