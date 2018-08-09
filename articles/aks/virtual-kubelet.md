---
title: Executar Kubelet Virtual em um cluster AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o Virtual Kubelet com o Serviço de Kubernetes do Azure (AKS) para executar contêineres do Linux e do Windows em Instâncias de Contêiner do Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: iainfou
ms.openlocfilehash: 2730ab1d909ead0431f0dd7fd0061d3080834296
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443725"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Usar o Virtual Kubelet com o serviço de Kubernetes do Azure (AKS)

ACIs (Instâncias de Contêiner do Azure) fornecem um ambiente hospedado para execução de contêineres no Azure. Ao utilizar ACI, não é necessário gerenciar a infraestrutura de computação subjacente, o Azure lida com esse gerenciamento para você. Ao executar contêineres em ACI, você é cobrado por segundo para cada contêiner em execução.

Ao usar o provedor Kubelet Virtual para Instâncias de Contêiner do Azure, os contêineres do Linux e do Windows podem ser agendados em uma instância de contêiner, como se fosse um nó Kubernetes padrão. Essa configuração permite que você aproveite os recursos dos Kubernetes, além do valor de gerenciamento e a economia das instâncias de contêiner.

> [!NOTE]
> Kubelet Virtual é um projeto experimental de software livre e deve ser usado como tal. Para contribuir, registrar problemas e ler mais sobre kubelet virtual, confira o [projeto Kubelet Virtual do GitHub][vk-github].

Este documento detalha a configuração do Kubelet Virtual para instâncias de contêiner em um AKS.

## <a name="prerequisite"></a>Pré-requisito

Este documento presume que você tenha um cluster AKS. Se você precisar de um cluster AKS, consulte o [início rápido do AKS (Serviço de Kubernetes do Azure)][aks-quick-start].

Você também precisa da CLI do Azure versão **2.0.33** ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para instalar o Virtual Kubelet, [Helm](https://docs.helm.sh/using_helm/#installing-helm) também é necessário.

### <a name="for-rbac-enabled-clusters"></a>Para clusters habilitados para RBAC

Se o cluster do AKS for habilitado para o RBAC, você deverá criar uma conta de serviço e a associação de função para uso com o Tiller. Para saber mais, confira [Controle de acesso baseado em função do Helm][helm-rbac].

Um *ClusterRoleBinding* também deve ser criado para o Virtual Kubelet. Para criar uma associação, crie um arquivo chamado *rbac-virtualkubelet.yaml* e cole a seguinte definição:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: virtual-kubelet
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
```

Aplique a associação com [kubectl apply] [ kubectl-apply] e especifique o arquivo *rbac-virtualkubelet.yaml*, conforme mostrado no seguinte exemplo:

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/virtual-kubelet created
```

Agora, você pode continuar a instalar o Virtual Kubelet no cluster AKS.

## <a name="installation"></a>Instalação

Use o comando [az aks install-conector][aks-install-connector] para instalar o Kubelet Virtual. O exemplo a seguir implanta o conector do Linux e do Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Esses argumentos estão disponíveis para o comando `aks install-connector`.

| Argumento: | DESCRIÇÃO | Obrigatório |
|---|---|:---:|
| `--connector-name` | Nome do conector ACI.| SIM |
| `--name` `-n` | Nome do cluster gerenciado. | SIM |
| `--resource-group` `-g` | Nome do grupo de recursos. | SIM |
| `--os-type` | Tipo de sistema operacional de instâncias de contêiner. Valores permitidos: Linux, Windows ou ambos. Padrão: Linux. | Não  |
| `--aci-resource-group` | O grupo de recursos no qual criar os grupos de contêiner do ACI. | Não  |
| `--location` `-l` | O local para criar os grupos de contêiner do ACI. | Não  |
| `--service-principal` | A entidade de serviço usada para autenticação em APIs do Azure. | Não  |
| `--client-secret` | Segredo associado à entidade de serviço. | Não  |
| `--chart-url` | URL de um gráfico Helm que instala o conector ACI. | Não  |
| `--image-tag` | A marca de imagem da imagem de contêiner do kubelet virtual. | Não  |

## <a name="validate-virtual-kubelet"></a>Validar Kubelet Virtual

Para validar a instalação do Kubelet Virtual, retorne uma lista de nós Kubernetes usando o comando [kubectl get nodes][kubectl-get].

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Executar contêiner do Linux

Crie um arquivo chamado `virtual-kubelet-linux.yaml` e copie no YAML a seguir. Substitua o valor `kubernetes.io/hostname` pelo nome do nó de Kubelet Virtual do Linux. Observe que [nodeSelector][node-selector] e [toleration][toleration] estão sendo usados para agendar o contêiner no nó.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Execute o aplicativo com o comando [kubectl create][kubectl-create].

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Use o comando [kubectl get pods][kubectl-get] com o argumento `-o wide` para gerar uma lista dos pods com o nó agendado. Observe que o pod `aci-helloworld` foi agendado no nó `virtual-kubelet-virtual-kubelet-linux`.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Executar contêiner do Windows

Crie um arquivo chamado `virtual-kubelet-windows.yaml` e copie no YAML a seguir. Substitua o valor `kubernetes.io/hostname` pelo nome do nó de Kubelet Virtual do Windows. Observe que [nodeSelector][node-selector] e [toleration][toleration] estão sendo usados para agendar o contêiner no nó.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Execute o aplicativo com o comando [kubectl create][kubectl-create].

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Use o comando [kubectl get pods][kubectl-get] com o argumento `-o wide` para gerar uma lista dos pods com o nó agendado. Observe que o pod `nanoserver-iis` foi agendado no nó `virtual-kubelet-virtual-kubelet-win`.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Remover Kubelet Virtual

Use o comando [az aks remove-conector][aks-remove-connector] para remover o Kubelet Virtual. Substitua os valores de argumento pelo nome do conector, o cluster AKS e o grupo de recursos do cluster AKS.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre o Virtual Kubelet no [projeto Virtual Kubelet do Github][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
