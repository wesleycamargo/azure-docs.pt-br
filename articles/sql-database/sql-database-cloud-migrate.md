---
title: "Migração do banco de dados do SQL Server para o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre a migração de banco de dados do SQL Server local para o Banco de Dados SQL do Azure na nuvem. Use ferramentas de migração do banco de dados para testar a compatibilidade antes da migração do banco de dados."
keywords: "migração de banco de dados, migração de banco de dados do sql server, ferramentas de migração de banco de dados, migrar banco de dados, migrar banco de dados sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 61fb027dfdd5830d87fe4fcfff57f685db71475e


---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migração de banco de dados do SQL Server para o Banco de Dados SQL na nuvem
Neste artigo, você aprenderá a migrar um banco de dados local do SQL Server 2005 ou posterior para o Banco de Dados SQL do Azure. Nesse processo de migração de banco de dados, você migra o esquema e os dados do banco de dados SQL Server em seu ambiente atual para o Banco de Dados SQL. Para ter êxito, o banco de dados existente deve passar pela primeira vez um teste de compatibilidade. Com o Banco de Dados SQL V12, estamos nos aproximando da [paridade de recursos](sql-database-features.md), em vez do problema relacionado a operações no servidor e entre bancos de dados. Os bancos de dados e os aplicativos que dependem de [funções com suporte parcial ou sem suporte](sql-database-transact-sql-information.md) precisarão de alguma reengenharia que corrija essas incompatibilidades para que o banco de dados SQL Server possa ser migrado.

Para migrar, os itens a seguir são as etapas pra você utilizar:

* **Teste de compatibilidade**: valide a compatibilidade do banco de dados com o Banco de Dados SQL. 
* **Corrigir problemas de compatibilidade, se houver**: se a validação falhar, você deverá corrigir os erros de validação.  
* **Executar a migração** : assim que o banco de dados for compatível, você poderá usar um ou vários métodos para executar a migração. 

