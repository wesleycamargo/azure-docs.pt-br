---
title: Usar o Arquivo do Azure com o Serviço de Contêiner do Azure
description: Usar discos do Azure com AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d3e92902e711ba2b1664c6497ecb66f035ea9308
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597494"
---
# <a name="persistent-volumes-with-azure-files"></a>Volumes persistentes com arquivos do Azure

Um volume persistente é uma parte do armazenamento que foi criado para uso em um cluster Kubernetes. Um volume persistente pode ser usado por um ou vários pods e pode ser criado estática ou dinamicamente. Este documento detalha **criação dinâmica** de um compartilhamento de arquivos do Azure como um volume persistente.

Para obter mais informações sobre volumes persistentes Kubernetes, incluindo a criação estática, consulte [Volumes persistentes Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Criar Conta de Armazenamento

Durante a criação dinâmica de um compartilhamento de arquivos do Azure como um volume Kubernetes, qualquer conta de armazenamento pode ser usada, desde que esteja contida no mesmo grupo de recursos do **nó** do AKS. Obtenha o nome do grupo de recursos com o comando [az resource show][az-resource-show].

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Use o comando [az storage account create][az-storage-account-create] para criar a conta de armazenamento.

Atualize `--resource-group` com o nome do grupo de recursos obtido na última etapa e `--name` para um nome de sua escolha.

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
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

Crie a classe de armazenamento com o comando [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Criar declaração de volume persistente

Uma PVC (declaração de volume persistente) usa o objeto de classe de armazenamento para provisionar dinamicamente um compartilhamento de arquivos do Azure.

O YAML a seguir pode ser usado para criar uma declaração de volume persistente `5GB` de tamanho com acesso `ReadWriteMany`. Para obter mais informações sobre modos de acesso, consulte a documentação do [volume persistente de Kubernetes][access-modes].

Crie um arquivo chamado `azure-file-pvc.yaml` e copie no YAML a seguir. Verifique se o `storageClassName` corresponde à classe de armazenamento criada na última etapa.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Crie a declaração de volume persistente com o comando [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

Depois de concluído, o compartilhamento de arquivos será criado. Um segredo do Kubernetes que contém informações de conexão e credenciais também é criado.

## <a name="using-the-persistent-volume"></a>Usando o volume persistente

O YAML a seguir cria um pod que usa a declaração de volume persistente `azurefile` para montar o compartilhamento de arquivos do Azure no caminho `/mnt/azure`.

Crie um arquivo chamado `azure-pvc-files.yaml` e copie no YAML a seguir. Verifique se o `claimName` corresponde ao PVC criado na última etapa.

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

Crie o pod com o comando [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

Agora você tem um pod em execução com o disco do Azure montado no diretório `/mnt/azure`. Essa configuração pode ser vista ao inspecionar o pod via `kubectl describe pod mypod`.

## <a name="mount-options"></a>Opções de montagem

Valores padrão de fileMode e dirMode diferem entre as versões Kubernetes conforme descrito na tabela a seguir.

| version | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v.1.8.6 ou superior | 0755 |
| v1.9.0 | 0700 |
| v.1.9.1 ou superior | 0755 |

Se usar um cluster de versão 1.8.5 ou superior e criar dinamicamente o volume persistente com uma classe de armazenamento, opções de montagem poderão ser especificadas no objeto de classe de armazenamento. O exemplo a seguir configura `0777`.

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

Se usar um cluster de versão 1.8.5 ou superior e criar estaticamente o objeto de volume persistente, opções de montagem precisarão ser especificadas no objeto `PersistentVolume`. Para obter mais informações sobre como criar estaticamente um volume persistente, consulte [Volumes persistentes estáticos][pv-static].

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  ```

Se usar um cluster de versão 1.8.0 - 1.8.4, um contexto de segurança pode ser especificado com o valor `runAsUser` definido como `0`. Para obter mais informações sobre o contexto de segurança Pod, consulte [Configurar um contexto de segurança][kubernetes-security-context].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre volumes persistentes Kubernetes usando os Arquivos do Azure.

> [!div class="nextstepaction"]
> [Plugin do Kubernetes para Arquivos do Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
