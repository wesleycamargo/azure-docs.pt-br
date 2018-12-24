---
title: Crie e gerencie fluxos de trabalho automatizados com o Visual Studio Code - Aplicativos Lógicos do Azure | Microsoft Docs
description: Início rápido sobre como criar e gerenciar aplicativos lógicos com JSON no VS Code (Visual Studio Code)
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 10/05/2018
ms.openlocfilehash: 0fec590523fa130af2e5670a92914c056df289d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229610"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Início rápido: Criar e gerenciar fluxos de trabalho de aplicativos lógicos automatizados – Visual Studio Code

Com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o Visual Studio Code, você pode criar e gerenciar aplicativos lógicos que ajudam a automatizar tarefas, fluxos de trabalho e processos para integrar aplicativos, dados, sistemas e serviços em organizações e empresas. Este início rápido mostra como criar e editar definições de fluxo de trabalho de aplicativo lógico trabalhando com o esquema de definição de fluxo de trabalho em JSON (JavaScript Object Notation) por meio de uma experiência baseada em código. Também é possível trabalhar em aplicativos lógicos existentes já implantados no <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> na nuvem. 

Embora seja possível executar essas mesmas tarefas no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> e no Visual Studio, você poderá começar mais rapidamente no Visual Studio Code quando já conhecer definições de aplicativo lógico e desejar trabalhar diretamente no código. Por exemplo, é possível desabilitar, habilitar, excluir e atualizar aplicativos lógicos já criados. Além disso, você pode trabalhar em aplicativos lógicos e nas contas de integração de qualquer plataforma de desenvolvimento em que o Visual Studio Code é executado, como Linux, Windows e Mac.

