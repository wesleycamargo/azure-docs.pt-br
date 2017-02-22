---
title: "Adicionar o gatilho de recorrência em aplicativos lógicos | Microsoft Docs"
description: "Visão geral do gatilho de recorrência e como usá-lo com um aplicativo lógico do Azure."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: fe558958c316c8dba42163e277ae01451f712e5a


---
# <a name="get-started-with-the-recurrence-trigger"></a>Introdução ao gatilho de recorrência
Usando o gatilho de recorrência, você pode criar fluxos de trabalho poderosos na nuvem.

Por exemplo, você pode:

* Agendar um fluxo de trabalho para executar um procedimento armazenado SQL todos os dias.
* Enviar por email um resumo de todos os tweets da semana passada sobre uma determinada hashtag.

Para começar a usar o gatilho de recorrência em um aplicativo lógico, confira [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-a-recurrence-trigger"></a>Usar um gatilho de recorrência
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](connectors-overview.md).

Veja uma sequência de exemplo de como configurar um gatilho de recorrência em um aplicativo lógico:

1. Adicione o gatilho **Recorrência** como a primeira etapa em um aplicativo lógico.
2. Preencha os parâmetros para o intervalo de recorrência.

O aplicativo lógico iniciará uma execução depois de cada intervalo de tempo.

![Gatilho HTTP](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>Detalhes do gatilho
O gatilho de recorrência tem as propriedades a seguir que você pode configurar.

Ele dispara um aplicativo lógico depois de um intervalo de tempo especificado.
Um * significa que é um campo obrigatório.

| Nome de exibição | Nome da propriedade | Descrição |
| --- | --- | --- |
| Frequência* |frequência |A unidade de tempo: `Second`, `Minute`, `Hour`, `Day` ou `Year`. |
| Intervalo* |intervalo |O intervalo da frequência determinada para a recorrência. |
| Fuso horário |timeZone |Se uma hora de início for fornecida sem uma diferença UTC, este fuso horário será usado. |
| Hora de início |startTime |A hora de início no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). |

<br>

## <a name="next-steps"></a>Próximas etapas
Agora, experimente a plataforma e [crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).




<!--HONumber=Jan17_HO3-->


