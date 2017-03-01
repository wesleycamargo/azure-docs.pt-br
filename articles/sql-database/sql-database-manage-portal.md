---
title: Gerenciar um Banco de Dados SQL do Azure usando o Portal do Azure | Microsoft Docs
description: "Referência rápida sobre como usar o Portal do Azure para gerenciar um banco de dados relacional na nuvem usando o Portal do Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3a56e9de-c21a-40ba-9a35-958172cb4e5b
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 130f2341eca570a982ab5c22ba429f6b48006f90
ms.lasthandoff: 02/17/2017


---
# <a name="manage-azure-sql-databases-using-the-azure-portal"></a>Gerenciar Bancos de Dados SQL do Azure usando o portal do Azure

O [portal do Azure](https://portal.azure.com/) permite que você crie, monitore e gerencie servidores e bancos de dados SQL Azure. Este artigo fornece uma descrição rápida e links para os detalhes das tarefas mais comuns.

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar um firewall baseado em servidor, exibir propriedades do servidor, conectar-se usando o SQL Server Management Studio, consultar o banco de dados mestre, criar um banco de dados de exemplo e um banco de dados em branco, consultar propriedades de banco de dados, conectar-se usando o SQL Server Management Studio e consultar o banco de dados de exemplo, confira [Tutorial de Introdução](sql-database-get-started.md).

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Exibir seus bancos de dados, servidores e pools SQL do Azure
Para exibir os serviços disponíveis do Banco de Dados SQL, clique em **Mais serviços** e digite **SQL** na caixa de pesquisa:

![Banco de dados SQL](./media/sql-database-manage-portal/sql-services.png)

## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Como criar ou exibir Bancos de Dados SQL do Azure?
Para abrir a folha **Bancos de Dados SQL**, clique em **Bancos de Dados SQL** e clique no banco de dados com o qual você deseja trabalhar ou clique em **+Adicionar** para criar um Banco de Dados SQL. Para obter detalhes, consulte [Seu primeiro Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-get-started.md).

![Bancos de dados SQL](./media/sql-database-manage-portal/sql-databases.png)

## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Como criar ou exibir os servidores do SQL Azure?
Para abrir a folha **Servidores SQL**, clique em **Servidores SQL** e clique no servidor com o qual você deseja trabalhar ou clique em **+Adicionar** para criar um servidor SQL. Para obter detalhes, consulte [Seu primeiro Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-get-started.md).

![Servidores SQL](./media/sql-database-manage-portal/sql-servers.png)

## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Como criar ou exibir os pools elásticos de SQL?
Para abrir a folha **Pools elásticos de SQL**, clique em **Pools elásticos de SQL** e clique no pool com o qual você deseja trabalhar, ou clique em **+Adicionar** para criar um pool. Para obter detalhes, confira [Criar um pool elástico com o Portal do Azure](sql-database-elastic-pool-manage-portal.md).

![Pools elásticos SQL](./media/sql-database-manage-portal/elastic-pools.png)

## <a name="how-do-i-update-or-view-sql-database-settings"></a>Como atualizar ou exibir as configurações de banco de dados SQL?
Para exibir ou atualizar as configurações de banco de dados, clique na configuração desejada na folha do banco de dados SQL:

![Configurações do Banco de Dados SQL](./media/sql-database-manage-portal/settings.png)

## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Como localizar um nome de servidor totalmente qualificado de bancos de dados SQL?
Para exibir o nome de seu servidor de bancos de dados, clique em **Visão geral** na folha **Banco de Dados SQL** e anote o nome do servidor:

![Configurações do Banco de Dados SQL](./media/sql-database-manage-portal/server-name.png)

## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Como gerenciar as regras de firewall para controlar o acesso a meu banco de dados e servidor do SQL?
Para exibir, criar ou atualizar regras de firewall, clique em **Definir firewall do servidor** na folha **Banco de Dados SQL**. Para obter detalhes, confira [Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md).

![regras de firewall](./media/sql-database-manage-portal/sql-database-firewall.png)

## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Como posso alterar meu nível de desempenho ou camada de serviço do Banco de Dados SQL?
Para atualizar a camada de serviço ou o nível de desempenho de um banco de dados SQL, clique em **Tipo de preços (escalar DTUs)** na folha **Banco de Dados SQL**. Para obter detalhes, confira [Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL](sql-database-service-tiers.md).

![tipos de preço](./media/sql-database-manage-portal/pricing-tier.png)

## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Como configurar a auditoria e detecção de ameaças para um banco de dados SQL?
Para configurar a detecção de ameaças e a auditoria para um banco de dados SQL, clique em **Auditoria e Detecção de ameaças** na folha **Banco de Dados SQL**. Para obter detalhes, consulte [Auditoria de banco de dados](sql-database-auditing.md) e [Introdução à detecção de ameaças do Banco de Dados SQL](sql-database-threat-detection.md).

## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Como configurar máscara de dados dinâmicos para um banco de dados SQL?
Para configurar a máscara de dados dinâmica para um banco de dados SQL, clique em **Máscara de dados dinâmicos** na folha **Banco de dados SQL**. Para obter detalhes, confira [Introdução ao mascaramento de dados dinâmicos do banco de dados SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Como configurar a TDE (Transparent Data Encryption) para um banco de dados SQL?
Para configurar a Transparent Data Encryption para um banco de dados SQL, clique em **Transparent Data Encryption** na folha **Banco de Dados SQL**. Para obter detalhes, consulte [Habilitar TDE em um banco de dados usando o portal](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Como exibir ou alterar o tamanho máximo de um banco de dados SQL?
Para exibir ou alterar o tamanho do banco de dados SQL, clique em **Tamanho de banco de dados** na folha **Banco de dados SQL**. Atualize o tamanho máximo de um banco de dados alterando a camada de serviço ou o nível de desempenho. Para obter detalhes, confira [Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL](sql-database-service-tiers.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Como monitorar e melhorar o desempenho de um banco de dados SQL?
Para monitorar e melhorar as características de desempenho de um banco de dados SQL, clique em **Visão geral do desempenho** na folha **Banco de dados SQL**. Para obter detalhes, consulte [Análise de desempenho do Banco de Dados SQL](sql-database-performance.md).

## <a name="how-do-i-configure-geo-replication"></a>Como configurar a replicação geográfica?
Para configurar a replicação geográfica para um banco de dados SQL, clique em **Replicação geográfica** na folha **Banco de dados SQL**. Para obter detalhes, confira [Configurar a replicação geográfica para o Banco de Dados SQL do Azure com o Portal do Azure](sql-database-geo-replication-portal.md).

## <a name="how-do-i-fail-over-to-a-geo-replicated-sql-database"></a>Como fazer um failover para um banco de dados SQL com replicação geográfica?
Para realizar um failover para um secundário replicado geograficamente, clique em **Replicação geográfica** na folha **Banco de Dados SQL** e clique em **Failover**. Para obter detalhes, confira [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o Portal do Azure](sql-database-geo-replication-failover-portal.md).

## <a name="how-do-i-copy-a-sql-database"></a>Como copiar um banco de dados SQL?
Para copiar um banco de dados SQL, clique em **Copiar** na folha **Banco de Dados SQL**. Para obter detalhes, confira [Copiar um Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-copy-portal.md).

![Configurações do Banco de Dados SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Como arquivar um Banco de Dados SQL do Azure em um arquivo BACPAC?
Para criar um BACPAC de um banco de dados SQL, clique em **Exportar** na folha **Banco de Dados SQL**. Para obter detalhes, confira [Arquivar um Banco de Dados SQL do Azure em um arquivo BACPAC usando o Portal do Azure](sql-database-export.md).

![exportar banco de dados SQL](./media/sql-database-manage-portal/sql-database-export.png)

## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Como restaurar um banco de dados SQL para um ponto anterior no tempo?
Para restaurar um banco de dados SQL, clique em **Restaurar** na folha **Banco de Dados SQL**. Para obter detalhes, confira [Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o Portal do Azure](sql-database-point-in-time-restore-portal.md).

![Configurações do Banco de Dados SQL](./media/sql-database-manage-portal/sql-database-restore.png)

## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Como criar um arquivo do banco de dados SQL do Azure de um arquivo BACPAC?
Para criar um banco de dados SQL de um arquivo BACPAC, clique em **Importar banco de dados** na folha **SQL Server**. Para obter detalhes, confira [Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure](sql-database-import-portal.md).

![SQL Server](./media/sql-database-manage-portal/server-commands.png)

## <a name="how-do-i-restore-a-deleted-sql-database"></a>Como restaurar um banco de dados SQL excluído?
Para restaurar um banco de dados SQL excluído, clique em **Bancos de dados excluídos** na folha **SQL Server** (o SQL Server que continha o banco de dados excluído). Para obter detalhes, confira [Restaurar um Banco de Dados SQL do Azure excluído usando o Portal do Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Como excluir um banco de dados SQL?
Para excluir um banco de dados SQL, clique em **Excluir** na folha **Banco de Dados SQL**. 

![Configurações do Banco de Dados SQL](./media/sql-database-manage-portal/sql-database-delete.png)

## <a name="additional-resources"></a>Recursos adicionais
* [Banco de Dados SQL](sql-database-technical-overview.md)
* [Monitorar e gerenciar um pool elástico com o portal do Azure](sql-database-elastic-pool-manage-portal.md)


