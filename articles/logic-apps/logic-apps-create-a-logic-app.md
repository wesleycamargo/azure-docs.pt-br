---
title: "Automatizar fluxos de trabalho entre os sistemas e serviços de nuvem - Aplicativo Lógico do Azure | Microsoft Docs"
description: "Crie aplicativos lógicos para automatizar fluxos de trabalho para os cenários de integração de sistemas e de integração de aplicativos empresariais (EAI)"
author: ecfan
manager: anneta
editor: 
services: logic-apps
keywords: "fluxo de trabalho, aplicativos de nuvem, serviços de nuvem, processos de negócios, integração de sistemas, integração de aplicativos empresariais, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>Automatize seu primeiro fluxo de trabalho para processar dados com um aplicativo lógico

Para integrar sistemas e serviços mais rapidamente para sua organização, você pode automatizar fluxos de trabalho e processos de negócios com os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md). Este guia de início rápido mostra como é fácil criar e executar um fluxo de trabalho automatizado através da criação de um aplicativo lógico. O aplicativo de exemplo mostra como automatizar um fluxo de trabalho que verifica se há novos itens no RSS feed e envia um email para cada item.

Este exemplo de aplicativo lógico envia um email como neste exemplo:

![Email enviado para o novo item do feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

E aqui está o fluxo de trabalho de aplicativo lógico de alto nível que você cria:

![Visão geral - exemplo de aplicativo lógico](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

Neste guia de início rápido, você aprende a:

> [!div class="checklist"]
> * Criar um aplicativo lógico em branco.
> * Adicione um gatilho para iniciar o fluxo de trabalho quando um novo item aparece no RSS feed.
> * Adicione uma ação para enviar email com detalhes sobre o item do RSS feed.
> * Execute o fluxo de trabalho do seu aplicativo lógico.

Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de email de qualquer provedor de email com suporte pelos Aplicativos Lógicos do Azure para enviar notificações. Por exemplo, você pode usar o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros conectores de email com suporte, [verifique a lista de conectores](https://docs.microsoft.com/connectors/). Este guia de início rápido usa o Outlook do Office 365.

  > [!TIP]
  > Se você tiver uma [conta da Microsoft](https://account.microsoft.com/account) pessoal, terá uma conta do Outlook.com. Caso contrário, se tiver uma conta corporativa ou de estudante do Azure, terá uma conta do Outlook do Office 365.

* Um link para o RSS feed de um site. Este exemplo usa o [RSS feed para as principais matérias do site Reuters](http://feeds.reuters.com/reuters/topNews): `http://feeds.reuters.com/reuters/topNews`

Este guia de início rápido não requer que seja escrito um código, mas os Aplicativos Lógicos oferecem suporte a outros cenários que usam código, por exemplo, executar seu próprio código a partir de um aplicativo lógico com o [Azure Functions](../azure-functions/functions-overview.md).

## <a name="create-a-blank-logic-app"></a>Criar um aplicativo lógico em branco 

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2. No menu principal do Azure, escolha **Novo** > **Enterprise Integration** > **Aplicativo Lógico**.

   ![Portal do Azure, Novo, Enterprise Integration, Aplicativo Lógico](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Crie seu aplicativo lógico com as configurações na tabela da imagem:

   ![Defina os detalhes do aplicativo lógico](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome-seu-aplicativo-lógico* | Forneça um nome exclusivo de aplicativo lógico. | 
   | **Assinatura** | *your-Azure-subscription-name* | Selecione a assinatura do Azure que você deseja usar. | 
   | **Grupo de recursos** | *your-Azure-resource-group-name* | Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) para este aplicativo lógico e organize todos os recursos associados a este aplicativo. | 
   | **Localidade** | *your-Azure-datacenter-region* | Selecione a região do datacenter para implantar seu aplicativo lógico, por exemplo, Oeste dos EUA. | 
   | **Log Analytics** | Desativar | Ative o log de diagnósticos para seu aplicativo lógico, mas para este guia de início rápido, mantenha a configuração como **Desativada**. | 
   |||| 

4. Quando estiver pronto, selecione **Fixar no painel**. Dessa forma, seu aplicativo lógico aparece automaticamente no painel do Azure e é aberto após a implantação. Escolha **Criar**.

   > [!NOTE]
   > Se você não deseja fixar seu aplicativo lógico, é necessário localizar e abrir manualmente o aplicativo lógico após a implantação para poder continuar.

   Depois que o Azure implanta o aplicativo lógico, o Designer de Aplicativos Lógicos é exibido e mostra uma página com um vídeo de introdução. 
   Nesse vídeo, você pode encontrar modelos para padrões de aplicativo lógico comuns. 
   Este guia de início rápido cria o aplicativo lógico do zero. 

5. Role a tela até passar pelo vídeo de introdução e pelos gatilhos comuns. Em **Modelos**, escolha **Aplicativo lógico em branco**.

   ![Escolha o modelo de aplicativo lógico em branco](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   O Designer de Aplicativos Lógicos agora mostra os conectores disponíveis e seus gatilhos, que são usados para iniciar os fluxos de trabalho do aplicativo lógico.

   ![Gatilhos do aplicativo lógico](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>Adicionar um gatilho para detectar novos itens

Cada fluxo de trabalho do aplicativo lógico começa com um [gatilho](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). O gatilho é disparado quando ocorre um evento específico ou quando novos dados atendem à condição que você definiu. Cada vez que o disparador é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho.

1. Na caixa de pesquisa, insira "rss" como o seu filtro. Selecione este gatilho: **RSS - Quando um item do feed é publicado** 

   ![Selecione o gatilho: "RSS - Quando um item do feed é publicado"](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Forneça o link do RSS feed do site que você deseja monitorar, por exemplo, `http://feeds.reuters.com/reuters/topNews`. Defina o intervalo e a frequência da recorrência. Este exemplo verifica o feed a cada cinco minutos.

   ![Configurar um gatilho com o RSS feed, frequência e intervalo](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   Os Aplicativos Lógicos criam uma conexão ao RSS feed.

   > [!TIP]
   > Para simplificar a exibição no designer, você pode recolher e ocultar os detalhes de uma forma - é só clicar na barra de título da forma.

3. Salve seu trabalho. Clique em **Salvar** na barra de ferramentas do designer. 

   ![Salve seu aplicativo lógico](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   Seu aplicativo lógico agora está ativo, mas ele não faz nada além de verificar o RSS feed. Portanto, vamos adicionar uma ação que responde quando o gatilho é acionado.

## <a name="add-an-action-to-send-email"></a>Adicione uma ação para enviar email

Agora que você possui um gatilho, adicione uma [ação](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) que envia email quando um novo item é exibido no RSS feed. O fluxo de trabalho executa esta ação após o gatilho ser acionado.

1. No Designer de Aplicativos Lógicos, no gatilho, escolha **+ Nova etapa** > **Adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-create-a-logic-app/add-new-action.png)

   O designer mostra as ações que o seu aplicativo lógico pode realizar quando o gatilho é acionado.

   ![Selecionar na lista de ações](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. Na caixa de pesquisa, insira "enviar email" como filtro. Localize e selecione o conector de email que você deseja usar. Em seguida, selecione a ação "enviar email" para o conector. Por exemplo: 

   * Para as contas corporativas ou de estudante do Azure, selecione o Outlook do Office 365. 
   * Para contas pessoais da Microsoft, selecione Outlook.com. 
   * Para contas do Gmail, selecione Gmail. 

   Este guia de início rápido usa o Outlook do Office 365. 
   Se você usar outro provedor de email, as etapas serão as mesmas, mas a IU pode ser diferente. 

   ![Selecione esta ação: "Outlook do Office 365 - Enviar um email"](./media/logic-apps-create-a-logic-app/actions.png)

3. Quando as credenciais forem solicitadas, entre com o nome de usuário e a senha de sua conta de email. 

   Os Aplicativos Lógicos criam uma conexão à sua conta de email.

4. Agora, especifique os dados que você deseja incluir no email. 

   1. Na caixa **Para**, insira o endereço de email do destinatário. 
   Para fins de teste, você pode usar seu próprio endereço de email.

   2. Na caixa **Assunto**, digite o assunto do email. 
   Para este exemplo, insira "Novo item RSS:" conforme mostrado:

      ![Insira o assunto do email](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      Ao clicar dentro da caixa de edição, a lista para **Adicionar conteúdo dinâmico** é exibida para que você possa selecionar os campos de dados disponíveis para incluir na sua ação. 
      Se a lista de conteúdo dinâmico não abrir, na respectiva caixa de edição, escolha **Adicionar conteúdo dinâmico**.

   3. Na lista **Adicionar conteúdo dinâmico**, selecione **Título do feed**, que inclui o título do item no email.

      ![Insira o assunto do email](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      Quando você tiver acabado, o assunto do email deve ser semelhante ao exemplo a seguir:

      ![Título de feed adicionado](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > Caso você selecione um campo que tenha uma matriz, como **categories-item**, o designer adicionará automaticamente um loop “For each” em torno da ação que faz referência a esse campo. Dessa forma, seu aplicativo lógico pode executar essa ação em cada item da matriz. 
      > 
      > Para remover o loop, escolha as elipses (**...**) na barra de título do loop e, em seguida, escolha **Excluir**.

   4. Na caixa **Corpo**, insira o conteúdo do corpo do email. 
   Para este exemplo, digite este texto e selecione estes campos:

      ![Adicionar conteúdos no corpo do email](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | Campo | Descrição | 
      | ----- | ----------- | 
      | **Título do feed** | Mostrar o título do item. | 
      | **Feed publicado em** | Mostrar a data e a hora de publicação do item. | 
      | **Link principal do feed** | Mostrar a URL do item. | 
      ||| 

      > [!TIP]
      > Para adicionar linhas em branco em uma caixa de edição, pressione Shift + Enter. 
      
5. Salve seu trabalho. Clique em **Salvar** na barra de ferramentas do designer.

   ![Aplicativo lógico concluído](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>Execute o fluxo de trabalho do seu aplicativo lógico

Para executar manualmente o aplicativo lógico, escolha **Executar** na barra de ferramentas do designer. Caso contrário, você pode esperar que o seu aplicativo lógico seja executado conforme o cronograma configurado.

![Executar o aplicativo lógico](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

Se o RSS feed possuir novos itens, o seu aplicativo lógico enviará um email para cada novo item. Por exemplo, aqui está um email de exemplo do Outlook enviado por esse aplicativo lógico:

![Email enviado para o novo item do feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Se o seu feed não possuir novos itens, o aplicativo lógico ignora a etapa que envia o email e aguarda o próximo intervalo antes de verificar novamente. 

> [!TIP]
> Se você não receber nenhum email, verifique a sua pasta de Lixo eletrônico. Caso contrário, se você não tiver certeza de que seu aplicativo lógico foi executado corretamente, confira [Solução de problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

Parabéns! Você acabou de criar e executar seu primeiro aplicativo lógico. Este guia de início rápido mostrou como criar fluxos de trabalho automatizados para integrar sistemas e serviços de forma fácil e rápida.

## <a name="clean-up-resources"></a>Limpar recursos

Seu aplicativo lógico continua em execução e possivelmente cobrando tarifas adicionais da sua assinatura do Azure, até que você desative ou exclua seu aplicativo. Além disso, ao criar conexões para o seu aplicativo lógico, essas conexões são mantidas, mesmo após o aplicativo lógico ter sido excluído. 

Quando tiver concluído tudo, verifique se desativou ou excluiu todos os recursos aos quais você não deseja que sejam cobradas tarifas adicionais ou que não deseja manter. Para excluir todos os recursos criados para este guia de início rápido, exclua o grupo de recursos do Azure que você criou para este aplicativo lógico. 

### <a name="delete-resource-group"></a>Excluir grupo de recursos

Se você não deseja manter nada relacionado ao seu aplicativo lógico, exclua o grupo de recursos que você criou para este guia de início rápido e todos os recursos relacionados. Saiba mais sobre [como gerenciar grupos de recursos do Azure](../azure-resource-manager/resource-group-portal.md#manage-resources).

1. No menu do Azure, escolha **Grupos de recursos**.

2. Escolha o grupo de recursos que você deseja excluir. No menu do grupo de recursos, escolha **Visão geral**, se ainda não estiver selecionado. 

3. Verifique todos os recursos no grupo que você deseja excluir. Quando você estiver pronto, escolha **Excluir grupo de recursos** na barra de ferramentas do grupo de recursos.

### <a name="turn-off-logic-app"></a>Desativar o aplicativo lógico

Para interromper a execução do seu aplicativo lógico sem excluir o seu trabalho, desabilite o aplicativo. 

No menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Desabilitar**.

  ![Como desabilitar o aplicativo lógico](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se o menu do aplicativo lógico não aparecer, tente retornar ao painel do Azure e abra novamente o aplicativo lógico.

### <a name="delete-logic-app"></a>Excluir aplicativo lógico

Você pode excluir apenas o aplicativo lógico, mas mantenha todos os outros recursos relacionados, como as conexões que você criou.

1. No menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Excluir**. 

   ![Excluir seu aplicativo lógico](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > Se o menu do aplicativo lógico não aparecer, tente retornar ao painel do Azure e abra novamente o aplicativo lógico.

2. Confirme que você deseja excluir o aplicativo lógico e escolha **Excluir**.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar fluxos de trabalho de aplicativo lógico com modelos predefinidos](../logic-apps/logic-apps-create-logic-apps-from-templates.md)