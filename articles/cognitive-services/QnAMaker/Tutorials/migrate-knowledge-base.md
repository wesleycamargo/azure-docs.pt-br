---
title: Como importar uma base de dados de conhecimento – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Como importar uma base de dados de conhecimento
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce8f98f9bdb37d5f326e942fe5b5e815e5272c56
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35364870"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento usando export-import
O QnA Maker anunciou a disponibilidade geral (GA) em 7 de maio de 2018 na conferência \\\build\. A GA do QnA Maker tem uma nova arquitetura criada no Azure. As Bases de Dados de Conhecimento criadas com a versão prévia gratuita do QnA Maker precisarão ser migrada para a GA do QnA. A versão prévia do QnA Maker será preterida em novembro de 2018. Para obter mais informações sobre as alterações na GA do QnA Maker, consulte o anúncio da [postagem de blog](https://aka.ms/qnamakerga-blog) da GA do QnA Maker.

O QnA Maker agora tem um [modelo de preços](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/).

pré-requisitos
> [!div class="checklist"]
> * Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
> * Configurar um novo [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Migrar uma base de dados de conhecimento do portal da versão prévia do QnA Maker
1. Navegue até o [Portal de versão prévia do QnA Maker](https://aka.ms/qnamaker-old-portal
) e clique em **Meus serviços**.
2. Selecione a base de dados de conhecimento que você deseja migrar clicando no ícone de edição.

    ![Editar a base de dados de conhecimento](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Clique em **Baixar a base de dados de conhecimento** para baixar um arquivo .tsv que contém o conteúdo de sua base de dados de conhecimento: perguntas, respostas, metadados e nomes de fonte de dados do qual eles foram extraídos.

    ![Baixar a base de dados de conhecimento](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Entre no [portal do QnA Maker](https://qnamaker.ai) com suas credenciais do Azure e clique em **Criar novo serviço**.

    ![Criar uma base de dados de conhecimento ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. Se você ainda não criou um serviço do QnA Maker, selecione **Criar um serviço do QnA**. De outro modo, escolha um serviço do QnA Maker nos menus suspensos da Etapa 2. Selecione o serviço do QnA Maker que hospedará a Base de Dados de Conhecimento.

    ![Configurar serviço do QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Criar uma base de dados de conhecimento vazia 

    ![Definir fontes de dados](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Forneça um **nome** ao serviço. Nomes duplicados e caracteres especiais têm suporte.
    - Ignorar o carregamento de arquivos ou URLs porque você deseja usar os dados da sua base de dados de conhecimento de versão prévia. Por enquanto, você criará uma base de dados de conhecimento vazia.

7. Selecione **Criar**.

    ![Criar uma base de dados de conhecimento](../media/qnamaker-how-to-create-kb/create-kb.png)

8. Nessa nova base de dados de conhecimento, abra a guia **Configurações** e clique em **Importar a base de dados de conhecimento**. Isso importa as perguntas, as respostas e os metadados, e mantém os nomes das fontes de dados das quais eles foram extraídos.

   ![Importar base de dados de conhecimento](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Teste** a nova base de dados de conhecimento usando o painel de teste. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).
10. **Publicar** a base de dados de conhecimento. Saiba como [publicar a base de dados de conhecimento](../How-To/publish-knowledge-base.md).
11. Use o ponto de extremidade abaixo em seu aplicativo ou código de bot. Veja aqui como [Criar um bot de QnA](../Tutorials/create-qna-bot.md).

    ![Valores do QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

Neste ponto, todo o conteúdo da base de dados de conhecimento – perguntas, respostas e metadados, juntamente com os nomes dos arquivos de origem e as URLs, são importados para a nova base de dados de conhecimento. 

## <a name="chatlogs-and-alterations"></a>Chatlogs e alterações
As alterações (sinônimos) não são importadas automaticamente. Use as [APIs do V2](https://aka.ms/qnamaker-v2-apis) para exportar as alterações da pilha de versão prévia e as [APIs do V4](https://aka.ms/qnamaker-v4-apis) para substituir na nova pilha.

Não é possível migrar chatlogs, porque a nova pilha usa o Application Insights para armazenar chatlogs. No entanto, você pode baixar os chatlogs do [portal de versão prévia](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
