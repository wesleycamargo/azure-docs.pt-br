<properties 
	pageTitle="Introdução à Automação do Azure" 
	description="Saiba como importar e executar uma tarefa de automação no Azure." 
	services="automation" 
	documentationCenter="" 
	authors="bwren" 
	manager="stevenka" 
	editor=""/>

<tags 
	ms.service="automation" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/13/2015" 
	ms.author="bwren"/>


# Introdução à Automação do Azure

A Automação do Microsoft Azure fornece uma maneira para os desenvolvedores automatizarem tarefas manuais, longas, sujeitas a erros e repetidas com frequência que são executadas em um ambiente de nuvem. Você pode criar, monitorar, gerenciar e implantar recursos em seu ambiente do Azure usando runbooks que, na realidade, são fluxos de trabalho do Windows PowerShell. Para obter mais informações sobre Automação, consulte o [Guia de Visão Geral de Automação](http://go.microsoft.com/fwlink/p/?LinkId=392861). 

Este tutorial apresenta e explica as etapas para importar um runbook "Hello World" de exemplo na Automação do Azure, executá-lo e, em seguida, exibir sua saída.

>[WACOM.NOTE] Para aprender a automatizar as operações do Azure usando [cmdlets do PowerShell do Azure](http://msdn.microsoft.com/library/jj156055.aspx) consulte <a href="http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/">Automação do Azure: Autenticação do Azure usando o Active Directory do Azure</a>.

## Exemplos e runbooks utilitários

A equipe de Automação do Azure criou vários exemplos de runbook para ajudar você a começar a trabalhar com a Automação.  Esses exemplos abrangem os conceitos básicos da Automação e têm o objetivo de ajudá-lo a aprender como escrever seus próprios runbooks.  

A equipe de Automação também criou os runbooks utilitários que você pode usar como blocos de construção para tarefas maiores de Automação.  

>[WACOM.NOTE] É uma prática recomendada escrever runbooks pequeno, modulares e reutilizáveis. Também recomendamos que você crie seus próprios runbooks utilitários para os cenários mais usados quando estiver familiarizado com a Automação.  

Você pode exibir e baixar o exemplo da equipe de Automação e os runbooks utilitários no [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=393029) ou importá-los diretamente da [Runbook Gallery](http://aka.ms/runbookgallery). 

## A comunidade e os comentários da Automação

Os runbooks da comunidade e de outras equipes da Microsoft também são publicados no [Script Center](http://go.microsoft.com/fwlink/?LinkID=391681) e na [Runbook Gallery](http://aka.ms/runbookgallery). 

<strong>Fornecer comentários</strong>  Se você estiver procurando por uma solução de runbook de automação ou por um módulo de integração, poste uma Solicitação de Script no Script Center. Se você tiver uma ideia para um novo recurso de Automação, poste-a no [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback).

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Etapas de alto nível para este tutorial

1. [Criar uma Conta de Automação](#automationaccount)
2. [Importar runbook da Runbook Gallery](#importrunbook)
3. [Publicar Runbook](#publishrunbook)
4. [Iniciar Runbook](#startrunbook)


## <a name="automationaccount"></a>Criar uma conta de automação

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2.	No Portal de Gerenciamento, clique em **Criar uma Conta de Automação**.

	>[WACOM.NOTE] Se você já tiver criado uma conta de automação, poderá pular para a etapa 4.

	![Create Account](./media/automation/automation_01_CreateAccount.png)

3.	Na página **Adicionar uma Nova Conta de Automação**, digite um nome para a conta e, em seguida, clique na marca de seleção.

	![Add New Account](./media/automation/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>Importar runbook da Runbook Gallery
 
4.	Na página **Automação**, clique na nova conta que você acabou de criar.
 
	![New Account](./media/automation/automation_03_NewAutoAcct.png)

5.	Clique em **RUNBOOKS**.

	![Runbooks Tab](./media/automation/automation_04_RunbooksTab.png)
  
6.	Clique em **Novo** > **Runbook** > **Da Galeria**.

	![Runbook Gallery](./media/automation/automation_05_ImportGallery.png)

7.  Selecione a categoria **Tutorial** e depois **Hello World para Automatização do Azure**. Clique no botão com a seta para a direita.

	![Import Runbook](./media/automation/automation_06_ImportRunbook.png)

8.  Revise o conteúdo do runbook e clique no botão com a seta para a direita.

	![Runbook Definition](./media/automation/automation_07_RunbookDefinition.png)

8.	Revise os detalhes do runbook e clique no botão com a marca de seleção.

	![Runbook Details](./media/automation/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>Publicar Runbook 

9.	Quando a importação do runbook estiver concluída, clique em **Write-HelloWorld**.

	![Imported Runbook](./media/automation/automation_07_ImportedRunbook.png)

9.	Clique em **AUTOR** e, em seguida, clique em **RASCUNHO**.  

	Você pode modificar o conteúdo de um runbook no modo de Rascunho. Para este runbook, você não precisa fazer nenhuma modificação.

	![Author Draft](./media/automation/automation_08_AuthorDraft.png)  
 
10.	Clique em **PUBLICAR** para promover o runbook para que esteja pronto para uso em produção.

	![Publish](./media/automation/automation_085_Publish.png)
   
11.	Quando receber um prompt para salvar e publicar o runbook, clique em **Sim**.
 
	![Save and Pub prompt](./media/automation/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>Iniciar Runbook

12.	Com o runbook **Write-HelloWorld** aberto, clique em **INICIAR**.

	![Published](./media/automation/automation_10_PublishStart.png)
 
13.	Na página **Especificar valores de parâmetros de Runbook**, digite um **Nome** que será usado como um parâmetro de entrada para o script de Write-HelloWorld.ps1 e, em seguida, clique na marca de seleção.

	![Runbook Parameters](./media/automation/automation_11_RunbookParams.png)
  
14.	Clique em **TRABALHOS** para verificar o status do trabalho do runbook que você iniciou e, em seguida, clique no carimbo de data/hora na coluna **INÍCIO DO TRABALHO** para exibir o resumo do trabalho.

	![Runbook Status](./media/automation/automation_12_RunbookStatus.png)

15.	Na página **RESUMO**, você pode ver o resumo, os parâmetros de entrada e a saída do trabalho.
 
	![Runbook Summary](./media/automation/automation_13_RunbookSummary_callouts.png)


# Gerenciando os serviços do Azure a partir de um Runbook 
O exemplo acima mostra um runbook simples que não gerencia os serviços do Azure. Os [cmdlets do Azure](http://msdn.microsoft.com/library/jj156055.aspx) exigem autorização do Azure. Siga as instruções em [Automação do Azure: Autenticação do Azure usando o Active Directory do Azure](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/) para configurar sua assinatura do Azure para o gerenciamento por meio da Automação do Azure.

# Consulte também

- [Visão geral da Automação](http://go.microsoft.com/fwlink/p/?LinkId=392860)
- [Guia de criação de runbook](http://go.microsoft.com/fwlink/p/?LinkID=301740)
- [Fórum de Automação](http://go.microsoft.com/fwlink/p/?LinkId=390561)
- [Automação do Azure: Autenticação do Azure usando o Active Directory do Azure](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)

<!--HONumber=35.2-->

<!--HONumber=46--> 