O SQL Server fornece vários métodos para realizar cada uma dessas tarefas. Este artigo fornece uma visão geral dos métodos disponíveis para cada tarefa. O diagrama a seguir ilustra as etapas e os métodos.

  ![Diagrama de migração do VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> Para migrar um banco de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para o Banco de Dados SQL do Azure, confira [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/).
> 
> 

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Ferramentas de migração de banco de dados testam a compatibilidade do banco de dados do SQL Server com o Banco de Dados SQL
Para testar os problemas de compatibilidade do Banco de Dados SQL antes de iniciar o processo de migração de banco de dados, use um dos seguintes métodos:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* ["SSDT" (SQL Server Data Tools para Visual Studio)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): o SSDT usa as regras de compatibilidade mais recentes para detectar as incompatibilidades do Banco de Dados SQL V12. Se forem detectadas incompatibilidades, você poderá corrigir os problemas detectados diretamente nessa ferramenta. Esse método é o recomendado para testar e corrigir problemas de compatibilidade do Banco de Dados SQL V12. 
* [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage é um utilitário de linha de comando que testa problemas de compatibilidade e gera um relatório contendo problemas de compatibilidade detectados. Ao usar essa ferramenta, utilize a versão mais recente para ter as regras de compatibilidade mais recentes. Se forem detectados erros, você deverá usar outra ferramenta para corrigir os problemas de compatibilidade detectados – o SSDT é recomendado.  
* [O assistente Exportar Aplicativo da Camada de Dados no SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): esse assistente detecta e relata erros na tela. Se nenhum erro for detectado, você poderá continuar e concluir a migração para o Banco de Dados SQL. Se forem detectados erros, você deverá usar outra ferramenta para corrigir os problemas de compatibilidade detectados – o SSDT é recomendado.
* ["SAMW" (SQL Azure Migration Wizard)](sql-database-cloud-migrate-fix-compatibility-issues.md): o SAMW é uma ferramenta codeplex que usa as regras de compatibilidade do Banco de Dados SQL V11 do Azure para detectar as incompatibilidades do Banco de Dados SQL V12 do Azure. Se incompatibilidades forem detectadas, alguns problemas poderão ser corrigidos diretamente nessa ferramenta. Essa ferramenta pode localizar incompatibilidades que não precisam ser corrigidas. Ela foi a primeira ferramenta de assistência de migração do Banco de Dados SQL disponível e tem suporte ativo da comunidade do SQL Server. Além disso, essa ferramenta pode concluir a migração nela mesma. 

## <a name="fix-database-migration-compatibility-issues"></a>Corrigir problemas de compatibilidade de migração do banco de dados
Se forem detectados problemas de compatibilidade, você deve corrigi-los antes de continuar com a migração do banco de dados do SQL Server. Há uma grande variedade de problemas de compatibilidade que você pode encontrar, dependendo da versão do SQL Server no banco de dados de origem e da complexidade do banco de dados que você está migrando. Versões anteriores do SQL Server têm mais problemas de compatibilidade. Use os recursos a seguir, além de uma pesquisa direcionada na Internet, usando o mecanismo de pesquisa de sua preferência:

* [Recursos de banco de dados do SQL Server sem suporte no Banco de Dados SQL do Azure](sql-database-transact-sql-information.md)
* [Discontinued Database Engine Functionality in SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Discontinued Database Engine Functionality in SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Discontinued Database Engine Functionality in SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Discontinued Database Engine Functionality in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Discontinued Database Engine Functionality in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Além de pesquisar na Internet e usar esses recursos, use os [fóruns da comunidade do SQL Server no MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou o [StackOverflow](http://stackoverflow.com/).

Use uma das ferramentas de migração de banco de dados a seguir para corrigir os problemas detectados:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* Use [SQL Server Data Tools para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): para usar o SSDT, importe o esquema de banco de dados para o SQL Server Data Tools para Visual Studio "SSDT" e crie o projeto para uma implantação do Banco de Dados SQL V12. Então, corrija todos os problemas de compatibilidade detectados no SSDT. Ao concluir, sincronize as alterações de volta para o banco de dados de origem ou uma cópia do banco de dados de origem. O SSDT é o método atualmente recomendado para testar e corrigir problemas de compatibilidade do Banco de Dados SQL V12. Siga o link para [ver detalhadamente como usar o SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
* Use o ["SSMS" (SQL Server Management Studio)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): para usar o SSMS, execute os comandos do Transact-SQL para corrigir os erros detectados usando outra ferramenta. Esse método é basicamente para que os usuários avançados modifiquem o esquema do banco de dados diretamente no banco de dados de origem. 
* Use o [SQL Azure Migration Wizard ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): para usar o SAMW, você deve gerar um script Transact-SQL do banco de dados de origem. O assistente transforma o script, sempre que possível, para tornar o esquema compatível com o banco de dados SQL V12. Ao concluir, o SAMW pode se conectar ao Banco de Dados SQL V12 para executar o script. Essa ferramenta também analisa os arquivos de rastreamento para determinar problemas de compatibilidade. O script pode ser gerado apenas com esquemas ou pode incluir dados no formato BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrar um banco de dados SQL Server compatível para o Banco de Dados SQL
Para migrar um banco de dados do SQL Server compatível, a Microsoft fornece vários métodos de migração para vários cenários. O método escolhido depende da sua tolerância para tempo de inatividade, do tamanho e da complexidade do seu banco de dados SQL Server, bem como da conectividade com a nuvem do Microsoft Azure.  

> [!div class="op_single_selector"]
> * [Assistente de Migração do SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exportar para um arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importar de um arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Replicação Transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Para escolher o método de migração, a primeira pergunta a ser feita é se você pode arcar com a retirada do banco de dados da produção durante a migração. Migrar um banco de dados enquanto transações ativas estão ocorrendo pode resultar em inconsistências e na possível corrupção do banco de dados. Há vários métodos para fechar um banco de dados para novas sessões, desde desabilitar a conectividade do cliente até criar um [instantâneo do banco de dados](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar com tempo de inatividade mínimo, use a [replicação de transação do SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) se seu banco de dados atender aos requisitos de replicação transacional. Se for possível suportar algum tempo de inatividade ou se você estiver executando um teste de migração de um banco de dados de produção para migração posterior, leve em consideração um dos três métodos a seguir:

* [Assistente de Migração do SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): no caso de bancos de dados pequenos a médios, a migração de um banco de dados SQL Server 2005 ou posterior compatível é tão simples quanto executar o [Assistente para Implantar Banco de Dados no Banco de Dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) no SQL Server Management Studio.
* [Exportar para arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e [Importar do arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): se você tiver desafios de conectividade (nenhuma conectividade, largura de banda baixa ou problemas de tempo limite) e bancos de dados médios a grandes, use um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Com esse método, você exporta o esquema do SQL Server e os dados para um arquivo BACPAC. Então, importe o arquivo BACPAC no Banco de Dados SQL usando o Assistente para Exportar Aplicativo da Camada de Dados no SQL Server Management Studio ou o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .
* Use o BACPAC e o BCP em conjunto: use um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e o [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para bancos de dados muito maiores, a fim de alcançar maior paralelização para aumento de desempenho, ainda que com maior complexidade. Com esse método, migre o esquema e os dados separadamente.
  
  * [Exportar o esquema somente para um arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
  * [Importar o esquema somente do arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) no Banco de Dados SQL.
  * Use o [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extrair os dados em arquivos simples e [carregar paralelamente](https://technet.microsoft.com/library/dd425070.aspx) esses arquivos no Banco de Dados SQL do Azure.
    
     ![Migração do banco de dados do SQL Server: migrar o banco de dados SQL para a nuvem.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Próximas etapas
* [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais
* [Recurso do Banco de Dados SQL](sql-database-features.md)
  [Funções do Transact-SQL sem suporte ou com suporte parcial](sql-database-transact-sql-information.md)
* [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


