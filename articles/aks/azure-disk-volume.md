---
title: Usar discos do Azure com AKS
description: Usar discos do Azure com AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a2f46aba80ad47335b7cd9b5e8d615c1d895cccb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="volumes-with-azure-disks"></a>Volumes com discos do Azure

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Os discos do Azure podem ser UTILIZADOS como esse armazenamento de dados externos. Este artigo detalha o uso de um disco do Azure como um volume do Kubernetes em um cluster do AKS (Serviço de Contêiner do Azure).

Para obter mais informações sobre volumes Kubernetes, consulte [Volumes Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Criar um disco do Azure

Antes de montar um disco gerenciado do Azure como um volume do Kubernetes, o disco deverá existir no mesmo grupo de recursos que os recursos de cluster do AKS. Para localizar esse grupo de recursos, utilize o comando [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Você está procurando um grupo de recursos com um nome semelhante a `MC_clustername_clustername_locaton`, em que clustername é o nome do cluster do AKS e location é a região do Azure onde o cluster foi implantado.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Utilize o comando [az disk create][az-disk-create] para criar o disco do Azure. 

Usando este exemplo, atualize `--resource-group` com o nome do grupo de recursos e `--name` para um nome de sua escolha.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Depois que o disco tiver sido criado, você deverá ver uma saída semelhante à seguinte. Esse valor é a ID do disco, que é utilizado ao montar o disco em um Pod do Kubernetes.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montar o disco como volume

Monte o disco do Azure em seu Pod configurando o volume na especificação do contêiner. 

Crie um novo arquivo chamado `azure-disk-pod.yaml` com os conteúdos a seguir. Atualize `diskName` com o nome do disco recém-criado e `diskURI` com a ID do disco. Além disso, anote o `mountPath`, esse é o caminho no qual o disco do Azure é montado no Pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Utilize o kubectl para criar o Pod.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Agora você tem um Pod em execução com um disco do Azure montado no `/mnt/azure`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os volumes do Kubernetes utilizando discos do Azure.

> [!div class="nextstepaction"]
> [Plug-in do Kubernetes para discos do Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
