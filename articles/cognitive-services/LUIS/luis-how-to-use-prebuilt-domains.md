---
title: Usar domínios predefinidos em aplicativos do LUIS no Azure | Microsoft Docs
description: Saiba como usar domínios predefinidos em aplicativos Serviço Inteligente de Reconhecimento Vocal (LUIS)
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: diberry
ms.openlocfilehash: df57f9adf5bf7f5f652a77ddeafe950463c6a9fc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224170"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Usar os domínios predefinidos em aplicativos do LUIS  

O Reconhecimento vocal (LUIS) fornece *domínios predefinidos*, que são conjuntos predefinidos de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que trabalham juntas para domínios ou categorias comuns de aplicativos clientes. Os domínios predefinidos têm sido previamente treinados e estão prontos para serem adicionados ao seu aplicativo do LUIS. As intenções e entidades em um domínio predefinido são totalmente personalizáveis depois que você os tiver adicionado ao seu aplicativo – você pode treiná-los com declarações do seu sistema para que eles funcionem para seus usuários. Você pode usar um domínio predefinido inteiro como um ponto de partida para personalização ou pegar emprestadas apenas algumas intenções ou entidades de um domínio predefinido. 

Procure a guia **Domínios predefinidos** para ver outros domínios predefinidos que você pode usar em seu aplicativo. Clique no bloco para um domínio para adicioná-lo ao seu aplicativo ou clique em **Saiba mais** em seu bloco para saber mais sobre suas intenções e entidades.

> [!TIP]
> Você pode encontrar uma lista completa dos domínios predefinidos na [Referência de domínios predefinidos](./luis-reference-prebuilt-domains.md).

![Adicionar domínio predefinido](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio predefinido
Na guia **Domínios predefinidos**, localize o domínio RestaurantReservation e clique em **Adicionar domínio**. Depois que o domínio predefinido tiver sido adicionado ao seu aplicativo do LUIS, abra **Intenções** e clique na intenção RestaurantReservation.Reserve. Você pode ver que muitas declarações de exemplo já foram fornecidas e rotuladas com entidades.

![intenção RestaurantReservation.Reserve](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Projetando aplicativos do LUIS de domínios predefinidos
Ao usar domínios predefinidos em seu aplicativo do LUIS, você pode personalizar todo o domínio predefinido ou iniciar com algumas de suas intenções e entidades.

## <a name="customizing-an-entire-prebuilt-domain"></a>Personalizando um domínio predefinido inteiro
Domínios predefinidos são projetados para serem gerais. Eles contêm muitas intenções e entidades, que você pode escolher para personalizar um aplicativo de acordo com as suas necessidades. Se você começar personalizando um domínio predefinido inteiro, exclua as intenções e as entidades que seu aplicativo não precisa usar. Você também pode adicionar algumas intenções ou entidades ao conjunto que o domínio predefinido já fornece. Por exemplo, se você estiver usando o domínio predefinido **Eventos** para um aplicativo de evento esportivo, pode para adicionar entidades a times. Quando você começar a [fornecer declarações](luis-how-to-add-example-utterances.md) para o LUIS, inclua termos que sejam específicos ao seu aplicativo. O LUIS aprende a reconhecê-los e personaliza as intenções e entidades do domínio predefinido para as necessidades do seu aplicativo. 

> [!TIP]
> As intenções e as entidades em um domínio predefinido funcionam melhor juntas. É melhor combinar intenções e entidades do mesmo domínio, quando for possível.
> * Uma prática recomendada é usar intenções relacionadas do mesmo domínio. Por exemplo, se você estiver personalizando a intenção `MakeReservation` no domínio **Locais**, selecione a intenção `Cancel` do domínio **Locais** em vez da intenção Cancelar nos domínios **Eventos** ou **Utilitários**.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterando o comportamento de uma intenção de domínio predefinido
Você pode achar que um domínio predefinido contém uma intenção que é semelhante a uma que você deseja ter em seu aplicativo do LUIS mas quer que tenha um comportamento diferente. Por exemplo, o domínio predefinido **Locais** compilada fornece uma intenção `MakeReservation` para fazer uma reserva de restaurante, mas você quer que seu aplicativo use essa intenção para fazer reservas de hotel. Nesse caso, você pode modificar o comportamento dessa intenção fornecendo declarações para o LUIS sobre fazer reservas de hotel e rotulá-los usando a intenção `MakeReservation`, de modo que o LUIS possa ser treinado novamente para reconhecer a intenção `MakeReservation` em uma solicitação para reservar um hotel.

> [!TIP]
> Verifique o domínio Utilitários para intenções predefinidas que você pode personalizar para usar em qualquer domínio. Por exemplo, você pode adicionar `Utilities.Repeat` ao seu aplicativo e treiná-lo para reconhecer qualquer ação que o usuário quiser repetir em seu aplicativo.


## <a name="next-step"></a>Próxima etapa

Personalize um domínio predefinido adicionando mais declarações de exemplo a ele.

> [!div class="nextstepaction"]
> [Adicionar declarações de exemplo](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Recursos adicionais

Consulte a [Referência de domínios predefinidos](./luis-reference-prebuilt-domains.md) para obter mais detalhes sobre os domínios predefinidos.
