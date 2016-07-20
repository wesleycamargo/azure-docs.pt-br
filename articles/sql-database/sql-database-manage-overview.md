<properties
	pageTitle="Visão geral: ferramentas de gerenciamento para o Banco de Dados SQL"
	description="Compara as ferramentas e opções de gerenciamento de Banco de Dados SQL do Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="sstein"/>

# Visão geral: ferramentas de gerenciamento para o Banco de Dados SQL

Este tópico explora e compara as ferramentas e opções de gerenciamento de bancos de dados SQL do Azure para que você possa escolher a ferramenta certa para o trabalho, para sua empresa e para você. Escolher a ferramenta certa depende de quantos bancos de dados você gerencia, da tarefa e da frequência de execução de uma tarefa.

## Portal do Azure

O [Portal do Azure](https://portal.azure.com) é um aplicativo baseado na Web no qual você pode criar, atualizar e excluir bancos de dados e servidores lógicos, além de monitorar a atividade do banco de dados. Essa ferramenta será excelente se você estiver começando a usar o Azure, gerenciando uma pequena quantidade de bancos de dados, ou precisar fazer algo rapidamente.

Para obter informações mais detalhadas sobre como usar o portal, confira [Gerenciar Bancos de Dados SQL usando o Portal clássico do Azure](sql-database-manage-portal.md).

## SQL Server Management Studio e SQL Server Data Tools no Visual Studio

SQL Server Management Studio (SSMS) e SQL Server Data Tools (SSDT) no Visual Studio são ferramentas cliente que executam em seu computador e permitem a conexão, gerenciamento e desenvolvimento do banco de dados na nuvem. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), [tente usar o SSDT no Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Muitos administradores de banco de dados estão familiarizados com o SSMS, que pode ser usado com bancos de dados SQL do Azure. [Baixe a versão mais recente do SSMS](https://msdn.microsoft.com/library/mt238290) e use sempre o lançamento mais recente ao trabalhar com o banco de dados SQL do Azure. Para obter mais informações sobre como gerenciar seus bancos de dados SQL do Azure com SSMS, confira a seção [Gerenciar bancos de dados SQL usando o SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Você deve usar a versão mais recente do SQL Server Management Studio e das SQL Server Data Tools para Visual Studio para permanecer sincronizado com as atualizações do Microsoft Azure e do Banco de Dados SQL. As versões antigas dessas ferramentas não funcionarão corretamente com o Banco de Dados SQL.

## Ferramentas de linha de comando

Você pode usar ferramentas de linha de comando, como o PowerShell para gerenciar pools de banco de dados elásticos e para automatizar as implantações de recursos do Azure. A Microsoft recomenda essa ferramenta para gerenciar um grande número de bancos de dados e automatizar a implantação e alterações de recursos em um ambiente de produção.

Para obter mais informações sobre como gerenciar seus bancos de dados SQL do Azure com ferramentas de linha de comando, consulte [Gerenciamento de banco de dados SQL com o PowerShell](sql-database-command-line-tools.md)

## Ferramentas de Banco de Dados Elástico
Use as ferramentas de banco de dados elástico para executar ações como

* Executar um script T-SQL em um conjunto de bancos de dados usando um [trabalho elástico](sql-database-elastic-jobs-overview.md)
* Mover bancos de dados do modelo de multilocatário para um modelo de locatário único com a [ferramenta de divisão e mesclagem](sql-database-elastic-scale-overview-split-and-merge.md)
* Gerenciar bancos de dados em um modelo de locatário único ou um modelo de multilocatário usando a [biblioteca de cliente de dimensionamento elástico](sql-database-elastic-database-client-library.md).
 

<!---HONumber=AcomDC_0706_2016-->