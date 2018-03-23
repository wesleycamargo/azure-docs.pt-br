---
title: Usar o Arquivo do Azure com o Serviço de Contêiner do Azure
description: Usar discos do Azure com AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a5126bc4c5e7c9cd9832f33fc908e6c8b9e02b91
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Volumes persistentes com arquivos do Azure

Um volume persistente representa uma parte do armazenamento que foi provisionado para uso em um cluster Kubernetes. Um volume persistente pode ser usado por um ou vários pods e pode ser provisionado estática ou dinamicamente. Este documento detalha o provisionamento dinâmico de um compartilhamento de arquivos do Azure como um volume persistente Kubernetes em um cluster AKS. 

Para obter mais informações sobre volumes persistentes Kubernetes, consulte [Volumes persistentes Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Criar Conta de Armazenamento

Durante o provisionamento dinâmico de um compartilhamento de arquivos do Azure como um volume Kubernetes, qualquer conta de armazenamento pode ser usada como está contido no mesmo grupo de recursos de cluster AKS. Se necessário, crie uma conta de armazenamento no mesmo grupo de recursos que o cluster AKS. 

Para identificar o grupo de recursos apropriado, use o comando [lista de grupos az][az-group-list].

```azurecli-interactive
az group list --output table
```

Você está procurando um grupo de recursos com um nome semelhante a `MC_clustername_clustername_locaton`, em que clustername é o nome do cluster do AKS e location é a região do Azure onde o cluster foi implantado.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Use o comando [az storage account create][az-storage-account-create] para criar a conta de armazenamento. 

Usando este exemplo, atualize `--resource-group` com o nome do grupo de recursos e `--name` para um nome de sua escolha.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Criar a classe de armazenamento

Uma classe de armazenamento é usada para definir como um compartilhamento de arquivos do Azure é criado. Uma conta de armazenamento específica pode ser especificada na classe. Se uma conta de armazenamento não for especificada, um `skuName` e uma `location` precisarão ser especificados, e todas as contas de armazenamento no grupo de recursos associado serão avaliadas quanto a uma correspondência.

Para obter mais informações sobre as classes de armazenamento do Kubernetes em arquivos do Azure, consulte [Classes de armazenamento do Kubernetes][kubernetes-storage-classes].

Crie um arquivo chamado `azure-file-sc.yaml` e copie-o para o manifesto a seguir. Atualize a `storageAccount` com o nome de sua conta de armazenamento de destino.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

Crie a classe de armazenamento com o comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Criar declaração de volume persistente

Uma PVC (declaração de volume persistente) usa o objeto de classe de armazenamento para provisionar dinamicamente um compartilhamento de arquivos do Azure. 

O manifesto a seguir pode ser usado para criar uma declaração de volume persistente `5GB` de tamanho com acesso `ReadWriteOnce`.

Crie um arquivo chamado `azure-file-pvc.yaml` e copie-o para o manifesto a seguir. Verifique se o `storageClassName` corresponde à classe de armazenamento criada na última etapa.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Crie a declaração de volume persistente com o comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

Depois de concluído, o compartilhamento de arquivos será criado. Um segredo do Kubernetes que contém informações de conexão e credenciais também é criado.

## <a name="using-the-persistent-volume"></a>Usando o volume persistente

O manifesto a seguir cria um pod que usa a declaração de volume persistente `azurefile` para montar o compartilhamento de arquivos do Azure no caminho `/mnt/azure`.

Crie um arquivo chamado `azure-pvc-files.yaml` e copie-o para o manifesto a seguir. Verifique se o `claimName` corresponde ao PVC criado na última etapa.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Crie o pod com o comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

Agora você tem um pod em execução com o disco do Azure montado no diretório `/mnt/azure`. Você pode ver a montagem do volume ao inspecionar o pod via `kubectl describe pod mypod`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre volumes persistentes Kubernetes usando os Arquivos do Azure.

> [!div class="nextstepaction"]
> [Plugin do Kubernetes para Arquivos do Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
