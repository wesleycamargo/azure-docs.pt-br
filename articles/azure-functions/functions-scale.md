---
title: Escolher um plano de hospedagem do Azure Functions | Microsoft Docs
description: "Entenda como os Azure Functions escalam para atender às necessidades das cargas de trabalho orientadas por eventos."
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 1c740ac1f98a07b08bdf922dde99ce54bac23ee5
ms.openlocfilehash: e41e246b081efbdf5edf70ee5de86cd2a68043b2
ms.lasthandoff: 03/01/2017


---
# <a name="choose-the-correct-service-plan-for-azure-functions"></a>Escolher o plano de serviço correto para o Azure Functions

## <a name="introduction"></a>Introdução

O Azure Functions tem dois planos de serviço diferentes: o plano de Consumo e o plano do Serviço de Aplicativo. O plano de Consumo automaticamente aloca potência de computação quando seu código está em execução, escala horizontalmente conforme a necessidade para tratar da carga e reduz horizontalmente quando o código não está em execução. Isso significa que você não paga por VMs ociosas nem precisa reservar capacidade antes de precisar. Este artigo se concentra no plano de serviço de Consumo. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Se ainda não estiver familiarizado com o Azure Functions, veja o artigo [Visão geral do Azure Functions](functions-overview.md).

## <a name="service-plan-options"></a>Opções de plano de serviço

Ao criar um aplicativo de funções, você deve configurar um plano de hospedagem para funções contidas no aplicativo. Os planos de hospedagem disponíveis são: o **Plano de Consumo** e o [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Atualmente, essa opção deve ser feita durante a criação do aplicativo de funções. Você não pode alterar entre essas duas opções após a criação. É possível escalar entre camadas no [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). No momento, não é permitido fazer alterações no Plano de consumo, uma vez que o escalonamento é dinâmico.

### <a name="consumption-plan"></a>Plano de consumo

No **Plano de consumo**, seus aplicativos de funções são atribuídos a uma instância de processamento de computação. Quando necessário, mais instâncias são adicionadas ou removidas dinamicamente. Além disso, as funções são executadas em paralelo, minimizando o tempo total necessário para processar solicitações. O tempo de execução para cada função é agregado pelo aplicativo de funções que a contém. O custo é orientado pelo tamanho da memória e tempo de execução total em todas as funções em um aplicativo de funções, conforme medido em gigabyte-segundos. Essa é uma excelente opção se suas necessidades de computação são intermitentes ou os horários de trabalho tendem a ser muito curtos, pois permite que você pague pelos recursos de computação apenas quando estão realmente em uso. A próxima seção fornece detalhes sobre como funciona o plano de consumo.

### <a name="app-service-plan"></a>Plano do Serviço de Aplicativo

No **Plano do Serviço de Aplicativo**, seus aplicativos de funções são executados em VMs dedicadas, assim como os Aplicativos Web funcionam hoje para SKUs Basic, Standard ou Premium. Essas VMs dedicadas são alocadas para os aplicativos do Serviço de Aplicativo e aplicativos de funções, além de estarem sempre disponíveis, não importando se o código está sendo ativamente executado ou não. Essa será uma boa opção se você tiver VMs subutilizadas que já estão executando outro código, ou se pretende executar funções continuamente ou quase continuamente. Uma VM separa o custo do tempo de execução e do tamanho da memória. Consequentemente, você pode limitar o custo de muitas funções de execução longa para o custo das VMs nas quais elas são executadas. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

## <a name="how-the-consumption-plan-works"></a>Como funciona o plano de consumo

O plano de Consumo escala automaticamente recursos de CPU e memória adicionando mais instâncias de processamento com base nos requisitos de tempo de execução das funções em um aplicativo de funções. Cada instância de processamento do aplicativo de funções pode alocar até 1,5 GB de recursos de memória.

Durante a execução em um plano de Consumo, se uma Função de Aplicativo tiver ficado ociosa, poderá haver até 10 minutos por dia de processamento de novos blobs. Quando a Função de Aplicativo está em execução, os blobs são processados mais rapidamente. Para evitar esse atraso inicial, use um Plano do Serviço de Aplicativo regular com Always On habilitado ou use outro mecanismo para disparar o processamento de blob, como uma mensagem da fila que contém o nome do blob. 

### <a name="runtime-scaling"></a>Escalonamento de tempo de execução

O Azure Functions usa um ouvinte central para avaliar as necessidades de computação com base nos gatilhos configurados e para decidir quando escalar e reduzir horizontalmente. O ouvinte central processa dicas continuamente para requisitos de memória e pontos de dados específicos de gatilho. Por exemplo, no caso de um gatilho de Armazenamento de Filas do Azure, os pontos de dados incluem comprimento da fila e tempo de fila da entrada mais antiga.

![](./media/functions-scale/central-listener.png)

A unidade de escalonamento é o aplicativo de funções. Escalar horizontalmente, nesse caso, significa adicionar mais instâncias de um aplicativo de funções. Inversamente, quando a demanda de computação é reduzida, as instâncias do aplicativo de função são removidas. O número de instâncias é eventualmente reduzido a zero quando nenhuma estância está em execução. 

### <a name="billing-model"></a>Modelo de cobrança

A cobrança do plano de Consumo é descrita detalhadamente na [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions). O uso é relatado por aplicativo de funções, somente pelo tempo que o código está sendo executado. Veja a seguir as unidades de cobrança: 
* **Consumo de recursos em GB-s (gigabyte-segundos)** calculado como uma combinação do tamanho da memória e tempo de execução para todas as funções em execução em um Aplicativo de Funções. 
* **Execuções** contadas toda vez que uma função é executada em resposta a um evento, disparado por uma associação.

