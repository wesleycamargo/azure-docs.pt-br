---
title: Funções do Azure no Kubernetes com KEDA
description: Entender como executar funções do Azure no Kubernetes na nuvem ou local usando KEDA, dimensionamento automático do controlado por evento com base em Kubernetes.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077614"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funções do Azure no Kubernetes com KEDA

O tempo de execução do Azure Functions fornece flexibilidade na hospedagem onde e como você deseja.  [KEDA](https://github.com/kedacore/kore) (baseado no Kubernetes evento controlado por dimensionamento automático) pares perfeitamente com o tempo de execução do Azure Functions e ferramentas para fornecer escala controlada por eventos no Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Como Kubernetes-com base em funções de trabalho

O serviço do Azure Functions é composto por dois componentes principais: um tempo de execução e um controlador de escala.  O tempo de execução de funções é executado e executa seu código.  O tempo de execução inclui lógica sobre como disparar, registrar e gerenciar execuções de função.  O outro componente é um controlador de escala.  O controlador de escala monitora a taxa de eventos que são direcionados a sua função e dimensiona proativamente o número de instâncias que executam seu aplicativo.  Para obter mais informações, consulte [escala e hospedagem do Azure Functions](functions-scale.md).

Funções com base em Kubernetes fornece o tempo de execução de funções em um [contêiner do Docker](functions-create-function-linux-custom-image.md) com controlada por evento o dimensionamento por meio de KEDA.  KEDA poderá reduzir verticalmente às instâncias de 0 (quando não há eventos estiverem ocorrendo) e até *n* instâncias. Ele faz isso expondo métricas personalizadas para o dimensionador automático de Kubernetes (Horizontal Pod dimensionador automático).  Usando contêineres de funções com KEDA torna possível replicar as capacidades de função sem servidor em qualquer cluster Kubernetes.  Essas funções também podem ser implantadas usando [os nós virtuais dos serviços de Kubernetes do Azure (AKS)](../aks/virtual-nodes-cli.md) recurso de infraestrutura sem servidor.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gerenciando KEDA e funções no Kubernetes

Para executar funções em seu cluster Kubernetes, você deve instalar o componente KEDA. Você pode instalar esse componente usando [as ferramentas básicas do Azure Functions](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Instalar com o Azure Functions ferramentas básicas

Por padrão, ferramentas Core instala componentes KEDA e Osiris, que oferece suporte a controlada por evento e a escala de HTTP, respectivamente.  A instalação usa `kubectl` em execução no contexto atual.

Instale KEDA em seu cluster executando o comando a seguir:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Implantando um aplicativo de funções no Kubernetes

Você pode implantar qualquer aplicativo de funções para um cluster de Kubernetes executando KEDA.  Uma vez que suas funções são executadas em um contêiner do Docker, o projeto precisar de um `Dockerfile`.  Se ele ainda não tiver um, você pode adicionar um Dockerfile, executando o seguinte comando na raiz do seu projeto de funções:

```cli
func init --docker-only
```

Para criar uma imagem e implantar suas funções no Kubernetes, execute o seguinte comando:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Substitua `<name-of-function-deployment>` pelo nome do aplicativo de funções.

Isso cria um Kubernetes `Deployment` recurso, uma `ScaledObject` recurso, e `Secrets`, que inclui variáveis de ambiente importadas de sua `local.settings.json` arquivo.

## <a name="removing-a-function-app-from-kubernetes"></a>Removendo um aplicativo de funções do Kubernetes

Após a implantação, você pode remover uma função, removendo associado `Deployment`, `ScaledObject`, um `Secrets` criado.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalando KEDA do Kubernetes

Você pode executar o seguinte comando de ferramentas de núcleo para remover KEDA de um cluster do Kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Gatilhos com suporte no KEDA

KEDA está atualmente em versão beta com suporte para os seguintes gatilhos de função do Azure:

* [Filas do armazenamento do Azure](functions-bindings-storage-queue.md)
* [Filas do barramento de serviço do Azure](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Criar uma função usando uma imagem personalizada](functions-create-function-linux-custom-image.md)
* [Codificar e testar o Azure Functions localmente](functions-develop-local.md)
* [Como funciona o plano de consumo de função do Azure](functions-scale.md)