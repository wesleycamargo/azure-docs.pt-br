---
title: "Preço e cobrança - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Saiba como os preços e a cobrança funcionam para Aplicativos Lógicos do Azure."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: 49a66c826a98f7160b97b516e6fd541795ae3b0e
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="logic-apps-pricing-model"></a>Modelo de preços de Aplicativos Lógicos
Você pode criar e executar fluxos de trabalho automatizados integração escalonável na nuvem com os Aplicativos Lógicos do Azure. Aqui estão os detalhes sobre como cobrança e preços funcionam para Aplicativos Lógicos.
## <a name="consumption-pricing-model"></a>Modelo de preço por consumo
Com os novos aplicativos lógicos criados, você paga apenas pelo que usa. Novos aplicativos lógicos usam um plano e preços modelo de consumo, que significa que todas as execuções realizadas por uma instância lógica do aplicativo são medidas.
### <a name="what-are-action-executions"></a>O que são execuções de ação?
Cada etapa em uma definição de aplicativo lógico é uma ação que inclui gatilhos, etapas de fluxo de controle, como condições, escopos, loops for each e loops do until, chamadas a conectores.
### <a name="triggers"></a>Gatilhos
Os gatilhos são ações especiais que criam uma instância de aplicativo lógico quando ocorre um evento específico. Há diversos comportamentos diferentes para gatilhos que podem afetar o modo como o aplicativo lógico é monitorado.
* **Gatilho de sondagem** – esse gatilho sonda continuamente um ponto de extremidade até receber uma mensagem que satisfaça os critérios para a criação de uma nova instância de um aplicativo lógico para começar o fluxo de trabalho. O intervalo de sondagem pode ser configurado no gatilho no Designer dos Aplicativos Lógicos. Cada solicitação de sondagem conta como uma execução, mesmo quando nenhuma instância do aplicativo lógico é criada.
* **Gatilho de webhook** – esse disparador espera o envio de uma solicitação por parte do cliente em um ponto de extremidade específico. Cada solicitação enviada ao ponto de extremidade do webhook conta como uma execução da ação. Por exemplo, a Solicitação e o gatilho Webhook HTTP são gatilhos de webhook.
* **Gatilho de recorrência** – esse gatilho cria uma nova instância do aplicativo lógico com base no intervalo de recorrência configurado no gatilho. Por exemplo, você pode definir um gatilho de recorrência que é executado a cada três dias ou em um agendamento mais complexo.

Você pode encontrar as execuções do gatilho no painel de visão geral do aplicativo lógico na seção de histórico de gatilho.

### <a name="actions"></a>Ações
As ações internas, por exemplo, as ações que chame HTTP, Azure Functions, ou gerenciamento de API, bem como etapas do fluxo de controle, são monitoradas como ações nativo e tem seus respectivos tipos. Ações que chamam [conectores](https://docs.microsoft.com/connectors) têm o tipo de "ApiConnection". Conectores são classificados como conectores standard ou enterprise e são limitados em suas respectivas [preços][pricing].
Todas as ações de execução com êxito e sem êxito são contadas e monitoradas como execuções de ação. Entretanto, as ações que foram ignoradas devido a uma condição não atendida, ou ações que não foram executadas porque o aplicativo lógico foi encerrado antes da conclusão, não são contadas como execuções de ação. Aplicativos lógicos desabilitados não podem instanciar novas instâncias, para que eles não sejam cobrados enquanto eles estão desabilitados.

> [!NOTE]
> Depois de desativar um aplicativo lógico, suas instâncias em execução no momento podem demorar um pouco antes de eles param completamente.

Ações que são executadas em loops são contadas por cada ciclo no loop. Por exemplo, uma única ação em um loop "for each" que processa uma lista de itens de 10 é contada pela multiplicação do número de itens de lista (10), o número de ações no loop (1) mais um para iniciar o loop. Portanto, neste exemplo, o cálculo é (10 * 1) + 1, o que resulta em 11 execuções de ação.

### <a name="integration-account-usage"></a>Uso da conta de integração
Incluído no consumo com base em uso está uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) para exploração, desenvolvimento e testes, permitindo que você use os recursos [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e [processamento XML](logic-apps-enterprise-integration-xml.md) de Aplicativos Lógicos sem custo adicional. Você pode ter uma dessas contas de integração por região e o armazenamento de até 10 contratos e 25 mapas. Você pode ter e carregar certificados, esquemas e parceiros ilimitados.

Aplicativos lógicos também oferecem contas de integração básico e padrão com suporte à SLA de Aplicativos Lógicos. Quando você quiser usar apenas a manipulação de mensagens ou atuar como um parceiro de pequenas empresas que tenha uma relação de parceiro comercial com uma entidade de negócios maior, você pode usar contas de integração básica. Contas de integração Standard dão suporte a relações de B2B mais complexas e aumentam o número de entidades que você pode gerenciar. Para saber mais, veja [Preços do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="pricing"></a>Preços
Para obter mais informações, consulte a [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Próximas etapas
* [Uma visão geral dos Aplicativos Lógicos][whatis]
* [Criar seu primeiro aplicativo lógico][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

