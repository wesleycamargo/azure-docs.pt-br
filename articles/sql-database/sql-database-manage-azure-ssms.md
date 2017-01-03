---
title: Gerenciar um Banco de Dados SQL com o SSMS | Microsoft Docs
description: Aprenda a usar o SQL Server Management Studio para gerenciar bancos de dados e servidores de banco de dados SQL.
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: d9ff74a49742fa77f5989b8b05e0567e3ca81dc5
ms.openlocfilehash: 89cb8827745b31b3a77b64d5cafd586957d60d30


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Você pode usar o SSMS (SQL Server Management Studio) para administrar servidores e bancos de dados do Banco de Dados SQL do Azure. Este tópico o orienta em tarefas comuns com o SSMS. Você já deve ter um servidor e um banco de dados criados no Banco de Dados SQL do Azure antes de começar. Consulte [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md) e [Conectar e consultar usando o SSMS](sql-database-connect-query-ssms.md) para obter mais informações.

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar um firewall baseado em servidor, exibir propriedades do servidor, conectar-se usando o SQL Server Management Studio, consultar o banco de dados mestre, criar um banco de dados de exemplo e um banco de dados em branco, consultar propriedades de banco de dados, conectar-se usando o SQL Server Management Studio e consultar o banco de dados de exemplo, confira [Tutorial de Introdução](sql-database-get-started.md).
>

É recomendável que você use a versão mais recente do SSMS sempre que trabalhar com o Banco de Dados SQL do Azure. 

