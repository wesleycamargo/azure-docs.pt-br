---
title: Colaborando na base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o Controle de Acesso Baseado em Função do Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: de9ba7e9a30c38bf096b7fc0a25a3925d938f604
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265304"
---
# <a name="collaborate-on-your-knowledge-base"></a>Colaborar com sua base de dados de conhecimento

O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o [Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) do Azure. 

Execute as seguintes etapas para compartilhar seu serviço QnA Maker com alguém:

1. Faça logon no portal do Azure e vá para o recurso QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Abra a guia **Controle de Acesso (IAM)**.

    ![IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Selecione **Adicionar**.

    ![Adicionar IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Selecione a função de **Proprietário** ou **Colaborador**. Você não pode conceder acesso somente leitura por meio do Controle de Acesso Baseado em Função. Função de Proprietário e Colaborador tem direito de acesso de leitura/gravação ao serviço QnA Maker.

    ![Adicionar função no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Insira o email com o qual você deseja compartilhar com e clique em Salvar.

    ![Adicionar no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Agora quando a pessoa com a qual você compartilhou o serviço QnA Maker, entrar [portal do QnA Maker](https://qnamaker.ai) ela pode ver todas as bases de dados de conhecimento do serviço.

Lembre-se de que não é possível compartilhar uma base de Conhecimento específica em um serviço do QnA Maker. Se desejar um controle de acesso mais granular, considere distribuir as bases de dados de conhecimento em diferentes serviços do QnA Maker.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)
