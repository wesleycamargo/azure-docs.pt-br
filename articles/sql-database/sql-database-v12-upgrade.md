<properties 
	pageTitle="Atualizar para o Banco de Dados SQL V12" 
	description="Explica como atualizar para o Banco de Dados SQL V12 do Azure, de uma versão anterior do Banco de Dados SQL do Azure." 
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
	ms.date="05/15/2015" 
	ms.author="sonalm"/>


# Atualizar para o Banco de Dados SQL V12 in-loco


[Inscreva-se](https://portal.azure.com) no Banco de Dados SQL V12 para aproveitar a última geração do Banco de Dados SQL no Microsoft Azure. Primeiro, você precisa de uma assinatura do Microsoft Azure. Inscreva-se para obter uma versão de [avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial) e analisar as informações de [preço](http://azure.microsoft.com/pricing/details/sql-database).


## Etapas para atualizar para o Banco de Dados SQL V12


| Etapas de atualização | Captura de tela |
| :--- | :--- |
| 1. Entre em [http://portal.azure.com/](http://portal.azure.com/). | ![Novo Portal do Azure][1] |
| 2. Clique em **PROCURAR**. | ![Procurar Serviços][2] |
| 3. Clique em **Servidores SQL**. Uma lista de nomes do SQL Server é exibida. | ![Selecionar serviço do SQL Server][3] |
| 4. Selecione o servidor que você deseja copiar para um novo servidor com a atualização do banco de dados SQL habilitada. | ![Mostra uma lista de servidores SQL][4] |
| 5. Clique em **Atualização Mais Recente do Banco de Dados SQL V12**. | ![Recurso de visualização mais recente][5] |
| 6. Clique em **ATUALIZAR ESTE SERVIDOR**. | ![Atualiza o SQL Server para a visualização][6] |


> [AZURE.NOTE]Quando você seleciona a opção de atualização, seu servidor e os bancos de dados nesse servidor serão habilitados com recursos do Banco de Dados SQL V12, e isso não poderá ser revertido. Para atualizar servidores para o Banco de Dados SQL V12, você precisa de uma camada de serviço Basic, Standard ou Premium. Para saber mais sobre as camadas de serviço, confira [Atualizar banco de dados Web/Business do Banco de Dados SQL para novas camadas de serviço](sql-database-upgrade-new-service-tiers.md).


> [AZURE.IMPORTANT]Não há suporte para replicação geográfica com o Banco de Dados SQL V12 (visualização). Para saber mais, confira [Planejar e se preparar para atualizar para a visualização do Banco de Dados SQL V12](sql-database-v12-plan-prepare-upgrade.md).


Depois de clicar na opção **ATUALIZAR ESTE SERVIDOR**, a folha que é aberta mostra uma mensagem sobre um processo de validação.


- O processo de validação verifica a camada de serviço do banco de dados e verifica se a replicação geográfica está habilitada. A lâmina mostrará os resultados após a conclusão da validação. 
- Depois que o processo de validação for concluído, você verá uma lista de nomes de banco de dados que exigem uma ação para atender aos requisitos de atualização do Banco de Dados SQL V12.
 - **Você precisará concluir as ações em cada um desses bancos de dados para poder atualizar para o Banco de Dados SQL V12**.
- Ao clicar em cada nome de banco de dados, uma nova lâmina fornece a recomendação da camada de preço de serviço com base no seu uso atual. Também é possível procurar várias camadas de preços e selecionar aquela que atenda melhor o seu ambiente. Todos os bancos de dados que são configurados para replicação geográfica precisam ser reconfigurados para interromper a replicação. 
- Observe que uma recomendação sobre a camada de preço não será exibida se não forem encontrados dados suficientes. 


| Ação | Captura de tela |
| :--- | :--- |
| 7. Depois de concluir as ações que preparam o servidor para a atualização, digite o nome do servidor a ser atualizado e clique em **OK**. | ![Confirmar o nome do servidor para atualização][7] |
| 8. O processo de atualização é iniciado. A atualização pode levar até 24 horas. Durante esse tempo todos os bancos de dados neste servidor permanecerão online, mas o servidor e ações de gerenciamento de banco de dados serão restringidas. Quando o processo for concluído, o status **Habilitado** será exibido na folha do servidor. | ![Confirma se os recursos de visualização estão habilitados][8] |


## Cmdlets do Powershell


Os cmdlets do Powershell estão disponíveis para iniciar, parar ou monitorar uma atualização para o Banco de Dados SQL do Azure de V11 para V12 ou de qualquer outra versão anterior à V12.


Para ver a documentação de referência sobre esses cmdlets do Powershell, confira:


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


O cmdlet Stop- significa cancelar, e não pausar. Não há como retomar uma atualização, a não ser começá-la novamente. O cmdlet Stop- limpa e libera todos os recursos apropriados.


## Links relacionados

-  [Novidades no Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Planejar e se preparar para atualizar para o Banco de Dados SQL V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/firstscreenportal.png
[2]: ./media/sql-database-v12-upgrade/browse.png
[3]: ./media/sql-database-v12-upgrade/sqlserver.png
[4]: ./media/sql-database-v12-upgrade/sqlserverlist.png
[5]: ./media/sql-database-v12-upgrade/latestprview.png
[6]: ./media/sql-database-v12-upgrade/upgrade.png
[7]: ./media/sql-database-v12-upgrade/typeservername.png
[8]: ./media/sql-database-v12-upgrade/enabled.png

<!---HONumber=58--> 