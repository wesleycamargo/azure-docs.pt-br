<properties urlDisplayName="Get Started with Azure Automation" pageTitle="Introdu&ccedil;&atilde;o &agrave; Automa&ccedil;&atilde;o do Azure" metaKeywords="" description="Saiba como importar e executar uma tarefa de automa&ccedil;&atilde;o no Azure." metaCanonical="" services="automation" documentationCenter="" title="Introdu&ccedil;&atilde;o &agrave; Automa&ccedil;&atilde;o do Azure" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Introdução à Automação do Azure

A Automação do Microsoft Azure fornece uma maneira para os desenvolvedores automatizarem tarefas manuais, longas, sujeitas a erros e repetidas com frequência que são executadas em um ambiente de nuvem. Você pode criar, monitorar, gerenciar e implantar recursos em seu ambiente do Azure usando runbooks que, na realidade, são fluxos de trabalho do Windows PowerShell. Para obter mais informações sobre Automação, consulte o [Guia de Visão Geral de Automação (a página pode estar em inglês)][Guia de Visão Geral de Automação (a página pode estar em inglês)].

Este tutorial apresenta e explica as etapas para importar um runbook "Hello World” de exemplo na Automação do Azure, executá-lo e, em seguida, exibir sua saída.

> [WACOM.NOTE] Para aprender como automizar as operações do Azure usando [cmdlets do PowerShell do Azure][cmdlets do PowerShell do Azure], consulte[Automação do Azure: Autenticação do Azure usando o Active Directory do Azure][Automação do Azure: Autenticação do Azure usando o Active Directory do Azure].

## Exemplos e runbooks utilitários

A equipe de Automação do Azure criou vários exemplos de runbook para ajudar você a começar a trabalhar com a Automação. Esses exemplos abrangem os conceitos básicos da Automação e têm o objetivo de ajudá-lo a aprender como escrever seus próprios runbooks.

A equipe de Automação também criou os runbooks utilitários que você pode usar como blocos de construção para tarefas maiores de Automação.

> [WACOM.NOTE] É uma prática recomendada escrever runbooks pequenos, modulares e reutilizáveis. Também recomendamos que você crie seus próprios runbooks utilitários para os cenários mais usados quando estiver familiarizado com a automação.

Você pode exibir e baixar o exemplo da equipe de Automação e os runbooks utilitários no [Script Center][Script Center] ou importá-los diretamente da [Runbook Gallery][Runbook Gallery].

## A comunidade e os comentários da Automação

Os runbooks da comunidade e de outras equipes da Microsoft também são publicados no [Script Center][1] e na [Runbook Gallery][Runbook Gallery].

**Fornecer comentários** Se você estiver procurando por uma solução de runbook de automação ou por um módulo de integração, poste uma Solicitação de Script no Script Center. Se você tiver uma ideia para um novo recurso de Automação, poste-a no [User Voice (a página pode estar em inglês)][User Voice (a página pode estar em inglês)].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Etapas de alto nível para este tutorial

1.  [Assine para a visualização da automação][Assine para a visualização da automação]
2.  [Importar runbook da Runbook Gallery][Importar runbook da Runbook Gallery]
3.  [Publicar Runbook][Publicar Runbook]
4.  [Iniciar Runbook][Iniciar Runbook]

## <a name="preview"></a>Assine para a visualização da automação do Azure

Para começar a usar a automação, será necessário ter uma assinatura ativa do Azure com o recurso de visualização da Automação do Microsoft Azure habilitado.

-   Na página **Recursos de Visualização**, clique em **tentar novamente**.

    ![Habilitar visualização][Habilitar visualização]

## <a name="automationaccount"></a>Criar uma conta de automação

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  No Portal de Gerenciamento, clique em **Criar uma Conta de Automação**.

    > [WACOM.NOTE] Se você já tiver criado uma conta de automação, poderá pular para a etapa 4.

    ![Criar conta][Criar conta]

3.  Na página **Adicionar uma Nova Conta de Automação**, digite um nome para a conta e, em seguida, clique na marca de seleção.

    ![Adicionar nova conta][Adicionar nova conta]

## <a name="importrunbook"></a>Importar runbook da Runbook Gallery

1.  Na página **Automação**, clique na nova conta que você acabou de criar.

    ![Nova conta][Nova conta]

2.  Clique em **RUNBOOKS**.

    ![Guia Runbooks][Guia Runbooks]

3.  Clique em **Novo** \> **Runbook** \> **Da Galeria**.

    ![Runbook Gallery][2]

4.  Selecione a categoria **Tutorial** e depois **Hello World para automatização do Azure**. Clique no botão com a seta para a direita.

    ![Importar Runbook][Importar Runbook]

5.  Revise o conteúdo do runbook e clique no botão com a seta para a direita.

    ![Definição do Runbook][Definição do Runbook]

