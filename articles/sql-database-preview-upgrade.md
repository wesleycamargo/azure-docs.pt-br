<properties 
	pageTitle="Atualização para a atualização mais recente do banco de dados SQL V12 (visualização)" 
	description="Atualização para a atualização mais recente do banco de dados SQL V12 (visualização)" 
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



# Atualização para a atualização mais recente do banco de dados SQL V12 (visualização)


[Inscreva-se](https://portal.azure.com) para a mais recente atualização do banco de dados SQL V12 para aproveitar a próxima geração do  Banco de dados SQL no Microsoft Azure. Primeiro, você precisa de uma assinatura do Microsoft Azure. Você pode inscrever para uma [avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial) e examinar as informações de [preços](http://azure.microsoft.com/pricing/details/sql-database). 

## Como atualizar um servidor no local com a atualização mais recente do banco de dados SQL ##

| Atualizar  | Captura de tela |
| :--- | :--- |
| 1. Entre em [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. Clique em **PROCURAR**. | ![Browse Services][2] |
| 3.	Clique em **Servidores SQL**. Uma lista de nomes do SQL Server é exibida. | ![Select SQL Server service][3] |
| 4. Selecione o servidor que você deseja copiar para um novo servidor com a atualização do banco de dados SQL habilitada. | ![Shows a list of SQL Servers][4] |
| 5. Clique na **Atualização de banco de dados SQL V12 mais recente**. | ![Latest preview feature][5] |
| 6. Clique em **ATUALIZAR ESTE SERVIDOR**. | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **IMPORTANTE**: Quando você seleciona a opção de atualização, seu servidor e os bancos de dados nesse servidor serão habilitados com recursos de visualização da atualização de banco de dados SQL V12 e isso não poderá ser revertido. Para atualizar servidores para a atualização do banco de dados SQL V12, você precisa de uma camada de serviço Basic, Standard ou Premium. Para obter mais informações sobre as camadas de serviço, consulte [Atualizar banco de dados dos banco de dados Web/Business para Novas camadas de serviço"](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/).

> **IMPORTANTE**: Não há suporte para replicação geográfica com a atualização de banco de dados SQL V12 (visualização). Para obter mais informações, consulte [Planejar e preparar para atualização da visualização do banco de dados SQL V12 do Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade).


Depois de clicar na opção **ATUALIZAR ESTE SERVIDOR**, a lâmina abre mostra uma mensagem sobre um processo de validação. 

- O processo de validação verifica a camada de serviço do banco de dados e verifica se a replicação geográfica está habilitada. A lâmina mostrará os resultados após a conclusão da validação. 
- Após o processo de validação ser concluído, você verá uma lista de nomes de banco de dados que exigem que você execute uma ação para atender aos requisitos de atualização para de atualização para a Atualização do banco de dados SQL V12. **Você precisará concluir as ações para cada um desses bancos de dados para poder atualizar para atualização do banco de dados SQL V12**.
- Ao clicar em cada nome de banco de dados, uma nova lâmina fornece a recomendação da camada de preço de serviço com base no seu uso atual. Também é possível procurar várias camadas de preços e selecionar aquela que atenda melhor o seu ambiente. Todos os bancos de dados que são configurados para replicação geográfica precisam ser reconfigurados para interromper a replicação. 
- Observe que uma recomendação sobre a camada de preço não será exibida se não forem encontrados dados suficientes. 

| Ação | Captura de tela |
| :--- | :--- |
| 7. Depois de concluir as ações que prepara o servidor para a atualização, digite o nome do servidor para atualizar e clique em **OK**. | ![Confirm the server name to upgrade][7] |
| 8. O processo de atualização é iniciado. A atualização pode levar até 24 horas. Durante esse tempo todos os bancos de dados neste servidor permanecerão online, mas o servidor e ações de gerenciamento de banco de dados serão restringidas. Quando o processo for concluído, o status **Habilitado** é exibido na lâmina do servidor. | ![Confirms preview features are enabled][8] |
 

# Links relacionados  #

-  [O que há de novo na atualização V12 do banco de dados SQL (visualização)](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Planejar e preparar a atualização para a atualização mais recente do banco de dados SQL V12 mais recente (visualização)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
