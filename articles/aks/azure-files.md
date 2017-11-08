---
title: Usar o Arquivo do Azure com AKS | Microsoft Docs
description: Usar discos do Azure com AKS
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6e88c590e11aa8d2f4ae17e8b5e164483f0a6820
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Como usar os Arquivos do Azure com Kubernetes

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Os arquivos do Azure podem ser usados como esse armazenamento de dados externo. Este artigo detalha o uso de arquivos do Azure como um volume Kubernetes no Serviço de Contêiner do Azure.

Para obter mais informações sobre volumes Kubernetes, consulte [Volumes Kubernetes][kubernetes-volumes].

## <a name="creating-a-file-share"></a>Criar um compartilhamento de arquivos

Um compartilhamento de Arquivos do Azure existente pode ser usado com o Serviço de Contêiner do Azure. Se precisar criar um, use o seguinte conjunto de comandos.

Crie um grupo de recursos para o compartilhamento de Arquivos do Azure usando o comando [az group create][az-group-create]. O grupo de recursos da conta de armazenamento e o cluster Kubernetes devem estar localizados na mesma região.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Use o comando [az storage account create][az-storage-create] para criar uma conta de Armazenamento do Azure. O nome da conta de armazenamento deve ser exclusivo. Atualize o valor do argumento `--name` com um valor exclusivo.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

Use o comando [az storage account keys list ][az-storage-key-list] para retornar a chave de armazenamento. Atualize o valor do argumento `--account-name` com o nome da conta de armazenamento exclusivo.

Tome nota de um dos valores de chave, ele será usado nas etapas subsequentes.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

Use o comando [az storage share create][az-storage-share-create] para criar o compartilhamento de Arquivos do Azure. Atualize o valor de `--account-key` com o valor coletado na última etapa.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Criar segredo Kubernetes

O Kubernetes precisa de credenciais para acessar o compartilhamento de arquivos. Em vez de armazenar o nome da conta de armazenamento do Azure e a chave com cada pod, ele é armazenado uma vez em um [segredo do Kubernetes][kubernetes-secret] e referenciado por cada volume de Arquivos do Azure. 

Os valores em um manifesto de segredo do Kubernetes devem ser codificados com base64. Use os seguintes comandos para retornar valores codificados.

Primeiro, codifique o nome da conta de armazenamento. Substitua `storage-account` pelo nome da sua conta de armazenamento do Azure.

```azurecli-interactive
echo -n <storage-account> | base64
```

Em seguida, a chave de acesso da conta de armazenamento é necessária. Execute o comando a seguir para retornar a chave codificada. Substituir `storage-key` pela chave coletada em uma etapa anterior

```azurecli-interactive
echo -n <storage-key> | base64
```

Crie um arquivo chamado `azure-secret.yml` e copie no YAML a seguir. Atualize os valores de `azurestorageaccountname` e `azurestorageaccountkey` valores codificados com base64 recuperados na última etapa.

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

Use o comando [kubectl apply][kubectl-apply] para criar o segredo.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Montar compartilhamento de arquivos como volume

Você pode montar seu compartilhamento de Arquivos do Azure em seu pod configurando o volume em suas especificações. Crie um novo arquivo chamado `azure-files-pod.yml` com os conteúdos a seguir. Atualize `share-name` com o nome fornecido para o compartilhamento de Arquivos do Azure.

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
      shareName: <share-name>
      readOnly: false
```

Use kubectl para criar um pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

Agora você tem um contêiner em execução com o compartilhamento de arquivos do Azure montado no diretório `/mnt/azure`. Você pode ver a montagem do volume ao inspecionar o pod via `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre volumes Kubernetes usando os Arquivos do Azure.

> [!div class="nextstepaction"]
> [Plugin do Kubernetes para Arquivos do Azure](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create