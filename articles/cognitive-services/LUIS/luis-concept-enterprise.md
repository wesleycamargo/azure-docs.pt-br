---
title: Conceitos empresariais para um aplicativo LUIS – Azure | Microsoft Docs
description: Compreenda os conceitos de design de aplicativos LUIS grandes.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/05/2018
ms.author: v-geberr
ms.openlocfilehash: 1f501981dd4b45f4d36188ef4c2aaa6cb11881a2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263746"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Estratégias empresariais para um aplicativo LUIS
Examine essas estratégias de design para seu aplicativo empresarial.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando você espera solicitações do LUIS além da cota
Se a taxa de solicitação do aplicativo LUIS exceder a [taxa de cota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) permitida, distribua a carga para mais aplicativos LUIS com a [mesma definição de aplicativo](#use-multiple-apps-with-same-app-definition) ou crie e [atribua várias chaves](#assign-multiple-luis-keys-to-same-app) ao aplicativo. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Usar vários aplicativos com a mesma definição de aplicativo
Exporte o aplicativo LUIS original e, em seguida, importe-o de volta para aplicativos separados. Cada aplicativo tem sua própria ID do aplicativo. Quando você publica, em vez de usar a mesma chave entre todos os aplicativos, crie uma chave separada para cada aplicativo. Equilibre a carga entre todos os aplicativos para que nenhum aplicativo único fique sobrecarregado. Adicione o [Application Insights](luis-tutorial-bot-csharp-appinsights.md) para monitorar o uso. 

Para obter a mesma intenção principal entre todos os aplicativos, certifique-se de que a previsão da intenção entre a primeira e a segunda intenção seja ampla o suficiente para que o LUIS não fique confuso, oferecendo diferentes resultados entre aplicativos para obter pequenas variações em declarações. 

Designe um único aplicativo como o principal. Quaisquer declarações sugeridas para exame devem ser adicionadas ao aplicativo principal e movidas de volta para todos os outros aplicativos. Isso é uma exportação completa do aplicativo ou o carregamento de declarações rotuladas do principal para os filhos. O carregamento pode ser feito do site do [LUIS][LUIS] ou da API de criação para uma [única declaração](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) ou para um [lote](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Agende um [exame de declarações de ponto de extremidade](label-suggested-utterances.md) periódico para o aprendizado ativo, como a cada duas semanas e, em seguida, treine e publique novamente. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Atribuir várias chave LUIS ao mesmo aplicativo
Se seu aplicativo LUIS receber mais ocorrências de ponto de extremidade do que a cota da sua única chave, crie e atribua mais chaves ao aplicativo LUIS. Crie um gerenciador de tráfego ou balanceador de carga para gerenciar as consultas de ponto de extremidade entre as chaves de assinatura. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando seu aplicativo monolítico retorna uma intenção errada
Se seu aplicativo deve prever uma ampla variedade de declarações do usuário, considere implementar o [modelo de expedição](#dispatch-tool-and-model). Dividir um aplicativo monolítico permite que o LUIS concentre a detecção entre intenções de maneira bem-sucedida, em vez de ficar confuso entre intenções no aplicativo pai e nos aplicativos filho. 

Agende um [exame de declarações de ponto de extremidade](label-suggested-utterances.md) periódico para o aprendizado ativo, como a cada duas semanas e, em seguida, treine e publique novamente. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando você precisa ter mais de 500 intenções
Por exemplo, digamos que você está desenvolvendo um assistente de escritório que tem mais de 500 intenções. Se 200 intenções relacionarem-se ao agendamento de reuniões, 200 forem sobre lembretes, 200 se tratarem de obter informações sobre colegas e 200 forem para envio de email, agrupe as intenções para que cada grupo esteja em um único aplicativo e crie um aplicativo de nível superior que contém cada intenção. Use a [ferramenta de expedição e arquitetura](#dispatch-tool-and-model) para criar o aplicativo de nível superior. Em seguida, altere seu bot para usar a chamada em cascata conforme mostrado no [tutorial de expedição][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando você precisar combinar vários aplicativos LUIS e QnA Maker
Se você tiver vários aplicativos LUIS e QnA Maker que precisam responder a um bot, use a [ferramenta de expedição](#dispatch-tool-and-model) para criar o aplicativo de nível superior. Em seguida, altere seu bot para usar a chamada em cascata conforme mostrado no [tutorial de expedição][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Ferramenta e modelo de expedição
Use a ferramenta de linha de comando [Expedir][dispatch-tool], encontrada em [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) para combinar vários aplicativos LUIS e/ou QnA Maker em um aplicativo LUIS pai. Essa abordagem permite que você tenha um domínio pai incluindo todos os sujeitos e diferentes domínios filhos de entidade em aplicativos separados. 

![Imagem conceitual da arquitetura de expedição](./media/luis-concept-enterprise/dispatch-architecture.png)

O domínio pai é observado no LUIS como um aplicativo **V Dispatch**. 

![Captura de tela da lista de aplicativos LUIS com o aplicativo LUIS criado pela ferramenta de expedição](./media/luis-concept-enterprise/dispatch.png)

O chatbot recebe a declaração e envia ao aplicativo LUIS pai para previsão. A principal intenção prevista do aplicativo pai determina qual aplicativo LUIS filho é chamado em seguida. O chatbot envia a declaração ao aplicativo filho para obter uma previsão mais específica.

Entenda como essa hierarquia de chamadas é feita em [dispatcher-application-tutorial][dispatcher-application-tutorial] do Bot Builder v4.  

### <a name="intent-limits-in-dispatch-model"></a>Limites de intenção no modelo de expedição
Um aplicativo de expedição tem 500 fontes de expedição, equivalentes a 500 intenções, como o máximo. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [testar um lote](luis-how-to-batch-test.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch