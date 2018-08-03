---
title: Usar o aplicativo predefinido Cortana do LUIS | Microsoft Docs
description: Use o assistente pessoal Cortana, um aplicativo predefinido do LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: c7249cb8d8cff29f419412025c69e3b2b76b49d1
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110644"
---
# <a name="cortana-prebuilt-app"></a>Aplicativo predefinido Cortana

> [!IMPORTANT]
> Recomendamos que você use os [domínios predefinidos](./luis-how-to-use-prebuilt-domains.md), em vez do aplicativo predefinido do Cortana. Por exemplo, em vez de **builtin.intent.calendar.create_calendar_entry**, use **Calendar.Add** do domínio predefinido **Calendar**.
> Os domínios predefinido fornecem estas vantagens: 
> * Eles fornecem pacotes de intenções predefinidas e pré-treinadas, e entidades que foram projetadas para funcionar bem com a outro. Você pode integrar um domínio predefinido diretamente em seu aplicativo. Por exemplo, se você estiver criando um controlador fitness, você poderá adicionar o domínio **Fitness** e ter um conjunto inteiro de entidades para rastrear as atividades de fitness, incluindo intenções de controle de peso, planejamento de refeições, tempo ou distância restante e para salvar anotações de atividade física.
> * As intenções de domínio predefinidas são personalizáveis. Por exemplo, se você quiser fornecer análises sobre hotéis, você pode treinar e personalizar a intenção **Places.GetReviews** no domínio **Locais** para reconhecer solicitações para análises de hotel.
> * Os domínios predefinidos são extensíveis. Por exemplo, se você quiser usar o domínio predefinido **Locais** em um bot que procura restaurantes, e precisar de uma intenção para obter o tipo de culinária, você pode criar e treinar uma intenção **Places.GetCuisine**.

Além de permitir que você crie seus próprios aplicativos, o LUIS também fornece entidades e intenções do assistente pessoal Microsoft Cortana como um aplicativo predefinido. O comportamento desse aplicativo LUIS publicamente disponível não pode ser alterado. As entidades e as intenções neste aplicativo não podem ser editadas ou integradas a outros aplicativos LUIS. Se você quiser que seu aplicativo cliente tenha acesso a esse aplicativo predefinido e ao seu próprio aplicativo LUIS, seu aplicativo cliente precisará consultar os dois aplicativos LUIS.

O aplicativo do assistente pessoal predefinido está disponível nestas culturas (localidades): inglês, francês, italiano, espanhol e chinês.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>Obter o ponto de extremidade para o aplicativo predefinido Cortana

Você pode acessar o aplicativo predefinido Cortana usando os seguintes pontos de extremidade: 

| Linguagem | Ponto de extremidade|
|--------| ------------------|
| Inglês| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    Chinesa| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    Francês| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    Espanhol| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    Italiano| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> As URLs do ponto de extremidade também estão disponíveis na API [aplicativos - Obter aplicativos de assistente pessoal](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32).

## <a name="try-out-the-personal-assistant-app"></a>Experimente o aplicativo de assistente pessoal
Para chamar o ponto de extremidade, é possível anexar o argumento de chave de ponto de extremidade e cadeia de caracteres de consulta ao ponto de extremidade. 

Por exemplo, se o enunciado que você quer interpretar for "criar um compromisso para reunião de equipe", você poderá acrescentar esse enunciado à URL do ponto de extremidade. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

Você pode colar a URL em um navegador da Web e substituir a chave de ponto de extremidade do campo `{YOUR-SUBSCRIPTION-KEY}`.

No navegador, dá para ver que o aplicativo predefinido Cortana identifica `builtin.intent.calendar.create_calendar_entry` como a intenção e `builtin.calendar.title` como o tipo de entidade, e para o enunciado `create an appointment for team meeting`.

![Usar o aplicativo predefinido Cortana](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Referência do aplicativo predefinido Cortana](./luis-reference-cortana-prebuilt.md)

