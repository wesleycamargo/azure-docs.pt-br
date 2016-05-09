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

Uma das vantagens dos Azure Functions é que os recursos são consumidos somente conforme exigido pelo seu código em execução. Isso significa que você não paga por VMs ociosas nem precisa reservar capacidade para quando precisar. Em vez disso, a plataforma aloca a capacidade de computação quando seu código está em execução, escalando verticalmente conforme necessário para lidar com a carga e reduzindo novamente quando código não estiver mais em execução.

O mecanismo para essa nova funcionalidade é o Plano de Serviço Dinâmico. Este novo plano de serviço fornece um contêiner dinâmico para o código que escala verticalmente sob demanda, enquanto você é cobrado apenas pela quantidade de memória que seu código usar e o tempo que leva para ele ser executado, medido em Gigabytes por segundo.

Este artigo fornece uma visão geral de como p Plano de Serviço Dinâmico funciona e como a plataforma pode ser dimensionada sob demanda para executar seu código.

Se você ainda não estiver familiarizado com os Azure Functions, confira a [Visão geral dos Azure Functions](functions-overview.md) para entender melhor seus recursos.

## Configurar seu aplicativo de função

Há duas configurações principais relacionadas ao dimensionamento:

* [Plano de Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ou Plano de Serviço Dinâmico 
* Tamanho da memória para o ambiente de execução 

O custo de uma função muda dependendo do tipo de plano de serviço que você selecionar. Com Planos de serviço dinâmico, o custo é uma função do tempo de execução, tamanho da memória e o número de execuções. Mudanças contam apenas quando você está realmente executando o código.

Os planos de serviço regulares permitem hospedar suas funções em VMs existentes que também podem ser usadas para executar outro código. Depois de pagar por essas VMs cada mês, não há custos adicionais para a execução de funções nelas.

## Escolhendo um plano de serviço

Ao criar aplicativos de funções, você pode optar por executá-los em um Plano de Serviço Dinâmico (novo!) ou em um [Plano de Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) regular. No Plano de Serviço de Aplicativo, suas funções serão executadas em uma VM dedicada, da mesma forma que aplicativos Web funcionam hoje em dia (para SKUs Basic, Standard ou Premium). Essa VM dedicada é alocada para seus aplicativos e/ou funções e está disponível independentemente de qualquer código sendo executado ativamente. Essa será uma boa opção se você tiver VMs existentes que já estão executando outro código, mas que não são totalmente utilizadas, ou se você pretender executar funções de forma contínua ou quase contínua. Usar uma VM desvincula o custo do tempo de execução e do tamanho da memória, permitindo que você limite o custo de um grande número de funções de longo tempo de execução ao custo de uma ou mais VMs nas quais eles são executados.

[AZURE.INCLUDE [Plano de serviço Dinâmico](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0427_2016-->