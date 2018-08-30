---
title: Criar volumes persistentes com o Serviço de Kubernetes do Azure
description: Saiba como usar os discos do Azure para criar volumes persistentes para pods no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: 7048ab4e08d25fd5181857a4e7592d0bcb7d3b5f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885587"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Criar volumes persistentes com discos do Azure para AKS (Serviço de Kubernetes do Azure)

Um volume persistente representa uma parte do armazenamento que foi provisionada para uso com pods do Kubernetes. Um volume persistente pode ser usado por um ou vários pods e pode ser provisionado estática ou dinamicamente. Para obter mais informações sobre volumes persistentes Kubernetes, consulte [Volumes persistentes Kubernetes][kubernetes-volumes]. Este artigo mostra como usar volumes persistentes com discos do Azure em um cluster do AKS (Serviço de Kubernetes do Azure).

> [!NOTE]
> Um disco do Azure só pode ser montado com o tipo de *modo de acesso* *ReadWriteOnce*, que o torna disponível para um único nó AKS. Se precisar compartilhar um volume persistente em vários nós, considere usar [Arquivos do Azure][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Interno de classes de armazenamento

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre classes de armazenamento Kubernetes, consulte [Classes de Armazenamento Kubernetes][kubernetes-storage-classes].

Cada cluster AKS inclui duas classes de armazenamento criadas previamente, ambas configuradas para funcionarem com discos do Azure:

* A classe de armazenamento *padrão* provisiona um disco standard do Azure.
    * O armazenamento padrão é apoiado por HDDs e oferece armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.
* A classe de armazenamento *managed-premium* provisiona um disco premium do Azure.
    * Os discos Premium são apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. Se os nós do AKS no cluster usarem o armazenamento premium, selecione a classe *managed-premium*.

Use o comando [kubectl get sc][kubectl-get] para ver as classes de armazenamento pré-criadas. A exemplo a seguir mostra as classes de armazenamento criadas previamente disponíveis dentro de um cluster do AKS:

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Declarações de volume persistentes são especificadas em GiB, mas os discos gerenciados do Azure são cobrados por SKU para um tamanho específico. Essas SKUs variam de 32GiB para discos S4 ou P4 discos a 4TiB para discos S50 ou P50. A taxa de transferência e o desempenho de IOPS de um disco gerenciado Premium dependem da SKU e do tamanho da instância dos nós no cluster do AKS. Para saber mais, confira [Preços e desempenho do Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Criar uma declaração de volume persistente

Um PVC (declaração de volume persistente) é usado para provisionar automaticamente o armazenamento com base em uma classe de armazenamento. Nesse caso, um PVC pode usar uma das classes de armazenamento pré-criadas para criar um disco gerenciado standard ou premium do Azure.

Crie um arquivo chamado `azure-premium.yaml` e copie-o para o manifesto a seguir. A declaração solicita um disco chamado `azure-managed-disk` que tenha *5 GB* de tamanho com acesso *ReadWriteOnce*. A classe de armazenamento *premium gerenciado* é especificada como a classe de armazenamento.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Para criar um disco que use o armazenamento padrão, use `storageClassName: default` em vez de *premium gerenciado*.

Crie a declaração de volume persistente com o comando [kubectl create][kubectl-create] e especifique seu arquivo *azure-premium.yaml*:

```
$ kubectl create -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Usar o volume persistente

Quando a declaração do volume persistente tiver sido criada e o disco tiver sido provisionado com êxito, um pod poderá ser criado com acesso ao disco. O manifesto a seguir cria um pod NGINX básico que usa a declaração de volume persistente chamada *azure-managed-disk* para montar o disco do Azure no caminho `/mnt/azure`.

Crie um arquivo chamado `azure-pvc-disk.yaml` e copie-o para o manifesto a seguir.

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
        claimName: azure-managed-disk
```

Crie o pod com o comando [kubectl create][kubectl-create], conforme mostrado no exemplo a seguir:

```
$ kubectl create -f azure-pvc-disk.yaml

pod/mypod created
```

Agora você tem um pod em execução com o disco do Azure montado no diretório `/mnt/azure`. Essa configuração pode ser vista ao inspecionar o pod via `kubectl describe pod mypod`, conforme mostrado no exemplo condensado a seguir:

```
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false

Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Fazer backup de um volume persistente

Para fazer backup dos dados no seu volume persistente, crie um instantâneo do disco gerenciado para o volume. Em seguida, você pode usar esse instantâneo para criar um disco restaurado e anexá-lo a pods como um meio de restauração dos dados.

Primeiro, obtenha o nome do volume com o comando `kubectl get pvc`, como para o PVC chamado *azure-managed-disk*:

```
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Esse nome de volume forma o nome do disco do Azure subjacente. Consulte a ID do disco com [lista de discos az][az-disk-list] e forneça seu nome de volume PVC, conforme mostrado no exemplo a seguir:

```
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Use a ID do disco para criar um disco de instantâneo com [az snapshot create][az-snapshot-create]. O exemplo a seguir cria um instantâneo denominado *pvcSnapshot* no mesmo grupo de recursos que o cluster do AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Você poderá encontrar problemas de permissão se criar instantâneos e restaurar discos em grupos de recursos aos quais o cluster do AKS não tenha acesso.

```azurecli
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Dependendo da quantidade de dados no disco, poderão ser necessários alguns minutos para criar o instantâneo.

## <a name="restore-and-use-a-snapshot"></a>Restaurar e usar um instantâneo

Para restaurar o disco e usá-lo com um pod Kubernetes, use o instantâneo como uma fonte ao criar um disco com [az disk create][az-disk-create]. Essa operação preservará o recurso original se você então precisar acessar o instantâneo de dados original. O exemplo a seguir cria um disco chamado *pvcRestored* com base no instantâneo denominado *pvcSnapshot*:

```azurecli
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Para usar o disco restaurado com um pod, especifique a ID do disco no manifesto. Obtenha a ID do disco com o comando [az disk show][az-disk-show]. O exemplo a seguir obtém a ID do disco para o *pvcRestored* criado na etapa anterior:

```azurecli
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Crie um manifesto de pod chamado `azure-restored.yaml` e especifique o URI do disco obtido na etapa anterior. O exemplo a seguir cria um servidor Web NGINX básico, com o disco restaurado montado como um volume em */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
    - name: myfrontendrestored
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Crie o pod com o comando [kubectl create][kubectl-create], conforme mostrado no exemplo a seguir:

```
$ kubectl create -f azure-restored.yaml

pod/mypodrestored created
```

Você pode usar `kubectl describe pod mypodrestored` para exibir detalhes de pod, como no seguinte exemplo condensado que mostra as informações de volume:

```
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre volumes persistentes Kubernetes usando os discos do Azure.

> [!div class="nextstepaction"]
> [Plug-in do Kubernetes para discos do Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
