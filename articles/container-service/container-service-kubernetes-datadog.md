---
title: Monitorar o cluster Azure Kubernetes com o DataDog| Microsoft Docs
description: "Monitoramento do cluster Kubernetes no Serviço de Contêiner do Azure usando DataDog"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: what-goes-here?
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 2984a3811eba670b54fd253a8d7dc84acd8c8ec4


---

# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Monitorar um cluster do Serviço de Contêiner do Azure com o Datadog

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

## <a name="datadog"></a>DataDog
O Datadog é um serviço de monitoramento que reúne dados de monitoramento de seus contêineres em seu cluster do Serviço de Contêiner do Azure. O Datadog tem um Painel de Integração do Docker, onde você pode ver as métricas específicas em seus contêineres. As métricas obtidas de seus contêineres são organizadas por CPU, Memória, Rede e E/S. O Datadog divide as métricas em contêineres e em imagens.

Primeiro você precisa [criar uma conta](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalação do Agente DataDog com um DaemonSet
DaemonSets são usados pelo Kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento.

Depois de fazer logon no DataDog, siga as [instruções do DataDog](https://app.datadoghq.com/account/settings#agent/kubernetes) para instalar agentes de DataDog em seu cluster usando um DaemonSet.

## <a name="conclusion"></a>Conclusão
É isso! Quando seus agentes estiverem em execução, você deverá ver os dados no console em alguns minutos. Visite o [painel kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) integrado para ver um resumo do cluster.


<!--HONumber=Jan17_HO4-->


