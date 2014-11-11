<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Introdução à Automação do Azure

A Automação do Microsoft Azure fornece uma maneira para os desenvolvedores automatizarem tarefas manuais, longas, sujeitas a erros e repetidas com frequência que são executadas em um ambiente de nuvem. Você pode criar, monitorar, gerenciar e implantar recursos em seu ambiente do Azure usando runbooks que, na realidade, são fluxos de trabalho do Windows PowerShell. Para obter mais informações sobre Automação, consulte o [Guia de Visão Geral de Automação (a página pode estar em inglês)][Guia de Visão Geral de Automação (a página pode estar em inglês)].

Este tutorial apresenta e explica as etapas para importar um runbook "Hello World” de exemplo na Automação do Azure, executá-lo e, em seguida, exibir sua saída.

> [WACOM.NOTE] Para aprender como automizar as operações do Azure usando [cmdlets do PowerShell do Azure][cmdlets do PowerShell do Azure], consulte[Automação do Azure: Autenticação do Azure usando o Active Directory do Azure][Automação do Azure: Autenticação do Azure usando o Active Directory do Azure].

## Exemplos e runbooks utilitários

A equipe de Automação do Azure criou vários exemplos de runbook para ajudar você a começar a trabalhar com a Automação. Esses exemplos abrangem os conceitos básicos da Automação e têm o objetivo de ajudá-lo a aprender como escrever seus próprios runbooks.

A equipe de Automação também criou os runbooks utilitários que você pode usar como blocos de construção para tarefas maiores de Automação.

> [WACOM.NOTE] É uma prática recomendada escrever runbooks pequenos, modulares e reutilizáveis. Também recomendamos que você crie seus próprios runbooks utilitários para os cenários mais usados quando estiver familiarizado com a automação.

Você pode exibir e baixar o exemplo da equipe de Automação e os runbooks utilitários no [Script Center][Script Center].

## A comunidade e os comentários da Automação

Os runbooks da comunidade e de outras equipes da Microsoft também são publicados no [Script Center][1].

**Fornecer comentários** Se você estiver procurando por uma solução de runbook de automação ou por um módulo de integração, poste uma Solicitação de Script no Script Center. Se você tiver uma ideia para um novo recurso de Automação, poste-a no [User Voice (a página pode estar em inglês)][User Voice (a página pode estar em inglês)].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Etapas de alto nível para este tutorial

1.  [Assine para a visualização da automação][Assine para a visualização da automação]
2.  [Baixe um exemplo de runbook][Baixe um exemplo de runbook]
3.  [Importe, execute e visualize o resultado do exemplo de runbook][Importe, execute e visualize o resultado do exemplo de runbook]

## <a name="preview"></a>Assine para a visualização da automação do Azure

Para começar a usar a automação, será necessário ter uma assinatura ativa do Azure com o recurso de visualização da Automação do Microsoft Azure habilitado.

-   Na página **Recursos de Visualização**, clique em **tentar novamente**.

    ![Habilitar visualização][Habilitar visualização]

## <a name="download-sample"></a> Baixe um exemplo de runbook do Script Center

1.  Vá para o [Script Center][Script Center] e, em seguida, clique em **Hello World para automatização do Azure**.

2.  Clique no nome do arquivo **Write-HelloWorld.ps1**, ao lado de **Baixar** e, em seguida, salve o arquivo em seu computador.

## <a name="import-sample"></a>Importe, execute e visualize o resultado do exemplo de runbook na automação do Azure

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  No Portal de Gerenciamento, clique em **Criar uma Conta de Automação**.

    > [WACOM.NOTE] Se você já tiver criado uma conta de automação, poderá pular para a etapa 4.

    ![Criar conta][Criar conta]

3.  Na página **Adicionar uma Nova Conta de Automação**, digite um nome para a conta e, em seguida, clique na marca de seleção.

    ![Adicionar nova conta][Adicionar nova conta]