6.  Revise os detalhes do runbook e clique no botão com a marca de seleção.

    ![Detalhes do Runbook][Detalhes do Runbook]

## <a name="publishrunbook"></a>Publicar Runbook

1.  Quando a importação do runbook estiver concluída, clique em **Write-HelloWorld**.

    ![Runbook importado][Runbook importado]

2.  Clique em **AUTOR** e, em seguida, clique em **RASCUNHO**.

    Você pode modificar o conteúdo de um runbook no modo de Rascunho. Para este runbook, você não precisa fazer nenhuma modificação.

    ![Rascunho do autor][Rascunho do autor]

3.  Clique em **PUBLICAR** para promover o runbook para que esteja pronto para uso em produção.

    ![Publicar][Publicar]

4.  Quando receber um prompt para salvar e publicar o runbook, clique em **Sim**.

    ![Prompt para Salvar e publicar][Prompt para Salvar e publicar]

## <a name="startrunbook"></a>Iniciar Runbook

1.  Com o runbook **Write-HelloWorld** aberto, clique em **INICIAR**.

    ![Publicado][Publicado]

2.  Na página **Especificar valores de parâmetros de Runbook**, digite um **Nome** que será usado como um parâmetro de entrada para o script de Write-HelloWorld.ps1 e, em seguida, clique na marca de seleção.

    ![Parâmetros de runbook][Parâmetros de runbook]

3.  Clique em **TRABALHOS** para verificar o status do trabalho do runbook que você iniciou e, em seguida, clique no carimbo de data/hora na coluna **INÍCIO DO TRABALHO** para exibir o resumo do trabalho.

    ![Status do runbook][Status do runbook]

4.  Na página **RESUMO**, você pode ver o resumo, os parâmetros de entrada e a saída do trabalho.

    ![Resumo do runbook][Resumo do runbook]

# Gerenciando os serviços do Azure a partir de um Runbook

O exemplo acima mostra um runbook simples que não gerencia os serviços do Azure. Os [cmdlets do Azure][cmdlets do PowerShell do Azure] exigem autorização do Azure. Siga as instruções em [Automação do Azure: Autenticação do Azure usando o Active Directory do Azure][Automação do Azure: Autenticação do Azure usando o Active Directory do Azure] para configurar sua assinatura do Azure para o gerenciamento por meio da automação do Azure.

# Consulte também

-   [Visão geral da Automação][Visão geral da Automação]
-   [Guia de criação de runbook][Guia de criação de runbook]
-   [Fórum de Automação][Fórum de Automação]
-   [Automação do Azure: Autenticação do Azure usando o Active Directory do Azure][Automação do Azure: Autenticação do Azure usando o Active Directory do Azure]

  [Guia de Visão Geral de Automação (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [cmdlets do PowerShell do Azure]: http://msdn.microsoft.com/pt-br/library/jj156055.aspx
  [Automação do Azure: Autenticação do Azure usando o Active Directory do Azure]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [Script Center]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [Runbook Gallery]: http://aka.ms/runbookgallery
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice (a página pode estar em inglês)]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [Assine para a visualização da automação]: #automationaccount
  [Importar runbook da Runbook Gallery]: #importrunbook
  [Publicar Runbook]: #publishrunbook
  [Iniciar Runbook]: #startrunbook
  [Habilitar visualização]: ./media/automation/automation_00_EnablePreview.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Criar conta]: ./media/automation/automation_01_CreateAccount.png
  [Adicionar nova conta]: ./media/automation/automation_02_addnewautoacct.png
  [Nova conta]: ./media/automation/automation_03_NewAutoAcct.png
  [Guia Runbooks]: ./media/automation/automation_04_RunbooksTab.png
  [2]: ./media/automation/automation_05_ImportGallery.png
  [Importar Runbook]: ./media/automation/automation_06_ImportRunbook.png
  [Definição do Runbook]: ./media/automation/automation_07_RunbookDefinition.png
  [Detalhes do Runbook]: ./media/automation/automation_08_RunbookDetails.png
  [Runbook importado]: ./media/automation/automation_07_ImportedRunbook.png
  [Rascunho do autor]: ./media/automation/automation_08_AuthorDraft.png
  [Publicar]: ./media/automation/automation_085_Publish.png
  [Prompt para Salvar e publicar]: ./media/automation/automation_09_SavePubPrompt.png
  [Publicado]: ./media/automation/automation_10_PublishStart.png
  [Parâmetros de runbook]: ./media/automation/automation_11_RunbookParams.png
  [Status do runbook]: ./media/automation/automation_12_RunbookStatus.png
  [Resumo do runbook]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [Visão geral da Automação]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Guia de criação de runbook]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [Fórum de Automação]: http://go.microsoft.com/fwlink/p/?LinkId=390561
