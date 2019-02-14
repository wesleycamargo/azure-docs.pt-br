---
title: Gerenciamento de recursos e de chaves – QnA Maker
titleSuffix: Azure Cognitive Services
description: O serviço do QnA Maker lida com dois tipos de chaves, chaves de assinatura e chaves de ponto de extremidade.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 11/26/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3b75c2f0ec24fd58527643c8ccfec35f8cdd5914
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871890"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Como gerenciar chaves no QnA Maker

O serviço QnA Maker lida com dois tipos de chaves, **chaves de assinatura** e **chaves de ponto de extremidade**.

![gerenciamento de chaves](../media/qnamaker-how-to-key-management/key-management.png)

1. **Chaves de assinatura**: essas chaves são usadas para acessar as [APIs do serviço de gerenciamento do QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Essas APIs permitem que você realize a edição da sua base de conhecimento.  

2. **Chaves de ponto de extremidade**: essas chaves são usadas para acessar o ponto de extremidade da base de dados de conhecimento para obter uma resposta para uma pergunta do usuário. Normalmente, você usaria esse ponto de extremidade no seu bot de chat ou código de aplicativo cliente que consome o serviço QnA Maker.
 
## <a name="subscription-keys"></a>Chaves de assinatura
Você pode exibir e redefinir suas chaves de assinatura no portal do Azure onde você criou o recurso QnA Maker. 
1. Vá até o recurso QnA Maker no portal do Azure.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vá até **Chaves**.

    ![chave de assinatura](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Chaves de ponto de extremidade

As chaves de ponto de extremidade podem ser gerenciadas a partir do [portal do QnA Maker](https://qnamaker.ai).

1. Faça logon no [Portal do QnA Maker](https://qnamaker.ai), vá até seu perfil e, em seguida, clique em **Configurações de serviço**.

    ![chave de ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Exibir ou redefinir suas chaves.

    ![gerenciador de chaves de ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize as chaves se você achar que elas estão comprometidas. Isso pode exigir que sejam feitas as alterações correspondentes no seu aplicativo cliente ou código bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento em um idioma diferente](./language-knowledge-base.md)