4.  Na página **Automação**, clique na nova conta que você acabou de criar.

    ![Nova conta][Nova conta]

5.  Clique em **RUNBOOKS**.

    ![Guia Runbooks][Guia Runbooks]

6.  Clique em **IMPORTAR**.

    ![Importar][Importar]

7.  Navegue até o script **Write-HelloWorld.ps1** que você baixou e, em seguida, clique na marca de seleção.

    ![Procurar][Procurar]

8.  Clique em **Write-HelloWorld**.

    ![Runbook importado][Runbook importado]

9.  Clique em **AUTOR** e, em seguida, clique em **RASCUNHO**. Para este runbook, você não precisa fazer nenhuma modificação.

    Agora você pode ver o conteúdo de **Write-HelloWorld.ps1**. Você pode modificar o conteúdo de um runbook no modo de Rascunho.

    ![Rascunho do autor][Rascunho do autor]

10. Clique em **PUBLICAR** para promover o runbook para que esteja pronto para uso em produção.

    ![Publicar][Publicar]

11. Quando receber um prompt para salvar e publicar o runbook, clique em **Sim**.

    ![Prompt para Salvar e publicar][Prompt para Salvar e publicar]

12. Clique em **PUBLICADO** e, em seguida, clique em **INICIAR**.

    ![Publicado][Publicado]

13. Na página **Especificar valores de parâmetros de Runbook**, digite um **Nome** que será usado como um parâmetro de entrada para o script de Write-HelloWorld.ps1 e, em seguida, clique na marca de seleção.

    ![Parâmetros de runbook][Parâmetros de runbook]

14. Clique em **TRABALHOS** para verificar o status do trabalho do runbook que você iniciou e, em seguida, clique no carimbo de data/hora na coluna **INÍCIO DO TRABALHO** para exibir o resumo do trabalho.

    ![Status do runbook][Status do runbook]

15. Na página **RESUMO**, você pode ver o resumo, os parâmetros de entrada e a saída do trabalho.

    ![Resumo do runbook][Resumo do runbook]

# Gerenciando os serviços do Azure a partir de um Runbook

O exemplo acima mostra um runbook simples que não gerencia os serviços do Azure. Os [cmdlets do Azure][cmdlets do PowerShell do Azure] exigem autorização do Azure. Siga as instruções em [Automação do Azure: Autenticação do Azure usando o Active Directory do Azure][Automação do Azure: Autenticação do Azure usando o Active Directory do Azure] para configurar sua assinatura do Azure para o gerenciamento por meio da automação do Azure.

# Consulte também

-   [Visão geral da Automação][Visão geral da Automação]
-   [Guia de criação de runbook][Guia de criação de runbook]
-   [Fórum de Automação][Fórum de Automação]
-   [Automação do Azure: Autenticação do Azure usando o Active Directory do Azure][Automação do Azure: Autenticação do Azure usando o Active Directory do Azure]

  [cmdlets do PowerShell do Azure]: http://msdn.microsoft.com/pt-br/library/jj156055.aspx
  [Automação do Azure: Autenticação do Azure usando o Active Directory do Azure]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [Script Center]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [create-account-note]: ../includes/create-account-note.md
  [Assine para a visualização da automação]: #preview
  [Baixe um exemplo de runbook]: #download-sample
  [Importe, execute e visualize o resultado do exemplo de runbook]: #import-sample
  [Habilitar visualização]: ./media/automation/automation_00_EnablePreview.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Criar conta]: ./media/automation/automation_01_CreateAccount.png
  [Adicionar nova conta]: ./media/automation/automation_02_addnewautoacct.png
  [Nova conta]: ./media/automation/automation_03_NewAutoAcct.png
  [Guia Runbooks]: ./media/automation/automation_04_RunbooksTab.png
  [Importar]: ./media/automation/automation_05_Import.png
  [Procurar]: ./media/automation/automation_06_Browse.png
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
