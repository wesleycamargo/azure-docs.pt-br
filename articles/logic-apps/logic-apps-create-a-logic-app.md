---
title: "Criar seu primeiro fluxo de trabalho entre os aplicativos de nuvem e os serviços de nuvem - Aplicativo Lógico do Azure | Microsoft Docs"
description: "Automatizar os processos de negócios para os cenários de integração de sistemas e integração de aplicativos empresariais (EAI) criando e executando fluxos de trabalho no Aplicativo Lógico do Azure"
author: jeffhollan
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
ms.date: 03/31/2017
ms.author: LADocs; jehollan; estfan
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 204bf123509729b60b55c306050cef54aa7fecc5
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>Criar seu primeiro fluxo de trabalho lógico para automatizar os processos entre os aplicativos de nuvem e os serviços de nuvem

Sem escrever nenhum código, você pode automatizar os processos de negócios mais fácil e rapidamente quando cria e executa os fluxos de trabalho com o [Aplicativo Lógico do Azure](logic-apps-what-are-logic-apps.md). Este primeiro exemplo mostra como criar um fluxo de trabalho de aplicativo lógico básico que verifica um RSS feed para o novo conteúdo em um site. Quando novos itens aparecem no feed do site, o aplicativo lógico envia um email de uma conta do Outlook ou do Gmail.

