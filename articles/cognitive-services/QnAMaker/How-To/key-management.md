---
title: Gerenciamento de chaves - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Como gerenciar suas chaves do QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b402187f4949dac34fa476648c81b980ba3efc96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364250"
---
# <a name="key-management"></a>Gerenciamento de chaves

O serviço QnA Maker lida com dois tipos de chaves, **chaves de assinatura** e **chaves de ponto de extremidade**.

![gerenciamento de chaves](../media/qnamaker-how-to-key-management/key-management.png)

1. **Chaves de assinatura**: Essas chaves são usadas para acessar as [APIs do serviço de gerenciamento QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Essas APIs permitem que você execute várias operações CRUD na sua base de dados de conhecimento.  

2. **Chaves de ponto de extremidade**: Essas chaves são usadas para acessar o ponto de extremidade da base de dados de conhecimento para obter uma resposta para uma pergunta do usuário. Normalmente, você usaria esse ponto de extremidade no código do bot de bate-papo/aplicativo que consome o serviço QnA Maker.
 
## <a name="subscription-keys"></a>Chaves de assinatura
Você pode exibir e redefinir suas chaves de assinatura no portal do Azure onde você criou o recurso QnA Maker. 
1. Vá até o recurso QnA Maker no portal do Azure.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vá até **Chaves**.

    ![chave de assinatura](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Chaves de ponto de extremidade

As chaves de ponto de extremidade podem ser gerenciadas a partir do [portal do QnA Maker](https://qnamaker.ai).

1. Faça logon no [portal do QnA Maker](https://qnamaker.ai) e vá até **Gerenciar chaves**.

    ![chave de ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Exibir ou redefinir suas chaves.

    ![gerenciador de chaves de ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize as chaves se você achar que elas estão comprometidas. Isso pode exigir que sejam feitas as alterações correspondentes no seu código de aplicativo/Bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento em um idioma diferente](./language-knowledge-base.md)
