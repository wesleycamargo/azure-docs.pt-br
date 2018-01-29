---
title: "Usar o Arquivo do Azure com o Serviço de Contêiner do Azure"
description: Usar discos do Azure com AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b6267dd2bc1b29229b2e8016e2429ed88b7bf676
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Como usar os Arquivos do Azure com Kubernetes

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Os arquivos do Azure podem ser usados como esse armazenamento de dados externo. Este artigo detalha o uso de arquivos do Azure como um volume Kubernetes no Serviço de Contêiner do Azure.

Para obter mais informações sobre volumes Kubernetes, consulte [Volumes Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

Antes de usar um Compartilhamento de Arquivo do Azure como um volume Kubernetes, você deve criar uma conta de Armazenamento do Azure e o compartilhamento de arquivos. O script a seguir pode ser usado para concluir essas tarefas. Tome nota ou atualize os valores de parâmetro, alguns deles são necessários para criar o volume Kubernetes.

```azurecli-interactive
# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)
```

## <a name="create-kubernetes-secret"></a>Criar segredo Kubernetes

O Kubernetes precisa de credenciais para acessar o compartilhamento de arquivos. Essas credenciais são armazenadas em um [segredo Kubernetes][kubernetes-secret], que é referenciado na criação de um pod Kubernetes.

Ao criar um segredo Kubernetes, os valores secretos devem ser codificados com base em base64.

Primeiro, codifique o nome da conta de armazenamento. Se necessário, substitua `$AKS_PERS_STORAGE_ACCOUNT_NAME` pelo nome da sua conta de armazenamento do Azure.

```azurecli-interactive
echo -n $AKS_PERS_STORAGE_ACCOUNT_NAME | base64
```

Em seguida, atualize a chave de conta de armazenamento. Se necessário, substitua `$STORAGE_KEY` pelo nome da sua chave de conta de armazenamento do Azure.

```azurecli-interactive
echo -n $STORAGE_KEY | base64
```

Crie um arquivo chamado `azure-secret.yaml` e copie no YAML a seguir. Atualize os valores de `azurestorageaccountname` e `azurestorageaccountkey` valores codificados com base64 recuperados na última etapa.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

Use o comando [kubectl create][kubectl-create] para criar o segredo.

```azurecli-interactive
kubectl create -f azure-secret.yaml
```

## <a name="mount-file-share-as-volume"></a>Montar compartilhamento de arquivos como volume

Você pode montar seu compartilhamento de Arquivos do Azure em seu pod configurando o volume em suas especificações. Crie um novo arquivo chamado `azure-files-pod.yaml` com os conteúdos a seguir. Atualize `aksshare` com o nome fornecido para o compartilhamento de Arquivos do Azure.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Use kubectl para criar um pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Agora você tem um contêiner em execução com o compartilhamento de arquivos do Azure montado no diretório `/mnt/azure`. Você pode ver a montagem do volume ao inspecionar o pod via `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre volumes Kubernetes usando os Arquivos do Azure.

> [!div class="nextstepaction"]
> [Plugin do Kubernetes para Arquivos do Azure][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
