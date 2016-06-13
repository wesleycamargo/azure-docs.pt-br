<properties
   pageTitle="Como escalar os Azure Functions | Microsoft Azure"
   description="Entenda como os Azure Functions escalam para atender às necessidades das cargas de trabalho orientadas por eventos."
   services="functions"
   documentationCenter="na"
   authors="eduardolaureano"
   manager="erikre"
   editor=""
   tags=""
   keywords="funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/09/2016"
   ms.author="edlaure"/>

# Como dimensionar os Azure Functions

## Introdução

Uma vantagem do Azure Functions é que os recursos de computação são consumidos apenas quando necessário. Isso significa que você não paga por VMs ociosas nem precisa reservar capacidade quando precisar dela. Em vez disso, a plataforma aloca a capacidade de computação quando seu código está em execução, escala verticalmente conforme necessário para lidar com a carga e reduz verticalmente quando código não está mais em execução.

O mecanismo para essa nova funcionalidade é o plano de Serviço Dinâmico.

Este artigo fornece uma visão geral de como o plano de Serviço Dinâmico funciona e como a plataforma pode ser escalada sob demanda para executar seu código.

Se você ainda não estiver familiarizado com o Azure Functions, confira o artigo [Visão geral do Azure Functions](functions-overview.md) para entender melhor seus recursos.

## Configurar o Azure Functions

Há duas configurações principais relacionadas à escala:

* [Plano do Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ou plano de Serviço Dinâmico
* Tamanho da memória para o ambiente de execução

O custo de uma função muda dependendo do plano de serviço que você seleciona. Com um plano de Serviço Dinâmico, o custo é uma função do tempo de execução, do tamanho da memória e do número de execuções. Cobranças se acumulam apenas quando seu código está realmente em execução.

Um Plano do Serviço de Aplicativo hospeda suas funções em VMs existentes, que também podem ser usadas para executar outro código. Depois de pagar por essas VMs cada mês, não há custos adicionais para a execução de funções nelas.

## Escolher um plano de serviço

Ao criar funções, você pode optar por executá-las em um plano de Serviço Dinâmico ou em um [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). No Plano do Serviço de Aplicativo, as funções serão executadas em uma VM dedicada, da mesma forma que aplicativos Web funcionam hoje em dia para SKUs Basic, Standard ou Premium. Essa VM dedicada é alocada para os aplicativos e funções, além de estar sempre disponível, não importando se o código está sendo ativamente executado ou não. Essa será uma boa opção se você tiver VMs subutilizadas que já estão executando outro código, ou se pretende executar funções continuamente ou quase continuamente. Uma VM separa o custo do tempo de execução e do tamanho da memória. Consequentemente, você pode limitar o custo de muitas funções de execução longa para o custo de uma ou mais VMs nas quais elas são executadas.

[AZURE.INCLUDE [Plano de Serviço Dinâmico](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0601_2016-->