<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Introdução à Automação do Azure" pageTitle="Introdução à Automação do Azure" metaKeywords="" description="Saiba como importar e executar um trabalho de automação no Azure." metaCanonical="" services="automation" documentationCenter="" title="Introdução à Automação do Azure" authors="" solutions="" manager="" editor="" />


# Introdução à Automação do Azure

Este tutorial apresenta e explica as etapas para importar e executar um runbook de Automação no Microsoft Azure. 

A Automação do Microsoft Azure fornece uma maneira para os desenvolvedores automatizarem tarefas manuais, longas, sujeitas a erros e repetidas com frequência que são executadas em um ambiente de nuvem. Você pode criar, monitorar, gerenciar e implantar recursos em seu ambiente do Azure usando runbooks que, na realidade, são fluxos de trabalho do Windows PowerShell. Para obter mais informações sobre Automação, consulte o [Guia de Visão Geral de Automação (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkId=392861). 

Este tutorial apresenta e explica as etapas para importar um runbook de exemplo na Automação do Azure, executá-lo e, em seguida, exibir sua saída.

## Exemplos e runbooks utilitários

A equipe de Automação do Azure criou vários exemplos de runbook para ajudar você a começar a trabalhar com a Automação.  Esses exemplos abrangem os conceitos básicos da Automação e têm o objetivo de ajudá-lo a aprender como escrever seus próprios runbooks.  

A equipe de Automação também criou os runbooks utilitários que você pode usar como blocos de construção para tarefas maiores de Automação.  

>[WACOM.NOTE] É uma prática recomendada escrever runbooks pequeno, modulares e reutilizáveis. Também recomendamos que você crie seus próprios runbooks utilitários para cenários mais usados quando estiver familiarizado com a automação.  

Você pode exibir e baixar o exemplo da equipe de Automação e os runbooks utilitários no [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=393029). 

## A comunidade e os comentários da Automação

Os runbooks da comunidade e outras equipes da Microsoft também são publicados no [Script Center](http://go.microsoft.com/fwlink/?LinkID=391681). 

<strong>Fornecer comentários</strong>  Se você estiver procurando por uma solução de runbook ou um módulo de integração do PowerShell, poste uma Solicitação de Script no Script Center. Se você tiver uma ideia para um novo recurso de Automação, poste-a no [User Voice (a página pode estar em inglês)](http://feedback.windowsazure.com/forums/34192--general-feedback).

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Baixe um exemplo de runbook do Script Center

1.	Vá para o [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=393029) e, em seguida, clique em **Hello World for Azure Automation**.

2.	Clique no nome do arquivo **Write-HelloWorld.ps1**, ao lado de **Baixar** e, em seguida, salve o arquivo em seu computador.


## Importar o exemplo de runbook no Azure

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2.	No Portal de Gerenciamento, clique em **Criar uma Conta de Automação**.

	>[WACOM.NOTE] Se você já tiver criado uma conta de automação, poderá pular para a etapa 4.

	![Criar conta](./media/automation/automation_01_CreateAccount.png)

3.	Na página **Adicionar uma Nova Conta de Automação**, digite um nome para a conta e, em seguida, clique na marca de seleção.

	![Adicionar nova conta](./media/automation/automation_02_addnewautoacct.png)
 
4.	Na página **Automação**, clique na nova conta que você acabou de criar.
 
	![Nova conta](./media/automation/automation_03_NewAutoAcct.png)

5.	Clique em **RUNBOOKS**.

	![Guia Runbooks](./media/automation/automation_04_RunbooksTab.png)
  
6.	Clique em **IMPORTAR**.

	![Importar](./media/automation/automation_05_Import.png)

7.	Navegue até o script **Write-HelloWorld.ps1** que você baixou e, em seguida, clique na marca de seleção.

	![Procurar](./media/automation/automation_06_Browse.png)	
 
8.	Clique em **Write-HelloWorld**.

	![Runbook importado](./media/automation/automation_07_ImportedRunbook.png)

9.	Clique em **AUTOR** e, em seguida, clique em **RASCUNHO**. Para este runbook, você não precisa fazer nenhuma modificação.  

	Agora você pode ver o conteúdo de **Write-HelloWorld.ps1**. Você pode modificar o conteúdo de um runbook no modo de Rascunho. 

	![Rascunho do autor](./media/automation/automation_08_AuthorDraft.png)  
 
10.	Clique em **PUBLICAR** para promover o runbook para que esteja pronto para uso em produção.

	![Publicar](./media/automation/automation_085_Publish.png)
   
11.	Quando receber um prompt para salvar e publicar o runbook, clique em **Sim**.
 
	![Prompt para Salvar e publicar](./media/automation/automation_09_SavePubPrompt.png)

12.	Clique em **PUBLICADO** e, em seguida, clique em **INICIAR**.

	![Publicado](./media/automation/automation_10_PublishStart.png)
 
13.	Na página **Especificar valores de parâmetros de Runbook**, digite um **Nome** que será usado como um parâmetro de entrada para o script de Write-HelloWorld.ps1 e, em seguida, clique na marca de seleção.

	![Parâmetros de runbook](./media/automation/automation_11_RunbookParams.png)
  
14.	Clique em **TRABALHOS** para verificar o status do trabalho do runbook que você iniciou e, em seguida, clique no carimbo de data/hora na coluna **INÍCIO DO TRABALHO** para exibir o resumo do trabalho.

	![Status do runbook](./media/automation/automation_12_RunbookStatus.png)

15.	Na página **RESUMO**, você pode ver o resumo, os parâmetros de entrada e a saída do trabalho.
 
	![Resumo do runbook](./media/automation/automation_13_RunbookSummary_callouts.png)

## Consulte também

- [Visão geral da Automação](http://go.microsoft.com/fwlink/p/?LinkId=392860)
- [Guia de criação de runbook](http://go.microsoft.com/fwlink/p/?LinkID=301740)
- [Fórum de Automação](http://go.microsoft.com/fwlink/p/?LinkId=390561)

