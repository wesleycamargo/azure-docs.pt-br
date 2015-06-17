<properties 
	pageTitle="Problemas de conectividade de Banco de Dados SQL do Azure" 
	description="Identificação e a determinação de falhas de conexão do Banco de Dados SQL." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="04/14/2015" 
	ms.author="sstein"/>


# Problemas de conexão de Banco de Dados SQL.

Este artigo fornece uma visão geral sobre como usar várias soluções de problemas quando não é possível se conectar ao Banco de Dados SQL do Azure.


## Determine se o problema de conectividade é específico para um único aplicativo ou se simplesmente não é possível se conectar ao banco de dados?

Use o SQL Server Management Studio ou o SQLCMD. EXE para verificar se é possível se conectar ao banco de dados.

- Para obter instruções sobre como se conectar ao Banco de Dados SQL com o SQL Server Management Studio (SSMS), consulte [Como se conectar a um Banco de Dados SQL do Azure com o SSMS](sql-database-connect-to-database.md).
- Para obter instruções sobre como se conectar ao Banco de Dados SQL com o SQLCMD, consulte [Como: conectar-se ao Banco de Dados SQL do Azure usando sqlcmd](https://msdn.microsoft.com/library/azure/ee336280.aspx).



## O aplicativo está tentando se conectar ao Banco de Dados SQL em execução no Azure (por exemplo, é o aplicativo que está apresentando falha ao se conectar ao banco de dados, um serviço de nuvem ou aplicativo Web)?

Garanta que essa regra de firewall permite que todos os serviços do Azure estejam habilitados para o servidor/banco de dados.

- Para obter informações sobre regras de firewall e habilitar conexões do Azure, consulte [Firewall de Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure).



## O aplicativo está tentando se conectar ao Banco de Dados SQL de uma rede privada?

Garanta de que as regras de firewall para permitir o acesso de rede(s) específica(s) esteja habilitado para o servidor/banco de dados.

- Para obter informações gerais sobre regras de firewall, consulte [Firewall de Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).
- Para obter instruções sobre como configurar regras de firewall, consulte [Como: definir configurações de Firewall (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).


## Se as regras de firewall estiverem configuradas corretamente, tente o passo a passo orientado de [Solucionar problemas de conectividade o Banco de Dados SQL do Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).

O artigo de suporte acima fornece ajuda para os seguintes problemas de conectividade de Banco de Dados SQL:

- Não é possível abrir conexão com o servidor devido às configurações de firewall 
- O servidor não foi encontrado ou não estava acessível 
- Não é possível fazer login no servidor 
- Erros de tempo limite de conexão 
- Erros transitórios 
- Conexão encerrada porque foi atingido um limite definido pelo sistema 
- Conexões com falha do SQL Server Management Studio (SSMS) 


## Informações adicionais

- Para obter informações adicionais sobre a conexão ao Banco de Dados SQL, consulte [Diretrizes de conexão com o Banco de Dados SQL do Azure de forma programática](https://msdn.microsoft.com/library/azure/ee336282.aspx).   

- É possível encontrar detalhes sobre erros de conexão específicos na seção **Erros de perda de conexão** em [Mensagens de erro (banco de dados SQL do Azure)](https://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors).

- O acesso aos dados de eventos de conexão pode ser feito por meio de consulta a eventos de conectividade usando-se a exibição [**Sys. event_log (banco de dados do SQL Azure)**](https://msdn.microsoft.com/library/dn270018.aspx).

- O acesso às métricas de eventos de conectividade de banco de dados pode ser feito por meio de consulta à exibição [**Sys. database_connection_stats (banco de dados do SQL Azure)**](https://msdn.microsoft.com/library/dn269986.aspx).

<!---HONumber=58--> 