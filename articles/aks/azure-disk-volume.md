---
title: Usar discos do Azure com AKS
description: Usar discos do Azure com AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4af4620ff7a17cae76c4d5f2cf1a30ce4a3dccd8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "34597060"
---
# <a name="volumes-with-azure-disks"></a>Volumes com discos do Azure

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Os discos do Azure podem ser UTILIZADOS como esse armazenamento de dados externos. Este artigo detalha o uso de um disco do Azure como um volume do Kubernetes em um cluster do AKS (Serviço de Kubernetes do Azure).

Para obter mais informações sobre volumes Kubernetes, consulte [Volumes Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Criar um disco do Azure

Antes de montar um disco gerenciado do Azure como um volume do Kubernetes, o disco deverá existir no grupo de recursos do **nó** do AKS. Obtenha o nome do grupo de recursos com o comando [az resource show][az-resource-show].

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Utilize o comando [az disk create][az-disk-create] para criar o disco do Azure.

Atualize `--resource-group` com o nome do grupo de recursos obtido na última etapa e `--name` para um nome de sua escolha.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Depois que o disco tiver sido criado, você deverá ver uma saída semelhante à seguinte. Esse valor é a ID do disco, que é utilizada ao montar o disco.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montar o disco como volume

Monte o disco do Azure em seu Pod configurando o volume na especificação do contêiner.

Crie um novo arquivo chamado `azure-disk-pod.yaml` com os conteúdos a seguir. Atualize `diskName` com o nome do disco recém-criado e `diskURI` com a ID do disco. Além disso, anote o `mountPath`, que é o caminho no qual o disco do Azure é montado no pod.

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
[az-resource-show]: /cli/azure/resource#az-resource-show