Para este artigo, você pode criar o mesmo aplicativo lógico que no início rápido de [para criar um aplicativo lógico no Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se concentra mais nos conceitos básicos. No Visual Studio Code, o aplicativo lógico se parece com este exemplo:

![Aplicativo lógico concluído](./media/create-logic-apps-visual-studio-code/overview.png)

Antes de começar, verifique se você possui estes itens:

* Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Conhecimento básico sobre [as definições do fluxo de trabalho do aplicativo lógico](../logic-apps/logic-apps-workflow-definition-language.md) e sua estrutura, que usa JSON (JavaScript Object Notation) 

  Se você for novo nos Aplicativos Lógicos, experimente o início rápido que explica [como criar seu primeiro aplicativo lógico no portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), que se concentra mais nos conceitos básicos. 

* Acesso à web para entrar no Azure e sua assinatura do Azure

* Baixe e instale essas ferramentas, caso você ainda não as tenha: 

  * <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code versão 1.25.1 ou posterior</a>, que é gratuito

  * Extensão do Visual Studio Code para Aplicativos Lógicos do Azure

    É possível baixar e instalar essa extensão do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) ou diretamente do Visual Studio Code. 
    Certifique-se de recarregar o código do Visual Studio após a instalação. 

    ![Encontre "Extensão do Visual Studio Code para Aplicativos Lógicos do Azure"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Para verificar se a extensão foi instalada corretamente, o ícone do Azure aparece na barra de ferramentas do código do Visual Studio. 

    ![Extensão instalada](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Para obter mais informações, consulte <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">Marketplace de extensão</a>. Você também pode exibir e enviar contribuições para a versão de software livre dessa extensão visitando a [Extensão de aplicativos lógicos do Azure para Visual Studio Code no GitHub](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Entrar no Azure

1. Abra o Visual Studio Code. Na barra de ferramentas Código do Visual Studio, selecione o ícone do Azure. 

   ![Selecione o ícone do Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Na janela do Azure, em **Aplicativos Lógicos**, selecione **Fazer login no Azure**. 

   ![Selecione "Entrar no Azure"](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Agora você é solicitado a entrar usando o código de autenticação fornecido. 

1. Copie o código de autenticação e, em seguida, escolha **Copiar e abrir**, o que abre uma nova janela do navegador.

   ![Prompt de entrada](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Insira o código de autenticação. Quando solicitado, escolha **continuar**.

   ![Insira o código](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Selecione sua conta do Azure. Depois de entrar, você pode fechar o navegador e retornar ao código do Visual Studio.

   Na janela do Azure, o painel Logic Apps e o painel Integration Accounts agora mostram as assinaturas do Azure em sua conta. 

   ![Escolha a assinatura](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Se você não visualizar as inscrições esperadas, ao lado do rótulo **Aplicativos lógicos**, escolha **Selecionar inscrições** (ícone de filtro). Localize e selecione as assinaturas que você deseja.

1. Para visualizar quaisquer aplicativos lógicos ou contas de integração existentes na sua assinatura do Azure, expanda sua assinatura.

   ![Exibir aplicativos lógicos e contas de integração](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Criar aplicativo lógico

1. Se você não tiver feito login na sua assinatura do Azure de dentro do Visual Studio Code, siga as etapas neste artigo para [entrar agora](#sign-in-azure).

1. No menu de contexto da sua assinatura, selecione **criar**.

   ![Selecione "Criar"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Na lista que mostra os grupos de recursos do Azure em sua assinatura, selecione um grupo de recursos existente ou **Crie um novo grupo de recursos**. 

   Este exemplo cria um novo grupo de recursos:

   ![Criar novo grupo de recursos](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Forneça um nome para o grupo de recursos do Azure e pressione ENTER.

   ![Nomeie seu grupo de recursos](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Selecione o local do datacenter para onde salvar os metadados do seu aplicativo lógico.

   ![Selecionar local](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Forneça um nome para seu aplicativo lógico e pressione ENTER.

   ![Nomeie seu aplicativo lógico](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Seu novo aplicativo lógico agora aparece na janela do Azure, sob sua assinatura do Azure. Agora você pode começar a criar a definição de fluxo de trabalho de seu aplicativo lógico.

1. No menu de atalho do aplicativo lógico, selecione **Abrir no Editor**. 

   ![Abra o aplicativo lógico no editor](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   O Visual Studio Code abre um modelo de definição de fluxo de trabalho de aplicativo lógico (arquivo .logicapp.json) para que você possa começar a criar o fluxo de trabalho do seu aplicativo lógico.

   ![Nova definição de fluxo de trabalho do aplicativo lógico](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. No arquivo de modelo de definição de fluxo de trabalho do aplicativo lógico, comece a criar a definição de fluxo de trabalho do seu aplicativo lógico. Para referência técnica, consulte o [esquema de linguagem de definição de fluxo de trabalho para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-workflow-definition-language.md).

   Aqui está um exemplo de definição de lógica. Geralmente, os elementos JSON aparecem em ordem alfabética em cada seção, mas essa amostra mostra esses elementos aproximadamente na ordem em que as etapas do aplicativo lógico aparecem no designer.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Quando terminar, salve seu arquivo de definição de aplicativo lógico. Quando o Visual Studio Code solicitar que você confirme o upload de sua definição de aplicativo lógico para sua assinatura do Azure, escolha **Upload**.

   ![Carregue seu novo aplicativo lógico](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Depois que o Visual Studio Code publica seu aplicativo lógico no Azure, você pode encontrar seu aplicativo agora ativo e em execução no portal do Azure. 

   ![Aplicativo lógico publicado no portal do Azure](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Editar aplicativo lógico

Para trabalhar em um aplicativo lógico existente que já está implantado no Azure, você pode abrir o arquivo de definição de fluxo de trabalho desse aplicativo no Visual Studio Code.

1. Se você não tiver feito login na sua assinatura do Azure de dentro do Visual Studio Code, siga as etapas neste artigo para [entrar agora](#sign-in-azure).

1. Na janela do Azure, em **Aplicativos Lógicos**, expanda sua assinatura do Azure e selecione o aplicativo lógico desejado. 

1. No menu do seu aplicativo lógico, selecione **Abrir no Editor**. Ou, ao lado do nome do seu aplicativo lógico, escolha o ícone de edição.

   ![Abra o editor para o aplicativo lógico existente](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   O Visual Studio Code abre o arquivo .logic app.json para sua definição de fluxo de trabalho de aplicativos lógicos.

   ![Definição de fluxo de trabalho de aplicativo lógico aberto](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Faça suas alterações na definição do seu aplicativo lógico.

1. Quando terminar, salve as alterações.

1. Quando o Visual Studio Code solicitar que você atualize sua definição de aplicativo lógico na sua assinatura do Azure, escolha **Upload**. 

   ![Carregue suas edições](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Fórum dos Aplicativos Lógicos do Azure</a>.
* Para enviar ou votar em ideias de recurso, visite o <a href="https://aka.ms/logicapps-wish" target="_blank">site de comentários do usuário de Aplicativos Lógicos</a>.

