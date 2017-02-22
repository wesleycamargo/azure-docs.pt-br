---
title: Como escalar o Azure Functions | Microsoft Docs
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
ms.date: 08/03/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 1a8e8bdb95b1eff6feb992727d28051463710813
ms.openlocfilehash: fce766358c61494e77464a4506dcc0e466e4e1e4


---
# <a name="scaling-azure-functions"></a>Dimensionamento do Azure Functions

## <a name="introduction"></a>Introdução

A plataforma Azure Functions aloca potência de computação quando seu código está em execução, escala horizontalmente conforme a necessidade para tratar da carga e reduz horizontalmente quando o código não está em execução. Isso significa que você não paga por VMs ociosas nem precisa reservar capacidade antes de precisar. O mecanismo para esse recurso é o Plano do serviço de consumo. Este artigo fornece uma visão geral de como o Plano do serviço de consumo funciona. 

Se ainda não estiver familiarizado com o Azure Functions, veja o artigo [Visão geral do Azure Functions](functions-overview.md).

## <a name="choose-a-service-plan"></a>Escolher um plano de serviço

Ao criar um aplicativo de funções, você deve configurar um plano de hospedagem para funções contidas no aplicativo. Os planos de hospedagem disponíveis são: o **Plano de Consumo** e o [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Atualmente, essa opção deve ser feita durante a criação do aplicativo de funções. Você não pode alterar entre essas duas opções após a criação. É possível escalar entre camadas no [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). No momento, não é permitido fazer alterações no Plano de consumo, uma vez que o escalonamento é dinâmico.

### <a name="consumption-plan"></a>Plano de consumo

No **Plano de consumo**, seus Aplicativos de Funções são atribuídos a uma instância de processamento de computação. Se necessário, mais instâncias são adicionadas ou removidas dinamicamente. Além disso, as funções são executadas em paralelo, minimizando o tempo total necessário para processar solicitações. O tempo de execução para cada função é agregado pelo Aplicativo de Funções que a contém. O custo é orientado pelo tamanho da memória e tempo de execução total em todas as funções em um Aplicativo de Funções, conforme medido em gigabyte-segundos. Essa é uma excelente opção se suas necessidades de computação são intermitentes ou os horários de trabalho tendem a ser muito curtos, pois permite que você pague pelos recursos de computação apenas quando estão realmente em uso. 

### <a name="app-service-plan"></a>Plano do serviço de aplicativo

No **Plano do Serviço de Aplicativo**, seus Aplicativos de Funções são executados em VMs dedicadas, assim como os Aplicativos Web funcionam hoje para SKUs Basic, Standard ou Premium. Essas VMs dedicadas são alocadas para os aplicativos do Serviço de Aplicativo e Aplicativos de Funções, além de estarem sempre disponíveis, não importando se o código está sendo ativamente executado ou não. Essa será uma boa opção se você tiver VMs subutilizadas que já estão executando outro código, ou se pretende executar funções continuamente ou quase continuamente. Uma VM separa o custo do tempo de execução e do tamanho da memória. Consequentemente, você pode limitar o custo de muitas funções de execução longa para o custo de uma ou mais VMs nas quais elas são executadas.

## <a name="consumption-service-plan"></a>Plano do serviço de consumo

O Plano do serviço de consumo escala automaticamente recursos de CPU e memória adicionando mais instâncias de processamento com base nas necessidades de tempo de execução das funções no Aplicativo de Funções. Cada instância de processamento do Aplicativo de Funções pode alocar até 1,5 GB de recursos de memória.

### <a name="runtime-scaling"></a>Escalonamento de tempo de execução

A plataforma Azure Functions usa um ouvinte central para avaliar as necessidades de computação com base nos gatilhos configurados e para decidir quando escalar e reduzir horizontalmente. O ouvinte central processa dicas constantemente para requisitos de memória e pontos de dados específicos de gatilho. Por exemplo, no caso de um gatilho de Armazenamento de Filas do Azure, os pontos de dados incluem comprimento da fila e tempo de fila para a entrada mais antiga.

![](./media/functions-scale/central-listener.png)

A unidade de escalonamento é o Aplicativo de Funções. Escalar horizontalmente, nesse caso, significa adicionar mais instâncias de um Aplicativo de Funções. De modo inverso, conforme a demanda de computação é reduzida, as instâncias do Aplicativo de Funções são removidas; por fim, reduzidas horizontalmente para zero quando não houver mais código em execução. 

### <a name="billing-model"></a>Modelo de cobrança

A cobrança do Plano do serviço de consumo é descrita detalhadamente na [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions). O uso é relatado por Aplicativo de Funções, somente pelo tempo que o código está sendo executado. Veja a seguir as unidades de cobrança: 
* **Consumo de recursos em GB-s (gigabyte-segundos)** calculado como uma combinação do tamanho da memória e tempo de execução para todas as funções em execução em um Aplicativo de Funções. 
* **Execuções** contadas toda vez que uma função é executada em resposta a um evento, disparado por uma associação.



<!--HONumber=Nov16_HO4-->


