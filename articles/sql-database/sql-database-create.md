<properties 
	pageTitle="Criar um banco de dados na atualização do Banco de Dados SQL V12" 
	description="Demonstra como criar um banco de dados na atualização do Banco de Dados SQL do Azure V12" 
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
	ms.date="04/28/2015" 
	ms.author="sonalm"/>


# Criar um banco de dados no Banco de Dados SQL V12


<!--
True author is: authors="sonalmm" , ms.author="sonalm".
-->


[Inscreva-se](https://portal.azure.com) para o Banco de Dados SQL V12 [(no modo de visualização em algumas regiões)](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable) para aproveitar a última geração de Banco de Dados SQL no Microsoft Azure. Para começar, você precisa de uma assinatura do Microsoft Azure. Você pode inscrever para uma [avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial) e examinar as informações de [preços](http://azure.microsoft.com/pricing/details/sql-database).


| Criar banco de dados | Captura de tela |
| :--- | :--- |
| 1\. Entre em [http://portal.azure.com/](http://portal.azure.com/). | ![Novo Portal do Azure][1] |
| 2\. Na parte inferior da página, à esquerda, clique em **Novo**. | ![Iniciar Novo serviço][2]|
| 3\. Clique em **Bancos de Dados SQL**.| ![Serviços diferentes para selecionar][3] |
| 4\. Uma folha do **Banco de Dados SQL** será aberta. No campo **Nome**, especifique um nome do banco de dados. | ![Nome do banco de dados][4] |
| 5\. Na **Folha de banco de dados SQL**, clique em **SERVIDOR**. Uma folha **Servidor** é aberta com duas opções: você pode criar um novo servidor ou usar um servidor existente.|| ![selecione o tipo de servidor][4] |
|5a. Se você selecionar a opção **Usar um servidor existente**, selecione um servidor de sua escolha e clique em **Selecionar**. Em seguida, conclua todas as ações da etapa 6 em diante. || ![selecione um servidor na lista][5]| 
|5b. Se você selecionar **Criar um novo servidor**, a folha **Novo servidor** será aberta. Especifique o nome do servidor, o logon de administrador do servidor e a senha. Clique em **Local** para selecionar o local do servidor. | ![Criação completa de novas opções de servidor][9]| 
|5c.A folha **Novo servidor** lhe dá a opção de criar o novo servidor em atualizações do V12. Para saber mais sobre os recursos em servidores V12, examine [O que há de novo no Banco de Dados SQL V12](sql-database-v12-whats-new.md).| ![Selecione o servidor V12][6]|
|5d. Faça suas seleções na folha **Novo servidor** e clique em **OK**. Isso o levará para a folha **Banco de Dados SQL** para concluir o restante das ações para criar um banco de dados. | ![Concluir ações da folha Novo servidor][8]|
|6\. Clique em **Selecionar origem**. Os diferentes tipos de fontes que você pode selecionar para criar um banco de dados são: um banco de dados em branco, um banco de dados de exemplo ou de um backup de um banco de dados.| ![Selecione a origem para o banco de dados][10]|
|7\. Em seguida, na folha do **Banco de Dados SQL**, clique em **CAMADA DE PREÇOS**. Você pode selecionar uma das camadas de preço recomendadas ou **Exibir todas** as camadas de preços disponíveis. Depois de fazer uma escolha, clique em **Selecionar**. <p>Para obter mais informações sobre camadas de preços, consulte [Atualizar bancos de dados SQL Web/Business para novas camadas de serviço](./sql-database-upgrade-new-service-tiers/) e [Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho](http://msdn.microsoft.com/library/azure/dn741336.aspx). |![Selecione uma faixa de preço.][7]
| 8\. Em seguida, na folha **Banco de Dados SQL**, clique em **Configuração opcional**, faça as seleções e clique em **OK**. 
| 9\. Quando você seleciona o servidor existente, **Grupo de recurso** e **Assinatura** já são escolhidos por você. Na folha **Banco de Dados SQL**, você verá um ícone bloqueado ao lado de **Grupo de recurso** e **Assinatura**. Se você criar um novo servidor, poderá selecionar ou criar um grupo de recursos. Para obter mais informações, consulte [Usando grupos de recursos para gerenciar os recursos do Azure.](resource-group-overview.md)|![Especifique o grupo de recursos][11]
| 10\. Clique em **Criar**. É criado um novo banco de dados com recursos do Banco de Dados SQL V12. |![Cria um novo banco de dados][12]

## Links relacionados

- O que há de novo no [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Planejar e preparar a atualização para o Banco de Dados SQL do Azure V12](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-create/firstscreenportal.png
[2]: ./media/sql-database-create/new.png
[3]: ./media/sql-database-create/sqldatabase.png
[4]: ./media/sql-database-create/databasename.png
[5]: ./media/sql-database-create/useexistingserver.PNG
[6]: ./media/sql-database-create/v12server.PNG
[7]: ./media/sql-database-create/pricingtierdetails.png
[8]: ./media/sql-database-create/finishnewserverblade.png
[9]: ./media/sql-database-create/createnewserver.png
[10]: ./media/sql-database-create/selectsource.png
[11]: ./media/sql-database-create/resourcegroup.png
[12]: ./media/sql-database-create/create.png

 

<!---HONumber=August15_HO6-->