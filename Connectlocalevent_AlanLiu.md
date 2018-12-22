# Connect(); Local Event

## Agenda

### Tool
- Azure CLI
- Azure Draft
- Docker
- 
- Kubernetes Client Tool

### Dotnet Core with draft
 - dotnet new mvc -n mycore
 - draft config set registry alanacrhub.azurecr.io/alanacrhub
 - draft create --pack=csharp -a mycoremvc
 - draft up
 - draft connect

### ACSDemo
1. 創建Azure資源組
az group create -l eastus --name ACSDemo

2. 下載Demo Code
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
cd azure-voting-app-redis

3. 運行環境docker-compose up -d
yaml
docker container runing
local run application http://localhost:8080/

4. 創建Azure容器儲存倉庫
az acr create --resource-group ACSDemo --name ACSDemoACR --sku Basic
acr login -n ACSDemoACR

5. docker login with ACR
open login user with azure portal 
docker login -u ACSDemoACR -p j5zkWle=XpnEnbWQNtMNxfxxx acsdemoacr.azurecr.io

6. 標記與推送映像 docker tag&push image
docker tag azure-vote-front acsdemoacr.azurecr.io/azure-vote-front:v1
docker push acsdemoacr.azurecr.io/azure-vote-front:v1

7. 創建Azure Kubernetes服務
az aks create --resource-group ACSDemo --name AKSCluster --node-count 1 --generate-ssh-keys

8. 利用認證連接AKS
az aks get-credentials --resource-group ACSDemo --name AKSCluster
kubectl get nodes

9. 設定ACR與AKS權限串接
CLIENT_ID=$(az aks show --resource-group ACSDemo --name AKSCluster --query "servicePrincipalProfile.clientId" --output tsv)

ACR_ID=$(az acr show --name ACSDemoACR --resource-group ACSDemo --query "id" --output tsv)

az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID

10. 修改yaml配置內的容器來源位置
azure-vote-front > acsdemoacr.azurecr.io

11. 利用Kubectl跟根據yaml部署映像
kubectl apply -f azure-vote-all-in-one-redis.yaml

12. 完成並且瀏覽final kubectl get nodes and bowser
kubectl get service

### Kubernetes IaaS
- 準備工具 - ACS Engine
https://github.com/Azure/acs-engine/releases

- 生成服務主體帳戶
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/5ce27d57-514a-476b-a3c7-xxx/resourceGroups/kubernetes"

- 取得ACS-Kubernetes設定檔
https://raw.githubusercontent.com/Azure/acs-engine/master/examples/kubernetes.json

- 修改設定檔案
code kubernetes-vmss.json

- generate template
./acs-engine generate kubernetes-vmss.json

- 發布ARM Template
az group deployment create --name "alank8sacsenginevmssdeploy" --resource-group "kubernetes2" --template-file "azuredeploy.json" --parameters "azuredeploy.parameters.json"

- 登入遠端服務器
ssh username@13.76.194.81

- 查看節點
Kubectl get nodes

- 擴展節點>VMSS資源展示