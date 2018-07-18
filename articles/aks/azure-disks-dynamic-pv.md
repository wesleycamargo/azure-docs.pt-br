---
title: Usar Discos do Azure com AKS
description: Usar discos do Azure com AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 858961db439b28a71d3475d2608073287e02f2fd
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Volumes persistentes com discos do Azure

Um volume persistente representa uma parte do armazenamento que foi provisionada para uso com pods do Kubernetes. Um volume persistente pode ser usado por um ou vários pods e pode ser provisionado estática ou dinamicamente. Para obter mais informações sobre volumes persistentes Kubernetes, consulte [Volumes persistentes Kubernetes][kubernetes-volumes].

Este documento fornece detalhes sobre o uso de volumes persistentes com discos do Azure em um cluster do AKS (Serviço de Kubernetes do Azure).

> [!NOTE]
> Um disco do Azure só pode ser montado com o tipo de modo de acesso ReadWriteOnce, que o torna disponível para um único nó AKS. Se precisar compartilhar um volume persistente em vários nós, considere usar [Arquivos do Azure][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Interno de classes de armazenamento

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre classes de armazenamento Kubernetes, consulte [Classes de Armazenamento Kubernetes][kubernetes-storage-classes].

Cada cluster AKS inclui duas classes de armazenamento criadas previamente, ambas configuradas para trabalhar com discos do Azure. A classe de armazenamento `default` provisiona um disco standard do Azure. A classe de armazenamento `managed-premium` provisiona um disco premium do Azure. Se os nós do AKS no cluster usarem o armazenamento premium, selecione a classe `managed-premium`.

Use o comando [kubectl get sc][kubectl-get] para ver as classes de armazenamento pré-criadas.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>Criar declaração de volume persistente

Um PVC (declaração de volume persistente) é usado para provisionar automaticamente o armazenamento com base em uma classe de armazenamento. Nesse caso, um PVC pode usar uma das classes de armazenamento pré-criadas para criar um disco gerenciado standard ou premium do Azure.

Crie um arquivo chamado `azure-premimum.yaml` e copie-o para o manifesto a seguir.

Observe que a classe de armazenamento `managed-premium` é especificada na anotação e a declaração solicita um disco de `5GB` com acesso `ReadWriteOnce`.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Crie a declaração de volume persistente com o comando [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-premimum.yaml
```

## <a name="using-the-persistent-volume"></a>Usando o volume persistente

Quando a declaração do volume persistente tiver sido criada, e o disco tiver sido provisionado com êxito, um pod poderá ser criado com acesso ao disco. O manifesto a seguir cria um pod que usa a declaração de volume persistente `azure-managed-disk` para montar o disco do Azure no caminho `/mnt/azure`.

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

Crie o pod com o comando [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

Agora você tem um pod em execução com o disco do Azure montado no diretório `/mnt/azure`. Essa configuração pode ser vista ao inspecionar o pod via `kubectl describe pod mypod`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre volumes persistentes Kubernetes usando os discos do Azure.

> [!div class="nextstepaction"]
> [Plug-in do Kubernetes para discos do Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md