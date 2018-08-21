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
ms.openlocfilehash: d121f2744292ba64436f0722ae60cc3bc2b8dfa7
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714121"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Dimensionador automático de cluster no AKS (serviço de Kubernetes do Azure) – versão prévia

O AKS (serviço de Kubernetes do Azure) fornece uma solução flexível para implantar um cluster Kubernetes gerenciado no Azure. Conforme as demandas por recursos aumentam, o dimensionador automático do cluster permite que seu cluster aumente para atender a essa demanda com base nas restrições que você define. O AC (dimensionador automático de cluster) faz isso colocando em escala seus nós de agente com base em pods pendentes. Ele examina o cluster periodicamente para verificar se há pods pendentes ou nós vazios e aumenta o tamanho, se possível. Por padrão, o AC examina se há pods pendentes a cada 10 segundos e remove um nó se ele for desnecessário por mais de 10 minutos. Quando usado com o autoescalador [ horizontalmente ](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA), o HPA atualizará réplicas e recursos de pod de acordo com a demanda. Se não houver nós suficientes ou nós desnecessários após esse escalonamento de cápsulas, a autoridade de certificação responderá e agendará os pods no novo conjunto de nós.

Este artigo descreve como implantar o autoescalador de cluster nos nós do agente. No entanto, desde que o dimensionador automático de cluster é implantado no namespace kube-system, o dimensionador automático não será reduzir verticalmente o nó em execução desse pod.

> [!IMPORTANT]
> Integração do dimensionador automático de cluster do AKS (serviço de Kubernetes do Azure) está atualmente em **versão prévia**. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>

## <a name="prerequisites"></a>Pré-requisitos

Este documento presume que você tenha um cluster do AKS habilitado para RBAC. Se você precisar de um cluster AKS, consulte o [início rápido do AKS (Serviço de Kubernetes do Azure)][aks-quick-start].

 Para usar o dimensionador automático de cluster, o cluster deve estar usando o Kubernetes v1.10.X ou superior e estar habilitado para RBAC. Para atualizar seu cluster, consulte o artigo sobre [como atualizar um cluster do AKS][aks-upgrade].

## <a name="gather-information"></a>Coletar informações

Para gerar as permissões para que o autoescalador de cluster seja executado em seu cluster, execute este script bash:

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

CLUSTER_INFO=`az aks show --name $cluster_name  --resource-group $resource_group -o json`
NODE_RESOURCE_GROUP=`echo $CLUSTER_INFO | sed -e 's/^.*"nodeResourceGroup"[ ]*:[ ]*"//' -e 's/".*//' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

Após seguir as etapas do script, o script mostrará seus detalhes na forma de um segredo, da seguinte forma:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

Em seguida, obtenha o nome do seu pool de nós executando o comando a seguir. 

```console
$ kubectl get nodes --show-labels
```

Saída:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Em seguida, extraia o valor da etiqueta **agentpool**. O nome padrão para o pool de nós de um cluster é "nodepool1".

Agora, usando o pool de segredo e o nó, você pode criar um gráfico de implantação.

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

Copie e cole o segredo criado na etapa anterior e inseri-lo no início do arquivo.

Em seguida, para definir o intervalo de nós, preencha o argumento para `--nodes` em `command` na forma MIN:MAX:NODE_POOL_NAME. Por exemplo: `--nodes=3:10:nodepool1` define o número mínimo de nós como 3, o número máximo de nós como 10 e o nome do pool de nós como nodepool1.

Em seguida, preencha o campo de imagem em **contêineres** com a versão do dimensionador automático de cluster que você deseja usar. AKS requer v1.2.2 ou mais recente. O exemplo aqui usa v1.2.2.

## <a name="deployment"></a>Implantação

Implante o dimensionador automático de cluster executando

```console
kubectl create -f aks-cluster-autoscaler.yaml
```

Para verificar se o dimensionador automático de cluster está em execução, use o comando a seguir e verifique a lista de pods. Deve haver um pod prefixado com "cluster-autoscaler" em execução. Se você vir isso, o autoescalador de cluster foi implantado.

```console
kubectl -n kube-system get pods
```

Para exibir o status do dimensionador automático de cluster, execute

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>Interpretando o status do escalonamento automático de cluster

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

O status do escalonamento automático de cluster permite que você veja o estado do autoescalador de cluster em dois níveis diferentes: todo o cluster e dentro de cada grupo de nós. Como o AKS atualmente suporta apenas um pool de nós, essas métricas são as mesmas.

* Integridade indica a integridade geral de nós. Se o autoescalador de cluster tiver dificuldades para criar ou remover nós no cluster, esse status será alterado para "Não íntegro". Há também um detalhamento do status de diferentes nós:
    * "Pronto" significa que um nó está pronto para ter conjuntos de pods agendados nele.
    * "Desaprovado" significa um nó que quebrou depois de iniciado.
    * "NotStarted" significa que um nó não for totalmente iniciado ainda.
    * "LongNotStarted" significa que um nó falhou ao iniciar dentro de um limite razoável.
    * "Registrado significa que um nó é registrado no grupo
    * "Não registrado" significa que um nó está presente no lado do provedor do cluster, mas não conseguiu se registrar no Kubernetes.
  
* O ScaleUp permite verificar quando o cluster determina que uma ampliação deve ocorrer em seu cluster.
    * Uma transição é quando o número de nós no cluster muda ou o status de um nó é alterado.
    * O número de nós prontos é o número de nós disponíveis e prontos no cluster. 
    * O cloudProviderTarget é o número de nós que o autoescalador de cluster determinou que o cluster precisa para lidar com sua carga de trabalho.

* O ScaleDown permite verificar se há candidatos para redução de escala. 
    * Um candidato ao scale down é um nó que o autoescalador de cluster determinou que pode ser removido sem afetar a capacidade do cluster de manipular sua carga de trabalho. 
    * Os tempos de fornecido mostram a última vez que o cluster foi verificado para candidatos de dimensionamento e seu último tempo de transição.

Por fim, em eventos, você pode ver até qualquer escala ou reduzir verticalmente a eventos, com falhas ou êxito e seus tempos, que executou o dimensionador automático de cluster.

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
