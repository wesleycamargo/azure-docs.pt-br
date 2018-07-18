---
title: Usar o Arquivo do Azure com o Serviço de Contêiner do Azure
description: Usar discos do Azure com AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 0479e4d80b7490db170255d47ef3190bb744d2d8
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901486"
---
# <a name="volumes-with-azure-files"></a>Volumes com arquivos do Azure

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Os arquivos do Azure podem ser usados como esse armazenamento de dados externo. Este artigo detalha o uso de arquivos do Azure como um volume Kubernetes no Serviço de Kubernetes do Azure.

Para obter mais informações sobre volumes Kubernetes, consulte [Volumes Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

Antes de usar um Compartilhamento de Arquivo do Azure como um volume Kubernetes, você deve criar uma conta de Armazenamento do Azure e o compartilhamento de arquivos. O script a seguir pode ser usado para concluir essas tarefas. Tome nota ou atualize os valores de parâmetro, alguns deles são necessários para criar o volume Kubernetes.

```azurecli-interactive
#!/bin/bash

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

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Criar segredo Kubernetes

O Kubernetes precisa de credenciais para acessar o compartilhamento de arquivos. Essas credenciais são armazenadas em um [segredo Kubernetes][kubernetes-secret], que é referenciado na criação de um pod Kubernetes.

Utilize o comando a seguir para criar o segredo. Substitua `STORAGE_ACCOUNT_NAME` pelo nome da sua conta de armazenamento e `STORAGE_ACCOUNT_KEY` pela sua chave de armazenamento.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Montar compartilhamento de arquivos como volume

Monte seu compartilhamento de Arquivos do Azure em seu pod configurando o volume em suas especificações. Crie um novo arquivo chamado `azure-files-pod.yaml` com os conteúdos a seguir. Atualize `aksshare` com o nome fornecido para o compartilhamento de Arquivos do Azure.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
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

Agora você tem um contêiner em execução com o compartilhamento de arquivos do Azure montado no diretório `/mnt/azure`.  Você pode ver a montagem do volume ao inspecionar o pod via `kubectl describe pod azure-files-pod`.

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