Para criar e executar um aplicativo lógico, você precisa destes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, você pode [inscrever-se para uma assinatura Pré-paga](https://azure.microsoft.com/pricing/purchase-options/).

  Sua assinatura do Azure é usada para a cobrança de uso do aplicativo lógico. Saiba como a [medição de uso](../logic-apps/logic-apps-pricing.md) e os [preços](https://azure.microsoft.com/pricing/details/logic-apps) funcionam para os Aplicativos Lógicos do Azure.

Além disso, este exemplo requer estes itens:

* Uma conta do Gmail, Office 365 Outlook ou Outlook.com

    > [!TIP]
    > Se você tiver uma [conta da Microsoft](https://account.microsoft.com/account) pessoal, terá uma conta do Outlook.com. Caso contrário, se tiver uma conta corporativa ou de estudante do Azure, terá uma conta do **Outlook do Office 365**.

* Um link para o RSS feed de um site. Este exemplo usa o [RSS feed para as principais matérias do site CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="add-a-trigger-that-starts-your-workflow"></a>Adicionar um gatilho que inicia o fluxo de trabalho

Um [ *gatilho* ](./logic-apps-what-are-logic-apps.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho do aplicativo lógico e é o primeiro item que seu aplicativo lógico precisa.

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure").

2. No menu à esquerda, escolha **Novo** > **Enterprise Integration** > **Aplicativo Lógico**, como mostrado aqui:

     ![Portal do Azure, Novo, Enterprise Integration, Aplicativo Lógico](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > Você também pode escolher **Novo** e, na caixa de pesquisa, digitar `logic app` e pressionar Enter. Então, escolha **Aplicativo Lógico** > **Criar**.

3. Nomeie seu aplicativo lógico e selecione sua assinatura do Azure. Agora, crie ou selecione um grupo de recursos do Azure, o que ajuda a organizar e gerenciar os recursos do Azure afins. Por fim, selecione o local do datacenter para hospedar seu aplicativo lógico. Quando você estiver pronto, escolha **Fixar no painel** e **Criar**.

     ![Detalhes do aplicativo lógico](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > Quando você selecionar **Fixar no painel**, seu aplicativo lógico aparecerá no painel do Azure após a implantação e será aberto automaticamente. Se seu aplicativo lógico não aparecer no painel, no bloco **Todos os recursos**, escolha **Ver Mais** e selecione seu aplicativo lógico. No menu à esquerda, clique em **Mais serviços**. Em **Enterprise Integration**, escolha **Aplicativos Lógicos** e selecione seu aplicativo lógico.

4. Quando você abrir seu aplicativo lógico pela primeira vez, o Designer do Aplicativo Lógico mostrará os modelos que você pode usar para começar. Por ora, escolha **Aplicativo Lógico em Branco** para poder compilar seu aplicativo lógico do zero.

    O Designer do Aplicativo Lógico será aberto e mostrará os serviços disponíveis e *gatilhos* que você pode usar em seu aplicativo lógico.

5. Na caixa de pesquisa, digite `RSS` e selecione este gatilho: **RSS - quando um item do feed é publicado** 

    ![Gatilho do RSS](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. Insira o link do RSS feed do site que você deseja controlar. 

     Você também pode alterar a **Frequência** e o **Intervalo**. 
     Essas configurações determinam a frequência com que o aplicativo lógico verifica se há novos itens e retorna todos os itens encontrados durante esse período de tempo.

     Para este exemplo, iremos verificar todo dia as principais matérias postadas no site da CNN.

     ![Configurar um gatilho com o RSS feed, frequência e intervalo](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. Salve seu trabalho agora. (Na barra de comandos do designer, escolha **Salvar**.)

   ![Salve seu aplicativo lógico](media/logic-apps-create-a-logic-app/save-logic-app.png)

   Quando você salva, seu aplicativo lógico fica ativo, mas no momento, seu aplicativo lógico só verifica os novos itens no RSS feed especificado. 
   Para tornar este exemplo mais útil, adicionamos uma ação que seu aplicativo lógico executa após o gatilho disparar.

## <a name="add-an-action-that-responds-to-your-trigger"></a>Adicionar uma ação que responde a seu gatilho

Uma [*ação*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) é uma tarefa executada pelo fluxo de trabalho do aplicativo lógico. Depois de adicionar um gatilho ao seu aplicativo lógico, você poderá adicionar uma ação para executar operações com os dados gerados por esse gatilho. Para nosso exemplo, agora adicionaremos uma ação que envia email quando novos itens aparecem no RSS feed do site.

1. No designer, no gatilho, escolha **Nova etapa** > **Adicionar uma ação**, como mostrado aqui:

   ![Adicionar uma ação](media/logic-apps-create-a-logic-app/add-new-action.png)

   O designer mostra os [conectores disponíveis](../connectors/apis-list.md) para que você possa selecionar uma ação para executar quando o gatilho disparar.

2. Com base em sua conta de email, siga as etapas do Outlook ou do Gmail.

   * Para enviar um email de sua conta do Outlook, na caixa de pesquisa, digite `outlook`. Em **Serviços**, escolha **Outlook.com** para as contas pessoais da Microsoft ou escolha **Outlook do Office 365** para as contas corporativas ou de estudante do Azure. 
   Em **Ações**, selecione **Enviar um email**.

       ![Selecionar a ação "Enviar um email" do Outlook](media/logic-apps-create-a-logic-app/actions.png)

   * Para enviar um email de sua conta do Gmail, na caixa de pesquisa, digite `gmail`. 
   Em **Ações**, selecione **Enviar email**.

       ![Escolher "Gmail - enviar email"](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. Quando as credenciais forem solicitadas, entre com o nome de usuário e a senha de sua conta de email. 

4. Forneça os detalhes desta ação, como o endereço de email de destino, e escolha os parâmetros para os dados a incluir no email, por exemplo:

   ![Selecionar os dados a incluir no email](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    Portanto, se você escolheu o Outlook, seu aplicativo lógico poderá parecer com este exemplo:

    ![Aplicativo lógico concluído](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    Salve suas alterações. (Na barra de comandos do designer, escolha **Salvar**.)

6. Agora, você pode executar manualmente seu aplicativo lógico para testar. Na barra de comandos do designer, escolha **Executar**. Caso contrário, você pode deixar seu aplicativo lógico verificar o RSS feed especificado com base no agendamento configurado.

   Se seu aplicativo lógico encontrar novos itens, ele enviará um email que inclui os dados selecionados. 
   Se nenhum item novo for encontrado, seu aplicativo lógico irá ignorar a ação que envia um email.

7. Para monitorar e verificar a execução de seu aplicativo lógico e o histórico de gatilhos, no menu do aplicativo lógico, escolha **Visão Geral**.

   ![Monitorar e verificar a execução de seu aplicativo lógico e o histórico de gatilhos](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Se você não encontrar os dados esperados, na barra de comandos, escolha **Atualizar**.

   Para saber mais sobre o status de seu aplicativo lógico, a execução e o histórico de gatilhos, ou para diagnosticar seu aplicativo lógico, consulte [Solucionar problemas de seu aplicativo lógico](logic-apps-diagnosing-failures.md).

      > [!NOTE]
      > Seu aplicativo lógico continuará sendo executado até que você desative o aplicativo. Para desativar seu aplicativo agora, no menu de aplicativo lógico, escolha **Visão Geral**. Na barra de comandos, escolha **Desabilitar**.

Parabéns, você acabou de configurar e executar seu primeiro aplicativo lógico básico. Você também aprendeu como é fácil criar fluxos de trabalho que automatizam os processos e integram os aplicativos e serviços de nuvem - tudo sem código.

## <a name="manage-your-logic-app"></a>Gerenciar seu aplicativo lógico

Para gerenciar seu aplicativo, você pode executar tarefas como verificar o status, editar, exibir histórico, desativar ou excluir seu aplicativo lógico.

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure").

2. No menu à esquerda, clique em **Mais serviços**. Em **Enterprise Integration**, escolha **Aplicativos Lógicos**. Selecione seu aplicativo lógico. 

   No menu do aplicativo lógico, você pode encontrar estas tarefas de gerenciamento do aplicativo lógico:

   |Tarefa|Etapas| 
   |:---|:---| 
   | Exibir o status, histórico de execução e informações gerais de seu aplicativo| Escolha **Visão Geral**.| 
   | Editar seu aplicativo | Escolha **Designer de Aplicativos Lógicos**. | 
   | Exibir a definição JSON do fluxo de trabalho de seu aplicativo | Escolha **Exibir Código do Aplicativo Lógico**. | 
   | Exibir as operações executadas em seu aplicativo lógico | Escolha **Log de atividades**. | 
   | Exibir as antigas versões de seu aplicativo lógico | Escolha **Versões**. | 
   | Desativar temporariamente seu aplicativo | Escolha **Visão Geral**, em seguida, na barra de comandos, escolha **Desabilitar**. | 
   | Excluir seu aplicativo | Escolha **Visão Geral**, em seguida, na barra de comandos, escolha **Excluir**. Insira o nome do seu aplicativo lógico e escolha **Excluir**. | 

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

*  [Adicionar condições e executar fluxos de trabalho](../logic-apps/logic-apps-use-logic-app-features.md)
*     [Modelos de aplicativos lógicos](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Criar aplicativos lógicos a partir dos modelos do Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md)

