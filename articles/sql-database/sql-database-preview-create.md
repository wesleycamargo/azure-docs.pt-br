<properties 
	pageTitle="Criar um banco de dados na atualização do banco de dados SQL V12 (visualização) mais recente" 
	description="Criar um banco de dados na atualização do banco de dados SQL V12 (visualização) mais recente" 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="12/11/2014" 
	ms.author="sonalm"/>


# Criar um banco de dados na atualização do banco de dados SQL V12 (visualização) mais recente

[Inscreva-se](https://portal.azure.com) para a mais recente atualização do banco de dados SQL V12 para aproveitar a próxima geração do banco de dados SQL no Microsoft Azure. Para começar, você precisa de uma assinatura do Microsoft Azure. Você pode inscrever para uma [avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial) e examinar as informações de [preços](http://azure.microsoft.com/pricing/details/sql-database). 


| Criar banco de dados | Captura de tela |
| :--- | :--- |
| 1. Entre em [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. Na parte inferior da página, à esquerda, clique em **Novo**. | ![Initiate New service][2]|
| 3. Clique em **Bancos de Dados SQL**.| ![Different services to select from][3] |
| 4. Uma lâmina do **banco de dados SQL** será aberta. No campo **Nome**, especifique um nome do banco de dados. | ![Name the database][4] |
| 5. Na **lâmina de banco de dados SQL**, clique em **SERVIDOR**. Uma lâmina **Servidor** é aberta com duas opções: você pode criar um novo servidor ou usar um servidor existente.| ![select type of server][4] |
|5a. Se você selecionar a opção **Usar um servidor existente**, selecione um servidor de sua escolha e clique em **Selecionar**. Em seguida, conclua todas as ações da etapa 6 em diante. || ![select a server from the list][5]| 
|5b.   Se você selecionar **Criar um novo servidor**, a lâmina **Novo servidor** será aberta. Especifique o nome do servidor, o logon de administrador do servidor e a senha. Clique em **Local**   para selecionar o local do servidor. | ![Complete create new server options][9]| 
|5c.A lâmina **Novo servidor** lhe dá a opção de criar o novo servidor em atualizações do V12. Para saber mais sobre os recursos em servidores V12, examine [O que há de novo no Banco de Dados SQL do Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/). || ![Select V12 server][6]|
|5d. Faça suas seleções na lâmina **Novo servidor** e clique em **OK**. Isso o levará para a lâmina **banco de dados SQL** para concluir o restante das ações para criar um banco de dados. | ![Complete New Server blade actions][8]|
|6. Clique em **Selecionar fonte**. Os diferentes tipos de fontes que você pode selecionar para criar um banco de dados são: um banco de dados em branco, um banco de dados de exemplo ou de um backup de um banco de dados.| ![Select the source for the database][10]|
|7. Em seguida, na lâmina do **banco de dados SQL**, clique em **CAMADA DE PREÇOS**. Você pode selecionar a camada de preço recomendada ou procurar todas as camadas de preços disponíveis. Depois de fazer uma escolha, clique em **Selecionar**. <p> Para obter mais informações sobre camadas de preços, consulte [Atualização de banco de dados SQL Web/Business para novas camadas de serviço](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) e [Camadas de serviço de banco de dados SQL do Azure e níveis de desempenho](http://msdn.microsoft.com/library/azure/dn741336.aspx). |![Select a pricing tier][7]
| 8. Em seguida, na **banco de dados SQL** lâmina, clique em **configuração opcional**, faça as seleções e clique em **OK**. 
| 9. Na lâmina **banco de dados SQL**, clique em **Grupo de recursos**. Crie um novo grupo de recursos ou selecione um grupo de recursos existente na lista. Clique em **OK**. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups).|![Specify Resource group][11]
| 10. Na lâmina **banco de dados SQL**, clique em **ASSINATURA**.Uma lâmina **Assinatura** será aberta. Selecione uma assinatura do Azure que ofereça suporte a seleção de servidor que você fez. Observe que com base na seleção de assinatura, poderá alterar as configurações do servidor.| ![Select subscription.][13]
| 11. Clique em **Criar**. Um novo banco de dados com recursos da atualização do banco de dados SQL V12 é criado. |![Creates a new database][12]

# Links relacionados  #

-  [O que há de novo no banco de dados SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Planejar e preparar a atualização para V12 de banco de dados do SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
 