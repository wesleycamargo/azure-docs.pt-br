---
title: "Criar fluxos de trabalho com seu primeiro Aplicativo Lógico do Azure | Microsoft Docs"
description: "Comece a se conectar a aplicativos e serviços SaaS com seu primeiro Aplicativo Lógico"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Criar um novo aplicativo lógico conectando serviços SaaS
Este tópico demonstra como, em apenas alguns minutos, você pode começar a usar o [Aplicativo Lógico do Azure](logic-apps-what-are-logic-apps.md). Vamos percorrer um fluxo de trabalho simples que permite enviar tweets interessantes ao seu email.

Para usar este cenário, você precisará de:

* Uma assinatura do Azure
* Uma conta do Twitter
* Uma conta do Outlook.com ou do Outlook do Office 365

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Criar um novo aplicativo lógico para enviar tweets por email

1. Entre no [Portal do Azure](https://portal.azure.com). 

2. No menu à esquerda, escolha **Novo** > **Enterprise Integration** > **Aplicativo Lógico**.

    Você também pode escolher **Novo** e, na caixa de pesquisa, digitar `logic app` e pressionar Enter. Escolha **Aplicativo Lógico** > **Criar**.

3. Insira um nome para seu aplicativo lógico, selecione sua assinatura do Azure, crie ou selecione um grupo de recursos do Azure, selecione um local e escolha **Criar**.

    Se você selecionar **Fixar no Painel** , o aplicativo lógico será aberto automaticamente depois da implantação.

4. Ao abrir o aplicativo lógico pela primeira vez, você poderá selecionar um modelo para começar.
Agora, clique em **Aplicativo Lógico em Branco** para compilá-lo do zero. 

5. O primeiro item que você precisa criar é o disparador. Esse é o evento que inicia o aplicativo lógico. Na caixa Pesquisar, procure **Twitter**e selecione **Quando um novo tweet é publicado**. Entre com o nome de usuário e a senha da sua conta do Twitter.

6. Digite um termo de pesquisa para disparar seu aplicativo lógico.

   ![Pesquisa do Twitter](media/logic-apps-create-a-logic-app/twittersearch.png)

    A **Frequência** e o **Intervalo** determinam a frequência com que o aplicativo lógico verifica se há novos tweets (e retorna todos os tweets durante esse período de tempo).

7. Selecione **Nova etapa**e escolha **Adicionar uma ação** ou **Adicionar uma condição**.

    Ao selecionar **Adicionar uma ação**, você pode pesquisar [conectores disponíveis](../connectors/apis-list.md) para escolher uma ação. 

8. Na caixa Pesquisar, procure **outlook**e selecione **Enviar um email** a fim de enviar email de sua conta do Outlook aos endereços de email especificados.

   ![Ações](media/logic-apps-create-a-logic-app/actions.png)

9. Agora você precisa preencher os parâmetros para o email desejado: 

   ![Parâmetros](media/logic-apps-create-a-logic-app/parameters.png)

10. Por fim, você pode selecionar **Salvar** para ativar seu aplicativo lógico.

## <a name="manage-your-logic-app-after-creation"></a>Gerenciar seu aplicativo lógico após a criação

Agora seu aplicativo lógico está em execução. Ele verificará periodicamente se há tweets com o termo de pesquisa digitado. Quando encontrar um tweet correspondente, ele enviará um email. Por fim, você verá como desabilitar o aplicativo ou como está seu desempenho.

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. No menu esquerdo, clique em **Mais serviços**. Em **Enterprise Integration**, escolha **Aplicativos Lógicos**. Selecione seu aplicativo lógico.

    *    Para exibir o status, o histórico de execução e as informações gerais do seu aplicativo, no menu de aplicativo lógico, escolha **Visão geral**. Se você não encontrar os dados esperados, escolha **Atualização** na barra de comandos.

    *    Para editar seu aplicativo, no menu de aplicativo lógico, escolha **Designer de Aplicativos Lógicos**.

    *    Para desativar seu aplicativo temporariamente, no menu de aplicativo lógico, escolha **Visão geral**. Na barra de comandos, escolha **Desabilitar**.

    *    Para excluir seu aplicativo, no menu de aplicativo lógico, escolha **Visão geral**. 
    Na barra de comandos, escolha **Excluir**. Insira o nome do seu aplicativo lógico e escolha **Excluir**.

Em menos de 5 minutos, você configurou um aplicativo lógico simples e o colocou em execução na nuvem. Para saber mais sobre como usar os recursos de aplicativos lógicos, consulte [Usar os recursos de aplicativos lógicos]. Para saber mais sobre as definições de aplicativos lógicos, consulte [Criar definições de aplicativos lógicos](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Usar os recursos de aplicativos lógicos]: logic-apps-create-a-logic-app.md
