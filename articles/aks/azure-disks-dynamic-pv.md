---
title: Usar Discos do Azure com AKS
description: Usar discos do Azure com AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/25/2018
ms.author: nepeters
ms.openlocfilehash: aa89cf9fe4e2cd5b63017558e89401de86effdc9
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Volumes persistentes com discos do Azure

Um volume persistente representa uma parte do armazenamento que foi provisionado para uso em um cluster Kubernetes. Um volume persistente pode ser usado por um ou vários compartimentos e pode ser estática ou dinamicamente provisionado. Este documento detalha o provisionamento dinâmico de um disco do Azure como um volume persistente Kubernetes em um cluster AKS. 

Para obter mais informações sobre volumes persistentes Kubernetes, consulte [Volumes persistentes Kubernetes][kubernetes-volumes].

## <a name="built-in-storage-classes"></a>Interno de classes de armazenamento

Uma classe de armazenamento é usada para definir a configuração de um volume persistente criado dinamicamente. Para obter mais informações sobre classes de armazenamento Kubernetes, consulte [Classes de Armazenamento Kubernetes][kubernetes-storage-classes].

Cada cluster AKS inclui duas classes de armazenamento criadas previamente, ambas configuradas para trabalhar com discos do Azure. Use o comando `kubectl get storageclass` para vê-las.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

Se essas classes de armazenamento atenderem às suas necessidades, não será necessário criar uma nova.

## <a name="create-storage-class"></a>Criar a classe de armazenamento

Se você quiser criar uma nova classe de armazenamento configurada para os discos do Azure, faça isso usando o exemplo de manifesto a seguir. 

O valor `storageaccounttype` de `Standard_LRS` indica que um disco padrão foi criado. Esse valor pode ser alterado para `Premium_LRS` a fim de criar um [disco premium][premium-storage]. Para usar discos premium, a máquina de virtual do nó do AKS deverá ter um tamanho compatível com os discos premium. Veja [este documento][premium-storage] para obter uma lista de tamanhos compatíveis.

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```

## <a name="create-persistent-volume-claim"></a>Criar declaração de volume persistente

Uma declaração de volume persistente usa um objeto de classe de armazenamento para provisionar dinamicamente uma parte do armazenamento. Ao usar um disco do Azure, o disco é criado no mesmo grupo de recursos que os recursos do AKS.

Este exemplo de manifesto cria uma declaração de volume persistente usando a classe de armazenamento `azure-managed-disk`, para criar um disco de tamanho `5GB` com acesso `ReadWriteOnce`. Para obter mais informações sobre modos de acesso PVC, consulte [modos de acesso][access-modes].

> [!NOTE]
> Um disco do Azure só pode ser montado com o tipo de modo de acesso ReadWriteOnce, que o torna disponível para um único nó AKS. Se precisar compartilhar um volume persistente em vários nós, considere usar [Arquivos do Azure][azure-files-pvc]. 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>Usando o volume persistente

Quando a declaração do volume persistente tiver sido criada, e o disco tiver sido provisionado com êxito, um pod poderá ser criado com acesso ao disco. O manifesto a seguir cria um pod que usa a declaração de volume persistente `azure-managed-disk` para montar o disco do Azure no caminho `/var/www/html`. 

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
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre volumes persistentes Kubernetes usando os discos do Azure.

> [!div class="nextstepaction"]
> [Plug-in do Kubernetes para discos do Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md