> [!IMPORTANT]
> Sempre use a versão mais recente do SSMS porque ele é aprimorado continuamente para trabalhar com as atualizações mais recentes do Azure e do Banco de Dados SQL. Para obter a versão mais recente, consulte [Baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Criar e gerenciar Banco de Dados SQL do Azure
Enquanto estiver conectado ao banco de dados **mestre** , você pode criar bancos de dados no servidor e modificar ou soltar bancos de dados existentes. As etapas a seguir descrevem como realizar várias tarefas de gerenciamento de banco de dados comuns por meio do Management Studio. Para executar essas tarefas, verifique se você está conectado ao banco de dados **mestre** com o logon de nível de servidor principal que criou quando configurou o servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta de bancos de dados, expanda a pasta **Banco de dados do sistema**, clique com botão direito do mouse em **mestre** e clique em **Nova Consulta**.

* Use a instrução **CREATE DATABASE** para criar um banco de dados. Para saber mais, confira [CREATE DATABASE (Banco de dados SQL)](https://msdn.microsoft.com/library/dn268335.aspx). A instrução a seguir cria um banco de dados chamado **myTestDB** e especifica que se trata de um banco de dados da Standard S0 Edition com um tamanho padrão máximo de 250 GB.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Clique em **Execute** para executar a consulta.

* Use a instrução **ALTER DATABASE** para modificar um banco de dados existente, por exemplo, se você quiser alterar o nome e a edição do banco de dados. Para obter mais informações, consulte [ALTER DATABASE (Banco de Dados SQL)](https://msdn.microsoft.com/library/ms174269.aspx). A instrução a seguir modifica o banco de dados criado na etapa anterior para alterar a edição para Standard S1.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* Use a instrução **DROP DATABASE** para excluir um banco de dados existente. Para saber mais, confira [DROP DATABASE (Banco de dados SQL)](https://msdn.microsoft.com/library/ms178613.aspx). A instrução a seguir exclui o banco de dados **myTestDB** , mas não o remova agora, pois você o utilizará para criar logons na próxima etapa.
  
      DROP DATABASE myTestBase;
* O banco de dados mestre tem o modo de exibição **sys.databases** , que você pode usar para exibir detalhes sobre todos os bancos de dados. Para exibir todos os bancos de dados existentes, execute a seguinte instrução:
  
      SELECT * FROM sys.databases;
* No banco de dados SQL, a instrução **USE** não é suportada para alternar entre bancos de dados. Em vez disso, você precisa estabelecer uma conexão diretamente com o banco de dados de destino.

> [!NOTE]
> Muitas das instruções Transact-SQL que cria ou modifica um banco de dados devem ser executadas em seu próprio lote e não podem ser agrupadas com outras instruções Transact-SQL. Para obter mais informações, consulte as informações específicas de instrução.
> 
> 

## <a name="create-and-manage-logins"></a>Criar e gerenciar logons
O banco de dados **mestre** contém os logons e quais logons têm permissão para criar bancos de dados ou outros logons. Gerencie logons conectando-se ao banco de dados **mestre** com o logon de nível de servidor principal que você criou quando configurou o servidor. Você pode usar as instruções **CREATE LOGIN**, **ALTER LOGIN** ou **DROP LOGIN** para executar consultas no banco de dados mestre que gerenciam logons por todo o servidor. Para obter mais informações, consulte [gerenciamento de bancos de dados e logons no banco de dados SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx) 

* Use a instrução **CREATE LOGIN** para criar um logon no nível de servidor. Para obter mais informações, consulte [CREATE LOGIN (Banco de Dados SQL)](https://msdn.microsoft.com/library/ms189751.aspx). A instrução a seguir cria um logon chamado **login1**. Substitua **password1** pela senha de sua escolha.
  
      CREATE LOGIN login1 WITH password='password1';
* Use a instrução **CREATE USER** para conceder permissões em nível de banco de dados. Todos os logons devem ser criados no banco de dados **mestre** . Para que um logon se conecte a um banco de dados diferente, você deve conceder a esse logon permissões de nível de banco de dados usando a instrução **CREATE USER** nesse banco de dados. Para obter mais informações, consulte [CREATE USER (Banco de Dados SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 
* Para dar permissões de login1 para um banco de dados chamado **myTestDB**, execute as seguintes etapas:
  
  1. Para atualizar o Pesquisador de Objetos e exibir o banco de dados **myTestDB** que você acabou de criar, clique com o botão direito do mouse no nome do servidor no Pesquisador de Objetos e clique em **Atualizar**.  
     
     Se você fechou a conexão, você pode se reconectar, selecionando **conectar Object Explorer** no menu arquivo.
  2. Clique com o botão direito do mouse em banco de dados **myTestDB** e selecione **Nova Consulta**.
  3. Execute a seguinte instrução no banco de dados myTestDB para criar um usuário de banco de dados chamado **login1User** que corresponde ao logon em nível de servidor **login1**.
     
         CREATE USER login1User FROM LOGIN login1;
* Use o procedimento **sp\_addrolemember** armazenado para conceder à conta do usuário o nível apropriado de permissões no banco de dados. Para saber mais, consulte [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). A declaração a seguir dá ao **login1User** permissões de somente leitura ao banco de dados adicionando **login1User** à função **db\_datareader**.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Use a instrução **ALTER LOGIN** para modificar um logon existente, por exemplo, se você desejar alterar a senha para o logon. Para saber mais, consulte [ALTER LOGIN (banco de dados SQL)](https://msdn.microsoft.com/library/ms189828.aspx). A instrução **ALTER LOGIN** deve ser executada no banco de dados **mestre**. Alterne para a janela de consulta que está conectada ao banco de dados. A instrução a seguir modifica o logon **login1** para redefinir a senha. Substitua **newPassword** pela senha de sua escolha, e **oldPassword** pela senha atual de logon.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* Use a instrução **DROP LOGIN** para excluir um logon existente. Excluir um logon no nível do servidor também exclui quaisquer contas de usuário do banco de dados associado. Para saber mais, confira [DROP DATABASE (Banco de dados SQL)](https://msdn.microsoft.com/library/ms178613.aspx). A instrução **DROP LOGIN** deve ser executada no banco de dados **mestre**. A instrução exclui o logon **login1** .
  
      DROP LOGIN login1;
* O banco de dados mestre tem a visualização de **sys.sql\_logins** que você pode usar para exibir logons. Para exibir todos os logons existentes, execute a seguinte instrução:
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Monitorar o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico
O Banco de dados SQL oferece suporte a vários modos de exibição de gerenciamento dinâmico que você pode usar para monitorar um banco de dados individual. A seguir estão alguns exemplos do tipo de dados de monitor que podem ser recuperados por meio desses modos de exibição. Para obter detalhes completos e mais exemplos de uso, confira [Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* Consultar uma exibição de gerenciamento dinâmico requer permissões **VIEW DATABASE STATE** . Para conceder a permissão **VIEW DATABASE STATE** a um usuário de banco de dados específico, conecte-se ao banco de dados e execute a seguinte instrução no banco de dados:
  
      GRANT VIEW DATABASE STATE TO login1User;
* Calcular banco de dados usando a exibição **sys.dm\_db\_partition\_stats**. A exibição **sys.dm\_db\_partition\_stats** retorna informações da página e sobre a contagem de linhas para cada partição do banco de dados, que você pode usar para calcular o tamanho do banco de dados. A consulta a seguir retorna o tamanho do seu banco de dados em megabytes:
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* Use as exibições **sys.dm\_exec\_connections** e **sys.dm\_exec\_sessions** para recuperar informações sobre as conexões do usuário atual e tarefas internas associadas ao banco de dados. A consulta a seguir retorna informações sobre a conexão atual:
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* Use a exibição **sys.dm\_exec\_query\_stats** para recuperar estatísticas de desempenho agregado para planos de consulta em cache. A consulta a seguir retorna informações sobre as consultas de cinco principais classificados por tempo médio de CPU.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;




<!--HONumber=Dec16_HO3-->


