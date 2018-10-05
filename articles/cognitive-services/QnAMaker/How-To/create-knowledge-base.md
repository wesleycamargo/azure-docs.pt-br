---
title: Criar uma base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicionar bate-papo ao seu bot o torna mais comunicativo e interessante. O QnA Maker permite adicionar com facilidade um conjunto previamente populado dos principais bate-papos à sua KB. Esse pode ser um ponto de partida para o bate-papo do seu bot, economizando o tempo e o custo de escrevê-lo do zero.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 274c2289c75f44c5a1c8dd3799612a23f46a6d67
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037687"
---
# <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

O QnA Maker simplifica a adição das fontes de dados existentes ao criar uma base de dados de conhecimento. Você pode criar uma nova base de dados de conhecimento do QnA Maker dos seguintes tipos de documento:

<!-- added for scanability -->
* Páginas de perguntas frequentes
* Manuais de produtos
* Documentos estruturados

## <a name="steps"></a>Etapas

1. Entre no [portal do QnA Maker](https://qnamaker.ai) com suas credenciais do Azure e selecione **Criar novo serviço**.

    ![Criar uma base de dados de conhecimento ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Se você ainda não criou um serviço do QnA Maker, selecione **Criar um serviço do QnA**. 

3. Selecione seu locatário do Azure, o nome da assinatura do Azure e o nome do recurso do Azure associados ao serviço do QnA Maker nas listas na **Etapa 2** no portal do QnA Maker. Selecione o serviço do QnA Maker do Azure que hospedará a Base de dados de Conhecimento.

    ![Configurar serviço do QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. Insira o nome da sua base de dados de conhecimento e as fontes de dados para a nova base de dados de conhecimento.

    ![Definir fontes de dados](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Forneça um **nome** ao serviço. Nomes duplicados e caracteres especiais são compatíveis.
    - Adicione URLs para os dados que você deseja extrair. Consulte mais informações sobre os tipos de fontes com suporte [aqui](../Concepts/data-sources-supported.md).
    - Carregue arquivos para os dados que você deseja extrair. Consulte as [informações sobre preços](https://aka.ms/qnamaker-pricing) para ver quantos documentos é possível adicionar.
    - Se você quiser adicionar manualmente QnAs, poderá ignorar a **Etapa 4** mostrada na imagem anterior.

5. Adicione **Bate-papo** à sua base de dados de conhecimento. Escolha adicionar o suporte de bate-papo ao bot escolhendo uma das três personalidades predefinidas. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. Selecione **Criar sua KB**.

    ![Criar uma base de dados de conhecimento](../media/qnamaker-how-to-create-kb/create-kb.png)

7. Demora alguns minutos para os dados serem extraídos.

    ![Extração](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. Quando a base de dados de conhecimento for criada com êxito, você será redirecionado para a página **Base de dados de conhecimento**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar personalidade ao bate-papo](./chit-chat-knowledge-base.md)
