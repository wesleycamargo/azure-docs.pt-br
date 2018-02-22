---
title: "Usar o Arquivo do Azure com o Serviço de Contêiner do Azure"
description: Usar discos do Azure com AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ce37cfdd70f95822a912f6ea71b9e4a3f9a30a14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Volumes persistentes com arquivos do Azure

Um volume persistente representa uma parte do armazenamento que foi provisionado para uso em um cluster Kubernetes. Um volume persistente pode ser usado por um ou vários compartimentos e pode ser estática ou dinamicamente provisionado. Este documento detalha o provisionamento dinâmico de um compartilhamento de arquivos do Azure como um volume persistente Kubernetes em um cluster AKS. 

Para obter mais informações sobre volumes persistentes Kubernetes, consulte [Volumes persistentes Kubernetes][kubernetes-volumes].

## <a name="prerequisites"></a>pré-requisitos

Durante o provisionamento dinâmico de um compartilhamento de arquivos do Azure como um volume Kubernetes, qualquer conta de armazenamento pode ser usada como está contido no mesmo grupo de recursos de cluster AKS. Se necessário, crie uma conta de armazenamento no mesmo grupo de recursos que o cluster AKS. 

Para identificar o grupo de recursos apropriado, use o comando [lista de grupos az][az-group-list].

```azurecli-interactive
az group list --output table
```

A saída de exemplo a seguir mostra os grupos de recursos, ambos associados a um cluster AKS. O grupo de recursos com um nome como *MC_myAKSCluster_myAKSCluster_eastus* contém os recursos de cluster AKS e é onde a conta de armazenamento precisa ser criado. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Depois que o grupo de recursos tiver sido identificado, criar a conta de armazenamento com o comando [Criar conta de armazenamento az][az-storage-account-create].

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Criar a classe de armazenamento

Uma classe de armazenamento é usada para definir a configuração de um volume persistente criado dinamicamente. Itens como o nome da conta de armazenamento do Azure, SKU e região são definidos no objeto de classe de armazenamento. Para obter mais informações sobre classes de armazenamento Kubernetes, consulte [Classes de Armazenamento Kubernetes][kubernetes-storage-classes].

O exemplo a seguir especifica que qualquer conta de armazenamento do SKU tipo `Standard_LRS` na região `eastus` pode ser usada ao solicitar o armazenamento. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Para usar uma conta de armazenamento específica, o parâmetro `storageAccount` pode ser usado.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Criar declaração de volume persistente

Uma declaração de volume persistente usa o objeto de classe de armazenamento para provisionar dinamicamente uma parte do armazenamento. Ao usar um arquivos do Azure, um compartilhamento de arquivos do Azure é criado na conta de armazenamento selecionado ou especificado no objeto de classe de armazenamento.

>  [!NOTE]
>   Certifique-se de que uma conta de armazenamento adequada foi criada previamente no mesmo grupo de recursos, como os recursos de cluster AKS. Este grupo de recursos tem um nome como *MC_myAKSCluster_myAKSCluster_eastus*. A declaração de volume persistente falhará para provisionar o compartilhamento de arquivos Azure se uma conta de armazenamento não estiver disponível. 

O manifesto a seguir pode ser usado para criar uma declaração de volume persistente `5GB` de tamanho com acesso `ReadWriteOnce`. Para obter mais informações sobre modos de acesso PVC, consulte [modos de acesso][access-modes].

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

## <a name="using-the-persistent-volume"></a>Usando o volume persistente

Quando a declaração do volume persistente tiver sido criada, e o armazenamento provisionado com êxito, um pod pode ser criado com acesso ao volume. O manifesto a seguir cria um pod que usa a declaração de volume persistente `azurefile` para montar o compartilhamento de arquivos do Azure no caminho `/var/www/html`. 

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
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

## <a name="mount-options"></a>Opções de montagem

Valores padrão de fileMode e dirMode diferem entre as versões Kubernetes conforme descrito na tabela a seguir. 

| version | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v.1.8.6 ou superior | 0755 |
| v1.9.0 | 0700 |
| v.1.9.1 ou superior | 0755 |

Se usar um cluster de versão 1.8.5 ou maior, as opções de montagem podem ser especificadas no objeto de classe de armazenamento. O exemplo a seguir configura `0777`. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Se usar um cluster de versão 1.8.0 - 1.8.4, um contexto de segurança pode ser especificado com o valor `runAsUser` definido como `0`. Para obter mais informações sobre o contexto de segurança Pod, consulte [Configurar um contexto de segurança][kubernetes-security-context].

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
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create