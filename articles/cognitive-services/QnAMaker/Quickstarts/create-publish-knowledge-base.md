---
title: Criar, treinar, publicar base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: É possível criar uma KB (base de dados de conhecimento) do QnA Maker com base no seu próprio conteúdo, como perguntas frequentes ou manuais de produto. Neste exemplo, a KB do QnA Maker é criada com base em uma página da Web simples de perguntas frequentes, a fim de solucionar dúvidas sobre a recuperação da chave do BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 3f7b24af3d48075150ae7c192ccf421b928a7aa3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113692"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Criar, treinar e publicar sua base de dados de conhecimento do QnA Maker

É possível criar uma KB (base de dados de conhecimento) do QnA Maker com base no seu próprio conteúdo, como perguntas frequentes ou manuais de produto. Neste exemplo, a KB do QnA Maker é criada com base em uma página da Web simples de perguntas frequentes, a fim de solucionar dúvidas sobre a recuperação da chave do BitLocker.

## <a name="prerequisite"></a>Pré-requisito

> [!div class="checklist"]
> * Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker

1. Entre no QnAMaker.ai com suas credenciais do Azure.

2. No site do QnA Maker, selecione **Criar uma base de dados de conhecimento**.

   ![Criar nova KB](../media/qna-maker-create-kb.png)

3. Na página **Criar**, na etapa 1, selecione **Criar um serviço de QnA**. Você será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar o serviço do QnA Maker na sua assinatura. Se o portal do Azure atingir o tempo limite, selecione **Tentar novamente** no site. Depois que você se conectar, o painel do Azure será exibido.

4. Após a criação bem-sucedida de um novo serviço do QnA Maker no Azure, retorne para qnamaker.ai/create. Selecione o serviço de QnA nas listas suspensas da etapa 2. Se você tiver criado um novo serviço de QnA, não se esqueça de atualizar a página.

   ![Selecionar uma KB do serviço de QnA](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. Na etapa 3, nomeie a KB como **Minha KB de exemplo do QnA**.

6. Para adicionar conteúdo à KB, selecione três tipos de fontes de dados. Na etapa 4, em **Popular a KB**, adicione a URL [Perguntas frequentes sobre a recuperação do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) na caixa **URL**.

   ![Selecionar uma KB do serviço de QnA](../media/qnamaker-quickstart-kb/add-datasources.png)

7. Na etapa 5, selecione **Criar sua KB**.

8. Enquanto a KB está sendo criada, uma janela pop-up será exibida. O processo de extração leva alguns minutos para ler a página HTML e identificar perguntas e respostas.

9. Após a KB ser criada com êxito, a página **Base de dados de conhecimento** será aberta. É possível editar o conteúdo da KB nesta página.

10. No canto superior direito, selecione **Adicionar par de QnA** para adicionar uma nova linha à seção **Editorial** da KB. Em **Pergunta**, insira **Oi.** Em **Resposta**, insira **Olá. Faça perguntas sobre o BitLocker.**

    ![Adicionar um par de QnA](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. Na parte superior direito, selecione **Salvar e treinar** para salvar suas edições e treinar o modelo do QnA Maker. As edições não serão mantidas se não forem salvas.

12. Na parte superior direita, selecione **Testar** para verificar se as alterações entraram em vigor. Insira `hi there` na caixa e pressione Enter. A resposta que você criou será exibida.

13. Selecione **Inspecionar** para examinar a resposta mais detalhadamente. A janela de teste é usada para testar as alterações na KB antes que elas sejam publicadas.

    ![Painel de teste](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Selecione **Testar** novamente para fechar o item pop-up **Testar**.

15. No menu do lado a lado **Editar**, selecione **Publicar**. Para confirmar, selecione **Publicar** na página.

16. O serviço do QnA Maker foi publicado com êxito. É possível usar o ponto de extremidade no aplicativo ou código de bot.

    ![Publicar](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md)
