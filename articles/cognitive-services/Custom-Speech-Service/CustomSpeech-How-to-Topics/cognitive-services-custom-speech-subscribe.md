---
title: Obter chaves de assinatura para o Serviço de Fala Personalizada no Azure | Microsoft Docs
description: Saiba como obter chaves de assinatura para chamadas para o Serviço de Fala Personalizada em Serviços Cognitivos.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fcef86a19a77581ff82b64173e2ac68b26ae708a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363442"
---
# <a name="obtain-subscription-keys"></a>Obter chaves de assinatura
Para começar a usar o Serviço de Fala Personalizada do Azure, primeiro você precisa vincular sua conta de usuário a uma assinatura do Azure. Estão disponíveis tipos de assinaturas gratuitas e pagas. Para obter informações sobre os tipos, consulte a [página de preços](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Obter uma chave de assinatura
1. Você pode obter uma chave de assinatura no portal do Azure em uma das duas maneiras:

    * Vá para o [portal do Azure](https://ms.portal.azure.com), e adicione uma nova API de Serviços Cognitivos procurando por _Serviços Cognitivos_ e, em seguida, selecionando **APIs de Serviços Cognitivos**.

      ![Pesquisa de Serviços Cognitivos](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Ou vá diretamente para as [APIs de Serviços Cognitivos](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![API de Serviços Cognitivos](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
2. Preencha os campos obrigatórios a seguir:

      a. **Nome da conta**. Use um nome que funciona para você. Lembre-se desse nome para que você possa encontrar sua assinatura dos Serviços Cognitivos na lista de recursos.

      b. **Assinatura**. Selecione uma das suas assinaturas do Azure.

      c. **Tipo de API**. Selecione **Serviço de Fala Personalizado (versão prévia)**.

      d. **Local**. Atualmente é **Oeste dos EUA**.

      e. **Tipo de preço**. Selecione o tipo que funciona para você. **F0** é o tipo gratuito. As cotas que são permitidas são explicadas no [página de preços](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Criação da conta dos Serviços Cognitivos](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

3. Você deve encontrar um modo de exibição no painel ou um serviço com o nome da conta fornecido na sua lista de recursos. Quando você selecioná-la, você pode ver uma visão geral do serviço. Na lista à esquerda, em **Gerenciamento de Recursos**, selecione **Chaves**. Copie a **CHAVE 1**.

      Essa chave de assinatura é necessária nas etapas a seguir.

      ![CHAVE 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Não copie a **ID da assinatura** da página de visão geral. Você precisa da chave de assinatura na próxima etapa.
      >

      ![Visão geral da ID da assinatura](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

4. Para inserir a chave de assinatura, na faixa de opções no canto superior direito, selecione sua conta de usuário. No menu suspenso, selecione **Assinaturas**.

      ![Item de menu Assinaturas](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    É exibida uma tabela de assinaturas, que está vazia na primeira vez que é aberta.

    ![Tabela de assinaturas](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

5. Selecione **Adicionar nova**. Insira um nome para a assinatura e a chave de assinatura. Ela pode ser uma **CHAVE 1** (chave primária) ou **CHAVE 2** (chave secundária) de sua assinatura.

      ![Nome da chave de assinatura](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Para carregar dados, treinar um modelo, ou fazer uma implantação, você precisa vincular suas atividades de Serviço de Fala Personalizada para uma assinatura do Azure. Pode ser uma assinatura de tipo gratuito ou tipo pago. Para saber mais, confira a [página de preço](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Obter uma ID da assinatura
Para obter uma ID de assinatura, acesse o portal do Azure. Procure *Serviços Cognitivos* e *Serviço de Fala Personalizada* e siga as instruções.

> [!NOTE]
> A chave de assinatura é necessária posteriormente nesse processo.
>

## <a name="next-steps"></a>Próximas etapas
* Comece a criar seu [modelo acústico personalizado](cognitive-services-custom-speech-create-acoustic-model.md).
* Comece a criar seu [modelo de linguagem personalizado](cognitive-services-custom-speech-create-language-model.md).
