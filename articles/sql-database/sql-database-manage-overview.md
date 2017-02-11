---
title: "Visão geral: ferramentas de gerenciamento para o Banco de Dados SQL | Microsoft Docs"
description: "Compara as ferramentas e opções de gerenciamento de Banco de Dados SQL do Azure"
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
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 915292a191da82af9e24e89460d46dc61a062b18


---
# <a name="overview-management-tools-for-sql-database"></a>Visão geral: ferramentas de gerenciamento para o Banco de Dados SQL
Este tópico explora e compara as ferramentas e opções de gerenciamento de bancos de dados SQL do Azure para que você possa escolher a ferramenta certa para o trabalho, para sua empresa e para você. Escolher a ferramenta certa depende de quantos bancos de dados você gerencia, da tarefa e da frequência de execução de uma tarefa.

## <a name="azure-portal"></a>Portal do Azure
O [Portal do Azure](https://portal.azure.com) é um aplicativo baseado na Web no qual você pode criar, atualizar e excluir bancos de dados e servidores lógicos, além de monitorar a atividade do banco de dados. Essa ferramenta é excelente se você está começando a usar o Azure, gerenciando poucos bancos de dados ou precisar fazer algo rapidamente.

Para obter informações sobre como usar o portal, consulte [Gerenciar Bancos de Dados SQL usando o Portal do Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio e SQL Server Data Tools no Visual Studio
SSMS (SQL Server Management Studio) e SSDT (SQL Server Data Tools) são ferramentas cliente executadas em seu computador para gerenciar e desenvolver seu banco de dados na nuvem. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), [tente usar o SSDT no Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Muitos administradores de banco de dados estão familiarizados com o SSMS, que pode ser usado com bancos de dados SQL do Azure. [Baixe a versão mais recente do SSMS](https://msdn.microsoft.com/library/mt238290) e use sempre o lançamento mais recente ao trabalhar com o banco de dados SQL do Azure. Para obter mais informações sobre como gerenciar seus bancos de dados SQL do Azure com SSMS, confira a seção [Gerenciar bancos de dados SQL usando o SSMS](sql-database-manage-azure-ssms.md).

> [!IMPORTANT]
> Sempre use a versão mais recente do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) e do [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) para permanecer sincronizado com as atualizações do Microsoft Azure e do Banco de Dados SQL.
>  

## <a name="powershell"></a>PowerShell
É possível usar o PowerShell para gerenciar bancos de dados e pools elásticos, bem como para automatizar implantações de recursos do Azure. A Microsoft recomenda essa ferramenta para gerenciar um grande número de bancos de dados e automatizar a implantação e alterações de recursos em um ambiente de produção.

Para obter mais informações, consulte [Gerenciar o banco de dados SQL com o PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Ferramentas de Banco de Dados Elástico
Use as ferramentas de banco de dados elástico para executar ações como 

* Executar um script T-SQL em um conjunto de bancos de dados usando um [trabalho elástico](sql-database-elastic-jobs-overview.md)
* Mover bancos de dados do modelo de multilocatário para um modelo de locatário único com a [ferramenta de divisão e mesclagem](sql-database-elastic-scale-overview-split-and-merge.md)
* Gerenciar bancos de dados em um modelo de locatário único ou um modelo de multilocatário usando a [biblioteca de cliente de dimensionamento elástico](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Recursos adicionais
* [Gerenciador de Recursos do Azure](https://azure.microsoft.com/features/resource-manager/)
* [Automação do Azure](https://azure.microsoft.com/documentation/services/automation/)
* [Agendador do Azure](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Dec16_HO2-->


