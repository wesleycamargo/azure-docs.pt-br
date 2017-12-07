---
title: Tutorial do Kubernetes no Azure - Monitorar o Kubernetes
description: "Tutorial do AKS– monitorar o Kubernetes com o OMS (Microsoft Operations Management Suite)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 084c6bf3855bdc757c3f2926b35eaf7bba0ef389
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-azure-container-service-aks"></a>Monitorar o AKS (Serviço de Contêiner do Azure)

Monitorar seu cluster do Kubernetes e os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

Neste tutorial, você configura o monitoramento do seu cluster AKS usando a [Solução de contêineres para Log Analytics](../log-analytics/log-analytics-containers.md).

Este tutorial, parte sete de oito, aborda as seguintes tarefas:

> [!div class="checklist"]
> * Configuração da solução de monitoramento de contêiner
> * Configuração dos agentes de monitoramento
> * Acessar informações de monitoramento no portal do Azure

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em um cluster de contêiner, essas imagens foram carregadas no Registro de Contêiner do Azure e um cluster do Kubernetes foi criado.

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./tutorial-kubernetes-prepare-app.md).

## <a name="configure-the-monitoring-solution"></a>Configurar a solução de monitoramento

No portal do Azure, selecione **Novo** e procure `Container Monitoring Solution`. Depois de localizá-lo, selecione **Criar**.

![Adicionar solução](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Crie um novo espaço de trabalho do OMS ou selecione um existente. O formulário Espaço de Trabalho do OMS o orientará durante este processo.

Ao criar o espaço de trabalho, selecione **Fixar no Painel** para recuperação fácil.

![Espaço de Trabalho do OMS](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Ao terminar, selecione **OK**. Após a conclusão da validação, selecione **Criar** para criar a solução de monitoramento de contêiner.

Quando o espaço de trabalho tiver sido criado, ele será apresentado a você no portal do Azure.

## <a name="get-workspace-settings"></a>Obter configurações de espaço de trabalho

A ID e a Chave do Espaço de Trabalho do Log Analytics são necessárias para configurar o agente de solução em nós Kubernetes.

Para recuperar esses valores, selecione **Espaço de Trabalho do OMS** no menu esquerdo de soluções de contêiner. Selecione **Configurações avançadas** e anote a **ID DO ESPAÇO DE TRABALHO** e a **CHAVE PRIMÁRIA**.

## <a name="configure-monitoring-agents"></a>Configurar agentes de monitoramento

O arquivo de manifesto Kubernetes a seguir pode ser usado para configurar os agentes de monitoramento de contêiner em um cluster Kubernetes. Ele cria um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) do Kubernetes, que executa um único pod em cada nó de cluster.

Salvar o texto a seguir em um arquivo chamado `oms-daemonset.yaml`e substitua os valores de espaço reservado para `WSID` e `KEY` com a ID do espaço de trabalho de Log Analytics e a chave.

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
        - mountPath: /var/log
          name: host-log
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
    - name: container-hostname
      hostPath:
       path: /etc/hostname
    - name: host-log
      hostPath:
       path: /var/log
```

Crie o DaemonSet com o seguinte comando:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Para ver se o DaemonSet foi criado, execute:

```azurecli-interactive
kubectl get daemonset
```

A saída deverá ser semelhante a esta:

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Depois que os agentes estiverem em execução, levará vários minutos para que o OMS ingira e processe os dados.

## <a name="access-monitoring-data"></a>Acessar dados de monitoramento

No portal do Azure, selecione o espaço de trabalho do Log Analytics que tenha sido fixado no painel do portal. Clique no bloco **Solução de Monitoramento de Contêiner**. Aqui você pode encontrar informações sobre o cluster AKS e contêineres do cluster.

![Painel](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Consulte a [documentação do Azure Log Analytics](../log-analytics/index.yml) para obter orientações detalhadas de como consultar e analisar dados de monitoramento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você monitorou o cluster do Kubernetes com o OMS. As tarefas abordadas incluíram:

> [!div class="checklist"]
> * Configuração da solução de monitoramento de contêiner
> * Configuração dos agentes de monitoramento
> * Acessar informações de monitoramento no portal do Azure

Avance para o próximo tutorial para saber mais sobre como fazer upgrade do Kubernetes para uma nova versão.

> [!div class="nextstepaction"]
> [Atualizar o Kubernetes](./tutorial-kubernetes-upgrade-cluster.md)