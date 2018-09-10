---
title: Usar o Arquivo do Azure com o Serviço de Contêiner do Azure
description: Usar discos do Azure com AKS
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: ea77244d4b2e078c5eda716e94a97291350228f5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42145669"
---
# <a name="persistent-volumes-with-azure-files"></a>Volumes persistentes com arquivos do Azure

Um volume persistente é uma parte do armazenamento que foi criado para uso em um cluster Kubernetes. Um volume persistente pode ser usado por um ou vários pods e pode ser criado estática ou dinamicamente. Este documento detalha **criação dinâmica** de um compartilhamento de arquivos do Azure como um volume persistente.

Para obter mais informações sobre volumes persistentes Kubernetes, incluindo a criação estática, consulte [Volumes persistentes Kubernetes][kubernetes-volumes].

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Durante a criação dinâmica de um compartilhamento de arquivos do Azure como um volume Kubernetes, qualquer conta de armazenamento pode ser usada, desde que esteja contida no mesmo grupo de recursos do **nó** do AKS. Este grupo é aquele com o prefixo *MC_* que foi criado pelo provisionamento dos recursos para o cluster AKS. Obtenha o nome do grupo de recursos com o comando de [az-aks-show] de [az aks show].

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Use o comando [az storage account create][az-storage-account-create] para criar a conta de armazenamento.

Atualize `--resource-group` com o nome do grupo de recursos obtido na última etapa e `--name` para um nome de sua escolha. Forneça seu próprio nome de conta de armazenamento exclusivo:

```azurecli
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --sku Standard_LRS
```

> [!NOTE]
> Os arquivos do Azure atualmente funciona somente com o armazenamento padrão. Se você usar o armazenamento Premium, o volume falhará para provisionar.

## <a name="create-a-storage-class"></a>Criar uma classe de armazenamento

Uma classe de armazenamento é usada para definir como um compartilhamento de arquivos do Azure é criado. Uma conta de armazenamento pode ser especificada na classe. Se uma conta de armazenamento não for especificada, um *skuName* e *local* deve ser especificado e todas as contas de armazenamento no grupo de recursos associado serão avaliadas para uma correspondência. Para obter mais informações sobre classes de armazenamento do Kubernetes para arquivos do Azure, consulte [Classes de armazenamento Kubernetes][kubernetes-storage-classes].

Crie um arquivo chamado `azure-file-sc.yaml` e copie-o manifesto de exemplo a seguir. Atualize o valor de *storageAccount* com o nome da sua conta de armazenamento criada na etapa anterior. Para obter mais informações sobre *mountOptions*, consulte o [opções de montagem][mount-options] seção.

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
  storageAccount: mystorageaccount
```

Crie a classe de armazenamento com o comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Criar uma associação e função de cluster

Os clusters AKS usam o controle de acesso baseado em função (RBAC) do Kubernetes para limitar as ações que podem ser executadas. *Funções* definem as permissões a serem concedidas e *ligações* as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [autorização usando RBAC][kubernetes-rbac].

Para permitir que a plataforma do Azure criar os recursos de armazenamento necessário, crie uma *clusterrole* e *clusterrolebinding*. Crie um arquivo chamado `azure-pvc-roles.yaml` e o copie no YAML a seguir:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Atribua as permissões com o comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Criar uma declaração de volume persistente

Uma PVC (declaração de volume persistente) usa o objeto de classe de armazenamento para provisionar dinamicamente um compartilhamento de arquivos do Azure. O YAML a seguir pode ser usado para criar uma declaração de volume persistente de *5 GB* de tamanho com *acesso ReadWriteMany*. Para obter mais informações sobre modos de acesso, consulte a documentação do [volume persistente de Kubernetes][access-modes].

Agora, crie um arquivo chamado `azure-file-pvc.yaml` e copie no YAML a seguir. Certifique-se de que o *storageClassName* corresponde à classe de armazenamento criada na última etapa:

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

Crie a declaração de volume persistente com o comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-pvc.yaml
```

Depois de concluído, o compartilhamento de arquivos será criado. Um segredo do Kubernetes que contém informações de conexão e credenciais também é criado. Você pode usar o comando [kubectl get][kubectl-get] para visualizar o status do PVC:

```
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Usar o volume persistente

O YAML a seguir cria um pod que usa a declaração de volume persistente *azurefile* para montar o compartilhamento de arquivos do Azure no caminho */mnt/azure*.

Crie um arquivo chamado `azure-pvc-files.yaml` e copie no YAML a seguir. Certifique-se de que *claimName* corresponda ao PVC criado na última etapa.

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

```console
kubectl apply -f azure-pvc-files.yaml
```

Agora você tem um pod em execução com seu disco do Azure montado no diretório */ mnt / azure*. Essa configuração pode ser vista ao inspecionar o pod via `kubectl describe pod mypod`. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

```
Containers:
  myfrontend:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Wed, 15 Aug 2018 22:22:27 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile2
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opções de montagem

Os valores padrão *fileMode* e *dirMode* diferem entre as versões do Kubernetes, conforme descrito na tabela a seguir.

| version | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v.1.8.6 ou superior | 0755 |
| v1.9.0 | 0700 |
| v.1.9.1 ou superior | 0755 |

Se estiver usando um cluster da versão 1.8.5 ou superior e criar dinamicamente o volume persistente com uma classe de armazenamento, as opções de montagem podem ser especificadas no objeto da classe de armazenamento. O exemplo a seguir define *0777*:

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

Se estiver usando um cluster da versão 1.8.5 ou superior e criando estaticamente o objeto de volume persistente, as opções de montagem precisam ser especificadas no objeto *PersistentVolume*. Para obter mais informações sobre como criar estaticamente um volume persistente, consulte [estático Volumes persistentes][pv-static].

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

Se usar um cluster de versão 1.8.0 - 1.8.4, um contexto de segurança pode ser especificado com o *runAsUser* valor definido como *0*. Para obter mais informações sobre o contexto de segurança Pod, consulte [Configurar um contexto de segurança][kubernetes-security-context].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre volumes persistentes Kubernetes usando os Arquivos do Azure.

> [!div class="nextstepaction"]
> [Plugin do Kubernetes para Arquivos do Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
