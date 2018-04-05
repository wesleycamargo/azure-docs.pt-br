---
title: Tutorial do Kubernetes no Azure - Monitorar o Kubernetes
description: Tutorial do AKS – monitorar o Kubernetes com o Azure Log Analytics
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 86ae0c5ab302c49fa58df887d9dffef6cec31708
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-monitor-azure-container-service-aks"></a>Tutorial: monitorar o Serviço de Contêiner do Azure (AKS)

Monitorar seu cluster do Kubernetes e os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

Neste tutorial, você configura o monitoramento do seu cluster AKS usando a [Solução de contêineres para Log Analytics][log-analytics-containers].

Este tutorial, parte sete de oito, aborda as seguintes tarefas:

> [!div class="checklist"]
> * Configuração da solução de monitoramento de contêiner
> * Configuração dos agentes de monitoramento
> * Acessar informações de monitoramento no portal do Azure

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em um cluster de contêiner, essas imagens foram carregadas no Registro de Contêiner do Azure e um cluster do Kubernetes foi criado.

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

## <a name="configure-the-monitoring-solution"></a>Configurar a solução de monitoramento

No portal do Azure, selecione **Criar um recurso** e procure `Container Monitoring Solution`. Depois de localizá-lo, selecione **Criar**.

![Adicionar solução](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Crie um novo espaço de trabalho do Log Analytics ou selecione um existente. O formulário de Espaço de trabalho do Log Analytics o orienta durante este processo.

Ao criar o espaço de trabalho, selecione **Fixar no Painel** para recuperação fácil.

![Espaço de trabalho do Log Analytics](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Ao terminar, selecione **OK**. Após a conclusão da validação, selecione **Criar** para criar a solução de monitoramento de contêiner.

Quando o espaço de trabalho tiver sido criado, ele será apresentado a você no portal do Azure.

## <a name="get-workspace-settings"></a>Obter configurações de espaço de trabalho

A ID e a Chave do Espaço de Trabalho do Log Analytics são necessárias para configurar o agente de solução em nós Kubernetes.

Para recuperar esses valores, selecione **Espaço de Trabalho do OMS** no menu esquerdo de soluções de contêiner. Selecione **Configurações avançadas** e anote a **ID DO ESPAÇO DE TRABALHO** e a **CHAVE PRIMÁRIA**.

## <a name="create-kubernetes-secret"></a>Criar segredo Kubernetes

Armazene as configurações de espaço de trabalho do Log Analytics em um segredo Kubernetes chamado `omsagent-secret` usando o comando [kubectl create secret][kubectl-create-secret]. Atualize `WORKSPACE_ID` com sua ID de espaço de trabalho do Log Analytics e `WORKSPACE_KEY` com a chave do espaço de trabalho.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="configure-monitoring-agents"></a>Configurar agentes de monitoramento

O arquivo de manifesto Kubernetes a seguir pode ser usado para configurar os agentes de monitoramento de contêiner em um cluster Kubernetes. Ele cria um [DaemonSet][kubernetes-daemonset] do Kubernetes, que executa um único pod em cada nó de cluster.

Salve o texto a seguir em um arquivo chamado `oms-daemonset.yaml`.

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers 
          name: containerlog-path  
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux    
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"     
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log 
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers    
```

Crie o DaemonSet com o seguinte comando:

```azurecli
kubectl create -f oms-daemonset.yaml
```

Para ver se o DaemonSet foi criado, execute:

```azurecli
kubectl get daemonset
```

A saída deverá ser semelhante a esta:

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Depois que os agentes estiverem em execução, levará vários minutos para que o Log Analytics ingira e processe os dados.

## <a name="access-monitoring-data"></a>Acessar dados de monitoramento

No portal do Azure, selecione o espaço de trabalho do Log Analytics que tenha sido fixado no painel do portal. Clique no bloco **Solução de Monitoramento de Contêiner**. Aqui você pode encontrar informações sobre o cluster AKS e contêineres do cluster.

![painel](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Consulte a [documentação do Azure Log Analytics][log-analytics-docs] para obter orientações detalhadas de como consultar e analisar dados de monitoramento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você monitorou o cluster Kubernetes com o Log Analytics. As tarefas abordadas incluíram:

> [!div class="checklist"]
> * Configuração da solução de monitoramento de contêiner
> * Configuração dos agentes de monitoramento
> * Acessar informações de monitoramento no portal do Azure

Avance para o próximo tutorial para saber mais sobre como fazer upgrade do Kubernetes para uma nova versão.

> [!div class="nextstepaction"]
> [Atualizar o Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubectl-create-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml
