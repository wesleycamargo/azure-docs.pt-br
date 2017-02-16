---
title: "Monitorar cluster do Azure Kubernetes - Gerenciamento de Operações | Microsoft Docs"
description: "Monitoramento do cluster Kubernetes no serviço de contêiner do Azure usando o Microsoft Operations Management Suite"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 2a124c42e6c90e9443475e1f46cf3e10b5d53d6a


---

# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Monitorar um cluster do Serviço de Contêiner do Azure com o Microsoft Operations Management Suite (OMS)

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo presume que você tenha [criado um cluster Kubernetes usando o Serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Isso também pressupõe que você tenha as ferramentas `az` cli e `kubectl` do Azure instaladas.

Você pode testar se tem a ferramenta `az` instalada executando:

```console
$ az --version
```

Se você não tem a ferramenta `az` instalada, há instruções [aqui](https://github.com/azure/azure-cli#installation).

Você pode testar se tem a ferramenta `kubectl` instalada executando:

```console
$ kubectl version
```

Se não tem `kubectl` instalado, você pode executar:

```console
$ az acs kubernetes install-cli
```

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Contêineres de monitoramentos no Operations Management Suite (OMS)

O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e em nuvem. A Solução de Contêiner é uma solução do OMS Log Analytics, que ajuda você a exibir o inventário, o desempenho e os logs de contêineres em um único local. É possível realizar auditoria e solucionar problemas de contêineres vendo os logs em um local centralizado, e localizar contêineres com ruídos e excesso de consumo em um host.

![](media/container-service-monitoring-oms/image1.png)

Para saber mais sobre a Solução de Contêiner, veja [Solução de Contêiner do Log Analytics](../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Instalando o OMS em Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obter sua ID do espaço de trabalho e a chave
Para o OMS agente para conversar com o serviço deve ser configurado com uma id do espaço de trabalho e uma chave do espaço de trabalho. Para obter a id do espaço de trabalho e você precisa criar uma conta do OMS no <https://mms.microsoft.com>.
Execute as etapas a seguir para criar uma conta. Depois de criar a conta, você precisará obter sua id e chave clicando em **Configurações** e em **Fontes Conectadas**; depois em **Servidores Linux**, conforme mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Instalar o agente OMS usando um DaemonSet
DaemonSets são usados pelo Kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento.

Aqui está o arquivo DaemonSet YAML. Salve-o em um arquivo chamado `oms-daemonset.yaml` e substitua os valores de espaço reservado de `WSID` e `KEY` abaixo com sua id de espaço de trabalho e a chave do arquivo.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: omsagent
spec:
  template:
    metadata:
      labels:
        app: omsagent
    spec:
      containers:
      - env:
        - name: WSID
          value: <your workspace ID>
        - name: KEY
          value: <your key>
        image: microsoft/oms
        name: omsagent
        ports:
        - containerPort: 25225
          protocol: TCP
        securityContext:
          privileged: true
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
```

Depois de adicionar a ID e a chave para a configuração de DaemonSet, você pode instalar o agente do OMS no cluster com o `kubectl` ferramenta de linha de comando:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="conclusion"></a>Conclusão
É isso! Depois de alguns minutos, você deve ser capaz de ver os dados que fluem para o painel do OMS.



<!--HONumber=Jan17_HO4-->


