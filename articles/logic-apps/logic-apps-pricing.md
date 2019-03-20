---
title: Preço e cobrança - Aplicativos Lógicos do Azure | Microsoft Docs
description: Saiba como os preços e a cobrança funcionam para Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 02/26/2019
ms.openlocfilehash: 9b5452f112c6325dafd5edbe693b90ec2a94abc0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990230"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços para os Aplicativos Lógicos do Azure

Você pode criar e executar fluxos de trabalho de integração automatizados que podem ser redimensionados na nuvem quando você usa os Aplicativos Lógicos do Azure. Aqui estão os detalhes sobre como cobrança e preços funcionam para Aplicativos Lógicos. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preço por consumo

Para novos aplicativos lógicos executados no serviço de aplicativos lógicos públicos ou "globais", você paga apenas pelo que usa. Esses aplicativos lógicos usam um plano baseado em consumo e modelo de preços. Na sua definição de aplicativo lógico, cada etapa é uma ação. As ações incluem o acionador, quaisquer etapas do fluxo de controle, ações internas e chamadas de conectores. Os aplicativos lógicos medem todas as ações que são executadas no seu aplicativo lógico.  
Para obter mais informações, consulte a [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixo

Para novos aplicativos lógicos que são executados dentro de um [ *ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), você paga um preço mensal fixo para conectores padrão e as ações internas. Um ISE fornece uma maneira de criar e executar aplicativos lógicos isolados que podem acessar recursos em uma rede virtual do Azure. 

A unidade base do ISE corrigiu capacidade, portanto, se você precisar de mais taxa de transferência, você pode [adicionar mais unidades de escala](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), durante a criação ou posteriormente. Seu ISE inclui um conector corporativo gratuito, que inclui quantas conexões você desejar. O uso de conectores Enterprise adicionais é cobrado com base no preço de consumo da empresa. 

> [!NOTE]
> O ISE está na [ *visualização pública*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte a [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Gatilhos

Os gatilhos são ações especiais que criam uma instância de aplicativo lógico quando ocorre um evento específico. Dispara ações de diferentes formas, o que afeta o modo como o aplicativo lógico é monitorado.

* **Gatilho de sondagem** – esse gatilho verifica continuamente um ponto de extremidade para mensagens que satisfaçam os critérios para a criação de uma instância de aplicativo lógico e começar o fluxo de trabalho. Mesmo quando nenhuma instância de aplicativo lógico é criada, os Aplicativos Lógicos medem cada solicitação de pesquisa como uma execução. Para especificar o intervalo de sondagem, configure o gatilho no Designer de Aplicativos Lógicos.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Gatilho de webhook** – esse disparador espera o envio de uma solicitação por parte do cliente em um ponto de extremidade específico. Cada solicitação enviada ao ponto de extremidade do webhook conta como uma execução da ação. Por exemplo, a Solicitação e o gatilho Webhook HTTP são gatilhos de webhook.

* **Gatilho de recorrência** – esse gatilho cria uma nova instância do aplicativo lógico com base no intervalo de recorrência configurado no gatilho. Por exemplo, você pode definir um gatilho de recorrência que é executado a cada três dias ou em um agendamento mais complexo.

## <a name="actions"></a>Ações

Os aplicativos lógicos medem ações internas como ações nativas. Por exemplo, as ações internas incluem chamadas por HTTP, chamadas do Azure Functions ou Gerenciamento de API e etapas de fluxo de controle, como loops e condições 
- cada um com seu próprio tipo de ação. Ações que chamam [conectores](https://docs.microsoft.com/connectors) têm o tipo de "ApiConnection". Esses conectores são classificados como conectores standard ou enterprise, que são medidos com base em seus respectivos [preços][pricing]. Conectores empresariais na *Versão Prévia* são cobrados como conectores Standard.

Os aplicativos lógicos medem todas as ações executadas com e sem sucesso como execuções de ação. Os aplicativos lógicos não medem essas ações: 

* Ações que são ignoradas devido a condições não atendidas
* Ações que não são executadas porque o aplicativo lógico parou antes de terminar

Os aplicativos lógicos desativados não são cobrados enquanto desativados porque não podem criar novas instâncias.

> [!NOTE]
> Depois de desativar um aplicativo lógico, qualquer instância em execução no momento pode demorar algum tempo antes de parar completamente.

Para ações executadas em loops internos, os aplicativos lógicos contam cada ação por ciclo no loop. Por exemplo, suponha que você tenha um loop "para cada" que processe uma lista. Os aplicativos lógicos medem uma ação nesse loop multiplicando o número de itens da lista pelo número de ações no loop e adiciona a ação que inicia o loop. O cálculo para uma lista de 10 itens é (10 * 1) + 1, o que resulta em 11 execuções de ações.

## <a name="integration-account-usage"></a>Uso da conta de integração

O uso com base no consumo se aplica a [contas de integração](logic-apps-enterprise-integration-create-integration-account.md), nas quais é possível explorar, desenvolver e testar os recursos [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e [processamento XML](logic-apps-enterprise-integration-xml.md) nos aplicativos lógicos sem custo adicional. Você pode ter uma conta de integração por região. Cada conta de integração pode armazenar até [números específicos de artefatos](../logic-apps/logic-apps-limits-and-config.md), que incluem parceiros comerciais, contratos, mapas, esquemas, montagens, certificados, configurações de lote e assim por diante.

Aplicativos lógicos também oferecem contas de integração básico e padrão com suporte à SLA de Aplicativos Lógicos. Você pode usar as contas básicas de integração quando quiser apenas o tratamento de mensagens ou atuar como um parceiro de negócios pequeno que tenha um relacionamento de parceiro comercial com uma entidade comercial maior. Contas de integração padrão suportam relacionamentos B2B mais complexos e aumentam o número de entidades que você pode gerenciar. Para saber mais, veja [Preços do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os Aplicativos Lógicos][whatis]
* [Criar seu primeiro aplicativo lógico][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

