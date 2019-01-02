---
title: (PRETERIDO) Monitorar o cluster Kubernetes do Azure com o DataDog
description: Monitorando o cluster Kubernetes no Serviço de Contêiner do Azure usando o DataDog
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6a682c199b40035bfd44fc5611a7d44b49f7b3ab
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995348"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(PRETERIDO) Monitorar um cluster do Serviço de Contêiner do Azure com DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

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

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalando o Agente do DataDog com um DaemonSet
DaemonSets são usados pelo Kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento.

Depois de fazer logon no DataDog, siga as [instruções do DataDog](https://app.datadoghq.com/account/settings#agent/kubernetes) para instalar agentes do DataDog no cluster usando um DaemonSet.

## <a name="conclusion"></a>Conclusão
É isso! Quando seus agentes estiverem em execução, você deverá ver os dados no console em alguns minutos. Visite o [painel kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) integrado para ver um resumo do cluster.
