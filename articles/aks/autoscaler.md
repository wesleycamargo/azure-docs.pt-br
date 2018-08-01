---
title: Kubernetes no Azure – dimensionador automático de cluster
description: Saiba como usar o dimensionador automático de cluster com o AKS (serviço de Kubernetes do Azure) para dimensionar automaticamente o cluster para atender à demanda.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186706"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Dimensionador automático de cluster no AKS (serviço de Kubernetes do Azure) – versão prévia

O AKS (serviço de Kubernetes do Azure) fornece uma solução flexível para implantar um cluster Kubernetes gerenciado no Azure. Conforme as demandas por recursos aumentam, o dimensionador automático do cluster permite que seu cluster aumente para atender a essa demanda com base nas restrições que você define. O AC (dimensionador automático de cluster) faz isso colocando em escala seus nós de agente com base em pods pendentes. Ele examina o cluster periodicamente para verificar se há pods pendentes ou nós vazios e aumenta o tamanho, se possível. Por padrão, o AC examina se há pods pendentes a cada 10 segundos e remove um nó se ele for desnecessário por mais de 10 minutos. Quando usado com o HPA (dimensionador automático de pod horizontal), o HPA atualizará as réplicas e os recursos de pod conforme a demanda. Se não houver nós suficientes ou se houver nós desnecessários seguindo essa colocação em escala do pod, o AC responderá e agendará os pods no novo conjunto de nós.

> [!IMPORTANT]
> Integração do dimensionador automático de cluster do AKS (serviço de Kubernetes do Azure) está atualmente em **versão prévia**. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>

## <a name="prerequisites"></a>Pré-requisitos

Este documento presume que você tenha um cluster do AKS habilitado para RBAC. Se você precisar de um cluster AKS, consulte o [início rápido do AKS (Serviço de Kubernetes do Azure)][aks-quick-start].

 Para usar o dimensionador automático de cluster, o cluster deve estar usando o Kubernetes v1.10.X ou superior e estar habilitado para RBAC. Para atualizar seu cluster, consulte o artigo sobre [como atualizar um cluster do AKS][aks-upgrade].

## <a name="gather-information"></a>Coletar informações

A lista a seguir mostra todas as informações que você deve fornecer na definição do dimensionador automático.

- *ID da Assinatura*: a ID correspondente à assinatura usada para este cluster
- *Nome do Grupo de Recursos*: o nome do grupo de recursos ao qual o cluster pertence 
- *Nome do Cluster*: nome do cluster
- *ID do cliente*: a ID do aplicativo concedida pela etapa de geração de permissão
- *Segredo do Cliente*: segredo do aplicativo concedido pela etapa de geração de permissão
- *ID do Locatário*: ID do locatário (proprietário da conta)
- *Grupo de Recursos do Nó*: nome do grupo de recursos que contém os nós de agente no cluster
- *Nome do Pool de Nós*: nome do pool de nós que você gostaria de escalar
- *Número Mínimo de Nós*: número mínimo de nós que deve existir no cluster
- *Número Máximo de Nós*: número máximo de nós que deve existir no cluster
- *Tipo de VM*: serviço usado para gerar o cluster de Kubernetes

Obtenha sua ID de assinatura com: 

``` azurecli
az account show --query id
```

Gere um conjunto de credenciais do Azure executando o seguinte comando:

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

A ID do Aplicativo, a Senha e a ID de Locatário será seu clientID, clientSecret e tenantID nas etapas a seguir.

Obtenha o nome do seu pool de nós executando o comando a seguir. 

```console
$ kubectl get nodes --show-labels
```

Saída:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Em seguida, extraia o valor da etiqueta **agentpool**. O nome padrão para o pool de nós de um cluster é "nodepool1".

Para obter o nome do seu grupo de recursos do nó, extraia o valor da etiqueta **kubernetes.azure.com<span></span>/cluster**. O nome do grupo de recursos do nó geralmente está na forma MC_ [grupo de recursos]\_[nome do cluster] _ [local].

O parâmetro vmType refere-se ao serviço que está sendo usado, que, aqui, é AKS.

Agora, você deve ter as seguintes informações:

- SubscriptionID
- ResourceGroup
- ClusterName
- ClientID
- ClientSecret
- TenantID
- NodeResourceGroup
- VMType

Em seguida, codifique todos esses valores com base64. Por exemplo, para codificar o valor de VMType com base64:

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>Criar um segredo
Usando esses dados, crie um segredo para a implantação usando os valores encontrados nas etapas anteriores no seguinte formato:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

## <a name="create-a-deployment-chart"></a>Criar um gráfico de implantação

Crie um arquivo chamado `aks-cluster-autoscaler.yaml` e copie-o para o código YAML a seguir.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

Copie e cole o segredo criado na etapa anterior e insira-o no início do arquivo.

Em seguida, para definir o intervalo de nós, preencha o argumento para `--nodes` em `command` na forma MIN:MAX:NODE_POOL_NAME. Por exemplo: `--nodes=3:10:nodepool1` define o número mínimo de nós como 3, o número máximo de nós como 10 e o nome do pool de nós como nodepool1.

Em seguida, preencha o campo de imagem em **contêineres** com a versão do dimensionador automático de cluster que você deseja usar. AKS requer v1.2.2 ou mais recente. O exemplo aqui usa v1.2.2.

## <a name="deployment"></a>Implantação

Implante o dimensionador automático de cluster executando

```console
kubectl create -f cluster-autoscaler-containerservice.yaml
```

Para verificar se o dimensionador automático de cluster está em execução, use o comando a seguir e verifique a lista de pods. Se há um pod prefixado com "cluster-autoscaler" em execução, o dimensionador automático de cluster foi implantado.

```console
kubectl -n kube-system get pods
```

Para exibir o status do dimensionador automático de cluster, execute

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="next-steps"></a>Próximas etapas

Para usar o dimensionador automático de cluster com o dimensionador automático de pod horizontal, veja [como colocar em escala aplicativo e infraestrutura do Kubernetes][aks-tutorial-scale].

Saiba mais sobre como implantar e gerenciar o AKS com os tutoriais do AKS.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
