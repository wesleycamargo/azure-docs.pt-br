---
title: "Migrar para o Banco de Dados SQL usando a replicação transacional | Microsoft Docs"
description: "Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, replicação transacional"
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Migrar o banco de dados do SQL Server para o Banco de Dados SQL do Azure usando a replicação transacional
> [!div class="op_single_selector"]
> * [Assistente de Migração do SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exportar para um arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importar de um arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replicação Transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Neste artigo, você aprenderá a migrar um banco de dados do SQL Server compatível para o Banco de Dados SQL do Azure com tempo de inatividade mínimo usando a replicação transacional do SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Noções básicas sobre a arquitetura de Replicação Transacional
Quando não houver a possibilidade de remover seu banco de dados do SQL Server da produção durante a migração, você poderá usar a replicação transacional do SQL Server como sua solução de migração. Para usar esta solução, você pode configurar o Banco de Dados SQL do Azure como um assinante da instância do SQL Server local que você deseja migrar. O distribuidor de replicação transacional local sincroniza os dados do banco de dados local a ser sincronizado (o editor), enquanto as novas transações continuam a ocorrer. 

Você também pode usar a replicação transacional para migrar um subconjunto de seu banco de dados local. A publicação que você replica no Banco de Dados SQL do Azure pode ser limitada a um subconjunto de tabelas no banco de dados que está sendo replicado. Para cada tabela sendo replicada, você poderá limitar os dados a um subconjunto de linhas e/ou um subconjunto de colunas.

Com a replicação transacional, todas as alterações de dados ou esquema aparecem no seu Banco de Dados SQL do Azure. Quando a sincronização for concluída e você estiver pronto para migrar, altere a cadeia de conexão de seus aplicativos para apontá-los para seu Banco de Dados SQL do Azure. Assim que a replicação transacional realizar todas as alterações restantes em seu banco de dados local e todos os seus aplicativos apontarem para o Banco de Dados do Azure, você poderá desinstalar a replicação transacional. O Banco de Dados SQL do Azure agora é o sistema de produção.

 ![Diagrama do SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="how-transactional-replication-works"></a>Como funciona a replicação transacional

A replicação transacional envolve três componentes principais. Eles são o publicador, o distribuidor e o assinante. Juntos, esses componentes executam a replicação. O distribuidor é responsável por controlar os processos que movem os dados entre servidores. Ao configurar a distribuição, o SQL criará um banco de dados de distribuição. Cada distribuidor precisa estar vinculado a um banco de dados de distribuição. O banco de dados de distribuição contém os metadados para cada publicação associada e os dados sobre o progresso de cada replicação. Para a replicação de transação, ele conterá todas as transações que precisam ser executadas no assinante.

O distribuidor é o banco de dados em que se originam todos os dados para migração. No distribuidor, pode haver várias publicações. Essas publicações contêm artigos que são mapeados para todas as tabelas e dados que precisam ser replicados. Dependendo de como você define a publicação e os artigos, é possível replicar todo ou parte do banco de dados. 

Na replicação, o assinante é o servidor que recebe todos os dados e as transações da publicação. Cada publicação pode ter várias réplicas.

## <a name="transactional-replication-requirements"></a>Requisitos de replicação transacional
[Acesse este link para obter uma lista atualizada dos requisitos.](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> Você deve usar a versão mais recente do SQL Server Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. Versões anteriores do SQL Server Management Studio não podem configurar o Banco de Dados SQL como um assinante. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migração para o Banco de Dados SQL usando o fluxo de trabalho da Replicação de Transação

1. Configurar a distribuição
   -  [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Criar a publicação
   -  [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Criar a assinatura
   -  [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Algumas dicas e diferenças da migração para o Banco de Dados SQL

1. Usar um distribuidor local 
   - Isso causará um impacto de desempenho no servidor. 
   - Se o impacto de desempenho for inaceitável, você poderá usar outro servidor, mas isso adicionará complexidade ao gerenciamento e à administração.
2. Ao selecionar uma pasta de instantâneo, verifique se a pasta selecionada é grande o suficiente para manter um BCP de cada tabela que você deseja replicar. 
3. Observe que a criação do instantâneo bloqueará as tabelas associadas até sua conclusão. Portanto, lembre-se disso ao agendar o instantâneo. 
4. Há suporte apenas para assinaturas push no Banco de Dados SQL do Azure
   - É possível adicionar somente assinantes do lado do banco de dados local.

## <a name="next-steps"></a>Próximas etapas
* [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>Recursos adicionais
* Para saber mais sobre a Replicação Transacional, consulte [Transactional Replication (Replicação Transacional)](https://msdn.microsoft.com/library/mt589530.aspx).
* Para saber mais sobre as opções e o processo geral de migração, consulte [Migração de banco de dados SQL Server para o Banco de Dados SQL na nuvem](sql-database-cloud-migrate.md).



<!--HONumber=Dec16_HO2-->


