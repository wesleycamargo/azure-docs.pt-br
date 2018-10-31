---
title: Domínios predefinidos para LUIS (Serviço Inteligente de Reconhecimento Vocal)
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de domínios predefinidos para adicionar rapidamente cenários comuns de usuário de conversação.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: b1c7ced4a934ea5d094e0c54a295870986f09933
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651789"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Adicionar domínios predefinidos para cenários comuns de uso 

O LUIS inclui um conjunto de intenções predefinidas dos domínios predefinidos para adicionar rapidamente intenções e enunciados comuns. Essa é uma maneira rápida e fácil de adicionar habilidades ao aplicativo cliente de conversação sem a necessidade de projetar os modelos para essas habilidades. 

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio predefinido

1. Na página **Meus aplicativos**, selecione o aplicativo. Isso abre o aplicativo na seção **Compilar** do aplicativo. 

1. Na página **Intenções**, selecione **Adicionar domínios predefinidos** na barra de ferramentas inferior esquerda. 

1. Selecione a intenção **Calendário** e, em seguida, selecione o botão **Adicionar domínio**.

    ![Adicionar domínio predefinido do Calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Selecione **Intenções** na navegação esquerda para ver as intenções do Calendário. Cada intenção deste domínio é prefixada com `Calendar.`. Juntamente com enunciados, duas entidades para esse domínio são adicionadas ao aplicativo: `Calendar.Location` e `Calendar.Subject`. 

## <a name="train-and-publish"></a>Treinar e publicar

1. Depois que o domínio for adicionado, treine o aplicativo selecionando **Treinar** na barra de ferramentas superior direita. 

1. Na barra de ferramentas superior, selecione **Publicar**. Publique em **Produção**. 

1. Quando a notificação de êxito verde aparecer, selecione o link **Referir-se à lista de pontos de extremidade** para ver os pontos de extremidade.

1. Selecionar um ponto de extremidade. Uma nova guia do navegador é aberta para esse ponto de extremidade. Mantenha a guia do navegador aberta e continue na seção **Testar**.

## <a name="test"></a>Teste

Teste a nova intenção no ponto de extremidade adicionando um valor para o parâmetro **q**: `Schedule a meeting with John Smith in Seattle next week`.

O LUIS retorna a intenção correta e o assunto da reunião:

```JSON
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Referência de domínio predefinido](./luis-reference-prebuilt-domains.md)
