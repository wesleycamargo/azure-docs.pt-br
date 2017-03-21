---
title: Ferramentas para gerenciar e desenvolver com o Banco de Dados SQL do Azure | Microsoft Docs
description: "Apresenta as ferramentas de gerenciamento e desenvolvimento e as opções do Banco de Dados SQL do Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 9b0a3a314e576db2133e5c63fada43bb11a4e520
ms.lasthandoff: 03/04/2017


---
# <a name="overview-tools-to-manage--develop-with-azure-sql-database"></a>Visão geral: Ferramentas para gerenciar e desenvolver com o Banco de Dados SQL do Azure
Este tópico apresenta as ferramentas de gerenciamento e desenvolvimento com bancos de dados SQL do Azure.

> [!IMPORTANT]
> Este conjunto de documentações inclui guias de Início Rápido, de Amostras e de Instruções que mostram como gerenciar e desenvolver com o Banco de Dados SQL do Azure usando as ferramentas apresentadas nos parágrafos a seguir. Use o painel de navegação esquerdo e a caixa de filtro para encontrar um conteúdo específico do portal do Azure, PowerShell e T-SQL.
>

## <a name="azure-portal"></a>Portal do Azure
O [Portal do Azure](https://portal.azure.com) é um aplicativo baseado na Web no qual você pode criar, atualizar e excluir bancos de dados e servidores lógicos, além de monitorar a atividade do banco de dados. Essa ferramenta é excelente se você está começando a usar o Azure, gerenciando poucos bancos de dados ou precisar fazer algo rapidamente.

## <a name="sql-server-management-studio-and-transact-sql"></a>SQL Server Management Studio e Transact-SQL
O SSMS (SQL Server Management Studio) é uma ferramenta de cliente que é executada no computador para o gerenciamento do banco de dados na nuvem usando o Transact-SQL. Muitos administradores de banco de dados estão familiarizados com o SSMS, que pode ser usado com bancos de dados SQL do Azure. [Baixe a versão mais recente do SSMS](https://msdn.microsoft.com/library/mt238290) e use sempre o lançamento mais recente ao trabalhar com o banco de dados SQL do Azure. 

> [!IMPORTANT]
> Sempre use a última versão do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290).
>  

## <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools no Visual Studio
O SSDT (SQL Server Data Tools) é uma ferramenta de cliente que é executada no computador para o desenvolvimento do banco de dados na nuvem. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), [tente usar o SSDT no Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx).  

> [!IMPORTANT]
> Sempre use a última versão do [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) para permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL.
>  
## <a name="powershell"></a>PowerShell
É possível usar o PowerShell para gerenciar bancos de dados e pools elásticos, bem como para automatizar implantações de recursos do Azure. A Microsoft recomenda essa ferramenta para gerenciar um grande número de bancos de dados e automatizar a implantação e alterações de recursos em um ambiente de produção.

## <a name="elastic-database-tools"></a>Ferramentas de Banco de Dados Elástico
Use as ferramentas de banco de dados elástico para executar ações como 

* Executar um script T-SQL em um conjunto de bancos de dados usando um [trabalho elástico](sql-database-elastic-jobs-overview.md)
* Mover bancos de dados do modelo de multilocatário para um modelo de locatário único com a [ferramenta de divisão e mesclagem](sql-database-elastic-scale-overview-split-and-merge.md)
* Gerenciar bancos de dados em um modelo de locatário único ou um modelo de multilocatário usando a [biblioteca de cliente de dimensionamento elástico](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Recursos adicionais
* [Gerenciador de Recursos do Azure](https://azure.microsoft.com/features/resource-manager/)
* [Automação do Azure](https://azure.microsoft.com/documentation/services/automation/)
* [Agendador do Azure](https://azure.microsoft.com/documentation/services/scheduler/)


