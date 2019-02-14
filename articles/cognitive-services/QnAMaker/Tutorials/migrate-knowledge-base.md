---
title: Migrar bases de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: Mova uma base de dados de conhecimento criada com o QnA Maker para uma nova base de dados de conhecimento.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/06/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 73f355a6e8c9373a5c31dd7cfebd4455aa324302
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809735"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento usando export-import

Migrar uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento e a importação para outra. 

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Configurar um novo [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de dados de conhecimento do QnA Maker
1. Entre no [portal do QnA Maker](https://qnamaker.ai).
1. Selecione a base de dados de conhecimento que você deseja migrar.

1. Na página **Configurações**, selecione **Exportar base de dados de conhecimento** para baixar um arquivo .tsv que contém o conteúdo de sua base de dados de conhecimento: perguntas, respostas, metadados e nomes de fonte de dados da qual eles foram extraídos.

1. Selecione **Criar uma base de dados de conhecimento** no menu superior e crie uma base de dados de conhecimento vazia. 

    ![Definir fontes de dados](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Forneça um **nome** ao serviço. Nomes duplicados e caracteres especiais têm suporte.

1. Selecione **Criar**.

    ![Criar uma base de dados de conhecimento](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Nessa nova base de dados de conhecimento, abra a guia **Configurações** e selecione **Importar base de dados de conhecimento**. Isso importa as perguntas, as respostas e os metadados, e mantém os nomes das fontes de dados das quais eles foram extraídos.

   ![Importar base de dados de conhecimento](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Teste** a nova base de dados de conhecimento usando o painel de teste. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).
1. **Publicar** a base de dados de conhecimento. Saiba como [publicar a base de dados de conhecimento](../How-To/publish-knowledge-base.md).
1. Use o ponto de extremidade no aplicativo ou código de bot. Veja aqui como [Criar um bot de QnA](../Tutorials/create-qna-bot.md).

    ![Valores do QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

    Neste ponto, todo o conteúdo da base de dados de conhecimento – perguntas, respostas e metadados, juntamente com os nomes dos arquivos de origem e as URLs, são importados para a nova base de dados de conhecimento. 

## <a name="chat-logs-and-alterations"></a>Logs de chat e alterações
As alterações (sinônimos) não são importadas automaticamente. Use as [APIs V2](https://aka.ms/qnamaker-v2-apis) para exportar as alterações da base de dados de conhecimento antiga e as [APIs V4](https://aka.ms/qnamaker-v4-apis) a fim de mover as alterações para a nova base de dados de conhecimento.

Não há nenhuma maneira de migrar os logs de chat, já que a nova base de dados de conhecimento usa o Application Insights para armazenar os logs de chat. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
