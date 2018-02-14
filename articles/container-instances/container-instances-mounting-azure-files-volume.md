---
title: "Monte um volume de Arquivos do Azure em Instâncias de Contêiner do Azure"
description: "Saiba como montar um volume de Arquivos do Azure para persistir o estado com Instâncias de Contêiner do Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: be502e6aef39ee4ed8cfc1f8926cb556dc1defb1
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2018
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Monte um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure

Por padrão, as Instâncias de Contêiner do Azure são sem monitoração de estado. Se o contêiner parar ou falhar, todo o seu estado será perdido. Para persistir o estado além do tempo de vida do contêiner, você deve montar um volume de um repositório externo. Este artigo mostra como montar um compartilhamento de arquivos do Azure para ser usado com Instâncias de Contêiner do Azure.

> [!NOTE]
> Montar um Compartilhamento de Arquivos do Azure está atualmente restrito a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças atuais de plataforma em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

Antes de usar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure, você deve criá-lo. Execute o seguinte script para criar uma conta de armazenamento para hospedar o compartilhamento de arquivos e o próprio compartilhamento. O nome da conta de armazenamento deve ser globalmente exclusivo para que o script adicione um valor aleatório à cadeia de caracteres de base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>Obter credenciais de armazenamento

Para montar um compartilhamento de arquivos do Azure como um volume nas Instâncias de Contêiner do Azure, você precisa de três valores: o nome da conta de armazenamento, o nome do compartilhamento e a chave de acesso de armazenamento.

Se você usou o script acima, o nome da conta de armazenamento foi criado com um valor aleatório no final. Para consultar a cadeia de caracteres final (incluindo a parte aleatória), use os seguintes comandos:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

O nome do compartilhamento já é conhecido (definido como *acishare* no script acima), portanto, falta apenas a chave da conta de armazenamento, que pode ser encontrada usando o seguinte comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Implantar o contêiner e montar o volume

Para montar um compartilhamento de arquivos do Azure como um volume em um contêiner, especifique o compartilhamento e o ponto de montagem do volume ao criar o contêiner com [az container create][az-container-create]. Se você seguiu as etapas anteriores, será possível montar o compartilhamento já criado usando o comando a seguir para criar um contêiner:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>Gerenciar arquivos no volume montado

Depois que o contêiner for iniciado, você poderá usar o aplicativo Web simples implantado por meio da imagem [seanmckenna/aci-hellofiles][aci-hellofiles] para gerenciar arquivos no compartilhamento de arquivos do Azure no caminho de montagem especificado. Obtenha o endereço IP para o aplicativo Web com o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --output table
```

Você pode usar o [Portal do Azure][portal] ou uma ferramenta como o [Gerenciador de Armazenamento do Microsoft Azure][storage-explorer] para recuperar e inspecionar o arquivo gravado no compartilhamento de arquivos.

## <a name="mount-multiple-volumes"></a>Montar vários volumes

Para montar vários volumes em uma instância de contêiner, você deve implantar usando um [modelo do Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, forneça os detalhes de compartilhamento e defina os volumes populando a matriz `volumes` na seção `properties` do modelo. Por exemplo, se você criasse dois compartilhamentos de Arquivos do Azure denominados *share1* e *share2* na conta de armazenamento *myStorageAccount*, a matriz `volumes` teria aparência semelhante à seguinte:

```json
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Em seguida, para cada contêiner do grupo de contêineres no qual você deseja montar os volumes, popule a matriz `volumeMounts` na seção `properties` da definição de contêiner. Por exemplo, isso monta os dois volumes, *myvolume1* e *myvolume2*, definidos anteriormente:

```json
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Para ver um exemplo de implantação de instância de contêiner com um modelo do Azure Resource Manager, consulte [Implantar grupos com vários contêineres em Instâncias de Contêiner do Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a relação entre [Instâncias de Contêiner do Azure e orquestradores de contêiner](container-instances-orchestrator-relationship.md).

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/seanmckenna/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show