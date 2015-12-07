<properties 
	pageTitle="Visão geral: ferramentas de gerenciamento para o Banco de Dados SQL" 
	description="Compara as ferramentas e opções de gerenciamento de Banco de Dados SQL do Azure" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="vinsonyu"/>

# Visão geral: ferramentas de gerenciamento para o Banco de Dados SQL

Este tópico explora e compara as ferramentas e opções de gerenciamento de bancos de dados SQL para que você pode escolher a ferramenta certa para o trabalho, sua empresa e você. Escolher a ferramenta certa depende de quantos bancos de dados você gerencia, da tarefa e da frequência de execução de uma tarefa.



## Portal do Azure


O [Portal do Azure](http://portal.azure.com) é um portal de gerenciamento baseado na Web, em que você pode criar, atualizar e excluir servidores lógicos e bancos de dados e monitorar a atividade do banco de dados. Essa ferramenta será excelente se você estiver começando a usar o Azure, gerenciando uma pequena quantidade de bancos de dados, ou precisar fazer algo rapidamente.

Para obter informações mais detalhadas sobre como usar o portal, confira [Gerenciar Bancos de Dados SQL usando o Portal do Azure](sql-database-manage-portal.md).

## SQL Server Management Studio e SQL Server Data Tools no Visual Studio


SQL Server Management Studio (SSMS) e SQL Server Data Tools (SSDT) no Visual Studio são ferramentas cliente que executam em seu computador e permitem a conexão, gerenciamento e desenvolvimento do banco de dados na nuvem. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), [tente usar o SSDT no Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Se precisar de recursos avançados do SQL que ainda não estejam disponíveis no SSDT, como gerenciar bancos de dados SQL Server em ambientes híbridos, você pode usar o SSMS.

Para obter mais informações sobre como gerenciar seus bancos de dados SQL do Azure com SSMS, consulte [Gerenciar bancos de dados SQL usando o SSMS](sql-database-manage-azure-ssms.md)


## Ferramentas de linha de comando

Você pode usar ferramentas de linha de comando, como o PowerShell para gerenciar pools de banco de dados elásticos e para automatizar as implantações de recursos do Azure. A Microsoft recomenda essa ferramenta para gerenciar um grande número de bancos de dados e automatizar a implantação e alterações de recursos em um ambiente de produção.

Para obter mais informações sobre como gerenciar seus bancos de dados SQL do Azure com ferramentas de linha de comando, consulte [Gerenciamento de banco de dados SQL com o PowerShell](sql-database-command-line-tools.md)
 

<!---HONumber=AcomDC_1125_2015-->