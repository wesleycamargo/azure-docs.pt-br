<properties
    pageTitle="Visão geral da consulta de banco de dados elástico do Banco de Dados SQL do Azure"
    description="Visão geral do recurso de consulta elástica"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/09/2015"
    ms.author="sidneyh" />

# Visão geral da consulta de Banco de Dados Elástico do Banco de Dados SQL do Azure (visualização)

O **recurso de consulta de Banco de dados Elástico**, em visualização, permite que você execute uma consulta Transact-SQL que abrange vários bancos de dados no Banco de Dados SQL do Azure. Ele permite conectar ferramentas da Microsoft e de terceiros (Excel, PowerBI, Tableau, etc.) às camadas de dados com vários bancos de dados, especialmente quando esses bancos de dados compartilham um esquema comum (também conhecido como particionamento ou fragmentação horizontal). Usando esse recurso, você pode escalar horizontalmente para camadas de dados grandes no Banco de Dados SQL e visualizar os resultados em relatórios do BI (business intelligence).

Para começar a criar um aplicativo de consulta de banco de dados elástico, consulte [Introdução à consulta de Banco de Dados Elástico](sql-database-elastic-query-getting-started.md).

## Cenários de consulta de banco de dados elástico

O objetivo da consulta de Banco de Dados Elástico é facilitar cenários de relatórios em que vários bancos de dados acrescentam linhas em um único resultado geral. A consulta ou pode ser criada pelo usuário ou aplicativo diretamente ou indiretamente por meio de ferramentas que conectadas ao banco de dados de consulta. Isso é especialmente útil ao criar relatórios usando as ferramentas de integração de BI ou dados comerciais, ou então qualquer software que não pode ser alterado. Com uma consulta de banco de dados elástico, você pode consultar facilmente vários bancos de dados usando a experiência familiar de conectividade do SQL Server em ferramentas como o Excel, PowerBI, Tableau ou Cognos.

Uma consulta de banco de dados elástico também facilita o acesso a um conjunto inteiro de bancos de dados por meio de consultas emitidas pelo SQL Server Management Studio ou Visual Studio e facilita a consulta de bancos de dados do Entity Framework ou outros ambientes de ORM. A Figura 1 mostra um cenário em que um aplicativo de nuvem existente (que usa a biblioteca de ferramentas de Banco de Dados Elástico) se baseia em uma camada de dados dimensionável e uma consulta de banco de dados elástico é usada para os relatórios de bancos de dados.

**Figura 1**

![Consulta de banco de dados elástico usada na camada de dados em escala][1]

A camada de dados é escalada horizontalmente em vários bancos de dados usando um esquema comum. Essa abordagem também é conhecido como particionamento ou fragmentação horizontal. O particionamento pode ser executado e gerenciado usando (1) a [biblioteca de cliente do banco de dados elástico](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) ou (2) usando um modelo específico de aplicativo para distribuição de dados entre vários bancos de dados. Com essa topologia, os relatórios geralmente abrangem vários bancos de dados. Com uma consulta de banco de dados elástico, você pode conectar a um Banco de Dados SQL individual e resultados de consulta de bancos de dados remotos são exibidos como se fossem gerados por meio de um banco de dados virtual individual.

> [AZURE.NOTE]A consulta de banco de dados elástica funciona melhor em cenários de relatórios ocasionais em que a maior parte do processamento pode ser executada na camada de dados. Para cenários de cargas de trabalho pesadas de relatórios ou data warehouse com consultas mais complexas, considere também usar[Data Warehouse SQL do Azure](http://azure.microsoft.com/services/sql-data-warehouse/).


## Topologia de consulta de Banco de Dados Elástico

Usar uma consulta de banco de dados elástico para executar tarefas de relatórios em uma camada de dados particionados horizontalmente requer um mapa de fragmentos de escala elástica para representar os bancos de dados da camada de dados. Normalmente, um mapa de fragmento único é usado neste cenário e um banco de dados dedicado com recursos de consulta de banco de dados elástico serve como ponto de entrada para consultas de relatórios. Apenas este banco de dados dedicado precisa ser configurado com objetos de consulta de banco de dados elástico, conforme descrito abaixo. A Figura 2 ilustra essa topologia e sua configuração com a consulta de banco de dados elástico e o mapa de fragmentos.

> [AZURE.NOTE]A consulta de banco de dados elástico dedicado deve ser um banco de dados do Banco de Dados SQL v12, inicialmente com suporte apenas na camada Premium. Não há nenhuma restrição sobre os próprios fragmentos.

**Figura 2**

![Usar consultas de Banco de Dados elástico para geração de relatórios em camadas fragmentadas][2]

Um **shardlet** são todos os dados associados a um único valor de uma chave de fragmentação em um fragmento. Um **chave de fragmentação** é um valor de coluna que determina como os dados são distribuídos nos fragmentos. Por exemplo, dados distribuídos por região podem ter IDs de região como chave de fragmentação. Para obter mais detalhes, consulte o [Glossário de escala elástica](sql-database-elastic-scale-glossary.md).


Ao longo do tempo, topologias adicionais serão compatíveis com o recurso de consulta de banco de dados elástico. Este artigo será atualizado para refletir os novos recursos assim que forem disponibilizados.

## Habilitar consultas elásticas configurando um mapa do fragmentos

Criar uma solução de consulta de banco de dados elástico requer o [**mapa de fragmentos**](sql-database-elastic-scale-shard-map-management.md) das ferramentas de Banco de Dados Elástico para representar os bancos de dados remotos em uma consulta de banco de dados elástico. Se já estiver usando a biblioteca de cliente do Banco de Dados Elástico, você poderá usar o mapa de fragmentos existente. Caso contrário, você precisará criar um mapa de fragmentos usando ferramentas de Banco de Dados Elástico.

O exemplo de código C# mostra como criar um mapa de fragmentos com um único banco de dados remoto adicionado como um fragmento.

    ShardMapManagerFactory.CreateSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerCreateMode.ReplaceExisting, RetryBehavior.DefaultRetryBehavior);
    smm = ShardMapManagerFactory.GetSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerLoadPolicy.Lazy,
      RetryBehavior.DefaultRetryBehavior);
    map = smm.CreateRangeShardMap<int>("yourshardmapname");
    shard = map.CreateShard(new ShardLocation("yoursqldbserver", "yoursqldbdatabasename"));

Para obter mais detalhes sobre mapas de fragmentos, consulte [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).

Para usar o recurso de consulta de Banco de Dados Elástico, primeiro crie o mapa de fragmentos e registre seus bancos de dados remotos como fragmentos. Essa é uma operação única. Você só precisa alterar seu mapa de fragmentos quando adicionar ou remover bancos de dados remotos, que são operações incrementais em um mapa de fragmentos existente.


## Criar objetos de banco de dados de consulta de banco de dados elástico

Para descrever as tabelas remotas que podem ser acessadas de um ponto de extremidade de consulta de banco de dados elástico, apresentamos a capacidade de definir tabelas externas que serão exibidas no aplicativo e em ferramentas de terceiros como se fossem tabelas locais. Consultas podem ser enviadas em relação a esses objetos de banco de dados implicitamente por meio de ferramentas ou explicitamente no SQL Server Management Studio, Visual Studio Data Tools ou de um aplicativo. A consulta de banco de dados elástico é executada como qualquer outra instrução do Transact-SQL, com a diferença notável de que essa consulta distribuirá o processamento entre os potencialmente muitos bancos de dados remotos subjacentes os objetos externos.

O recurso de consulta de banco de dados elástico depende dessas quatro instruções DDL. Normalmente, essas instruções DDL são usadas de uma vez ou raramente ao alterar o esquema de seu aplicativo.

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
*    [CRIAR CREDENCIAL](https://msdn.microsoft.com/library/ms189522.aspx)
*    [CRIAR/REMOVER FONTE DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
*    [CRIAR/REMOVER TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)

### Chave mestra do escopo do banco de dados e credenciais

Uma credencial representa a ID de usuário e senha que a consulta de banco de dados elástico usará para se conectar ao seu mapa de fragmentos de escala elástica e seus bancos de dados remotos no Banco de Dados SQL do Azure. Você pode criar a chave mestra necessária e uma credencial usando a seguinte sintaxe:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';  
    CREATE CREDENTIAL <credential_name> ON DATABASE
    WITH IDENTITY = '<shard_map_username>',
    SECRET = '<shard_map_password>'
     [;]
Verifique se o & lt; nome_de_usuário_do_mapa_de_fragmentos > não tem nenhum sufixo "@servername".

Você pode usar a sintaxe a seguir para remover a chave mestra e as credenciais:

    DROP CREDENTIAL <credential_name> ON DATABASE;
    DROP MASTER KEY;  

### Fontes de dados externas

Como próxima etapa, você precisa registrar o mapa de fragmentos como uma fonte de dados externa. A sintaxe para criar e remover fontes de dados externas é definida da seguinte maneira:

      CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
                     (TYPE = SHARD_MAP_MANAGER,
                     LOCATION = '<fully_qualified_server_name>',
                     DATABASE_NAME = '<shardmap_database_name>',
                     CREDENTIAL = <credential_name>,
                     SHARD_MAP_NAME = '<shardmapname>'
    ) [;]

Você pode usar a instrução a seguir para remover uma fonte de dados externa:

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

O usuário deve ter a permissão para ALTERAR QUALQUER FONTE DE DADOS EXTERNA. Essa permissão está incluída na permissão ALTERAR BANCO DE DADOS.

**Exemplo**

O exemplo a seguir ilustra o uso da instrução CRIAR para fontes de dados externas.

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
    );

Você pode recuperar a lista de fontes de dados externas atual na exibição do catálogo a seguir:

    select * from sys.external_data_sources;

Observe que as mesmas credenciais são usadas para ler o mapa de fragmentos e acessar os dados nos bancos de dados remotos durante o processamento da consulta.


### Tabelas externas

Com a consulta de Banco de Dados Elástico, estendemos a sintaxe de tabela externa existente para se referir a tabelas que são particionadas em (vários) bancos de dados remotos no Banco de Dados SQL do Azure. Usando o conceito de fonte de dados externa mostrado acima, a sintaxe para criar e remover tabelas externas é definida da seguinte maneira:

    CREATE EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    )[;]

    <sharded_external_table_options> ::=
          DATA_SOURCE = <External_Data_Source>,
          DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED | ROUND_ROBIN

A política de fragmentação controla se uma tabela é tratada como uma tabela fragmentada ou como uma tabela replicada. Com uma tabela fragmentada, os dados de diferentes fragmentos não se sobrepõem. Tabelas replicadas, por sua vez, têm os mesmos dados em cada fragmento. O processador de consulta utiliza essas informações para proporcionar um processamento de consulta correto e mais eficiente. A distribuição round robin indica que foi usado um método específico de aplicativo para distribuir os dados da tabela.

    DROP EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name[;]

As permissões para **CRIAR/REMOVER TABELA EXTERNA**: ALTERAR QUALQUER FONTE DE DADOS EXTERNA são necessárias também para consultar a fonte de dados subjacente.

**Exemplo**: o exemplo a seguir ilustra como criar uma tabela externa:

    CREATE EXTERNAL TABLE [dbo].[order_line](
        [ol_o_id] [int] NOT NULL,
        [ol_d_id] [tinyint] NOT NULL,
        [ol_w_id] [int] NOT NULL,
        [ol_number] [tinyint] NOT NULL,
        [ol_i_id] [int] NOT NULL,
        [ol_delivery_d] [datetime] NOT NULL,
        [ol_amount] [smallmoney] NOT NULL,
        [ol_supply_w_id] [int] NOT NULL,
        [ol_quantity] [smallint] NOT NULL,
        [ol_dist_info] [char](24) NOT NULL
    )
    WITH
    (
        DATA_SOURCE = MyExtSrc,
        DISTRIBUTION=SHARDED(ol_w_id)
    );

O exemplo a seguir mostra como recuperar a lista de tabelas externas do banco de dados atual:

    select * from sys.external_tables;


## Relatórios e consultas

### Consultas
Depois de definir a fonte de dados externa e suas tabelas externas, você pode usar cadeias de conexão do Banco de Dados SQL familiares para se conectar ao banco de dados que tem o recurso de consulta de Banco de Dados Elástico habilitado. Agora você poderá executar consultas somente leitura completas nas suas tabelas externas, com certas limitações explicadas na [seção limitações](#preview-limitations) abaixo.

**Exemplo**: a consulta a seguir executa uma junção de três vias entre depósitos, pedidos e linhas da pedido e usa várias agregações e um filtro seletivo. Supondo que os warehouses, pedidos e linhas de pedido sejam particionadas pela coluna de ID do warehouse, uma consulta de banco de dados elástico pode colocar as junções nos bancos de dados remotos e expandir o processamento da parte cara da consulta.

    select
        w_id as warehouse,
        o_c_id as customer,
        count(*) as cnt_orderline,
        max(ol_quantity) as max_quantity,
        avg(ol_amount) as avg_amount,
        min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id

### Procedimento armazenado SP_ EXECUTE_FANOUT

SP_EXECUTE_FANOUT é um procedimento armazenado que fornece acesso aos bancos de dados representados por um mapa de fragmentos. O procedimento armazenado usa os seguintes parâmetros:

-    **Nome do servidor** (nvarchar): o nome totalmente qualificado do servidor lógico que hospeda o mapa de fragmentos.
-    **Nome de banco de dados do mapa de fragmentos** (nvarchar): o nome do banco de dados do mapa de fragmentos.
-    **Nome de usuário** (nvarchar): o nome de usuário para fazer logon em bancos de dados remotos e no banco de dados do mapa de fragmentos.
-    **Senha** (nvarchar): senha do usuário.
-    **Nome de mapa de fragmentos** (nvarchar): o nome do mapa de fragmentos a ser usado para a consulta.
-    **Consulta**: a consulta a ser executada em cada fragmento.

Ela usa as informações do mapa de fragmentos fornecidas nos parâmetros de invocação para executar a instrução em todos os fragmentos registrados com o mapa de fragmentos. Todos os resultados são mesclados usando a semântica de UNION ALL semelhante às consultas de vários fragmentos. O resultado também inclui a coluna adicional 'virtual' com o nome do banco de dados remoto.

Observe que as mesmas credenciais são usadas para conectar ao banco de dados do mapa de fragmentos e aos fragmentos.

**Exemplo**:

    sp_execute_fanout 'myserver.database.windows.net', N'ShardMapDb', N'myuser', N'MyPwd', N'ShardMap', N'select count(w_id) as foo from warehouse'

## Conectividade de ferramentas
Você pode usar cadeias de conexão do Banco de Dados SQL familiares para seu banco de dados de consulta de Banco de Dados Elástico para conectar suas ferramentas de integração de dados e BI. Certifique-se de que o SQL Server tem suporte como uma fonte de dados para a ferramenta. Em seguida, use objetos externos no banco de dados da consulta de Banco de Dados Elástico, da mesma maneira que com qualquer outro Banco de Dados SQL Server que se conectaria à ferramenta.

## Práticas recomendadas
*    Certifique-se de que o banco de dados do gerenciador de mapa de fragmentos e os bancos de dados definidos no mapa de fragmentos concedem acesso ao Microsoft Azure nas suas regras de firewall. Isso é necessário para que o banco de dados de consulta de Banco de Dados Elástico possa conectar-se a eles. Para obter mais informações, consulte [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).
*    Uma consulta de banco de dados elástico não valida nem impõe a distribuição de dados definida pela tabela externa. Se a distribuição de dados real for diferente da distribuição especificada na definição de tabela, as consultas podem gerar resultados inesperados.
*    Uma consulta de banco de dados elástico funciona melhor para consultas em que a maior parte da computação pode ser realizada nos fragmentos. Normalmente o melhor desempenho de consulta é obtido com predicados de filtro seletivo que podem ser avaliados nos fragmentos ou junções sobre as chaves de particionamento que podem ser executadas de forma alinhada por partição em todos os fragmentos. Outros padrões de consulta podem precisar carregar grandes quantidades de dados dos fragmentos para o nó principal, podendo acarretar em baixo desempenho.

## Custo

A consulta de Banco de Dados Elástico inclui o custo dos bancos de dados do Banco de Dados SQL do Azure. Observe que as topologias têm suporte em bancos de dados remotos em um data center diferente que o ponto de extremidade de consulta de banco de dados elástico, mas a saída de dados de bancos de dados remotos é cobrada a taxas normais de saída do Azure.

## Limitações de visualização

Há alguns pontos que devem ser considerados na visualização:

*    O recurso de consulta de Banco de Dados Elástico ficará inicialmente disponível apenas na camada de desempenho Premium do Banco de Dados SQL v12, embora os bancos de dados remotos acessados por uma consulta de banco de dados elástico possa ser de qualquer camada.
* Tabelas externas referenciadas por sua fonte de dados externa somente dão suporte a operações de leitura dos bancos de dados remotos. Você pode, no entanto, apontar a funcionalidade completa do Transact-SQL no banco de dados de consulta elástico do banco de dados onde reside a própria definição da tabela externa. Isso pode ser útil, por exemplo, para manter os resultados temporários usando SELECT column_list INTO local_table ou definir os procedimentos armazenados na consulta de banco de dados elástico que se referem a tabelas externas.
*    Parâmetros em consultas não podem ser enviados para bancos de dados remotos no momento. Consultas parametrizadas deverão reunir todos os dados no nó principal, podendo sofram com baixo desempenho, dependendo do tamanho dos dados. Uma solução temporária é evitar parâmetros em suas consultas ou usar a opção RECOMPILE para que os parâmetros sejam automaticamente substituídos por seus valores atuais.
* Estatísticas no nível de coluna sobre tabelas externas não tem suporte no momento.
* A consulta de Banco de Dados Elástico atualmente não executa a eliminação de fragmento em casos quando predicados da chave de fragmentação permitiriam excluir com segurança determinados bancos de dados remotos de processamento. Como resultado, consultas sempre afetam todos os bancos de dados remotos representados por fontes de dados externas da consulta.
* Todas as consultas que envolvem junções entre tabelas em bancos de dados diferentes podem trazer grandes números de linhas para o banco de dados de consulta de banco de dados elástico para processamento, resultando no custo de desempenho. É melhor desenvolver consultas que podem ser processadas localmente em cada banco de dados remoto ou usar cláusulas WHERE para restringir as linhas envolvidas de cada banco de dados antes de executar a associação.
*    A sintaxe usada para definição de metadados da consulta de banco de dados elástico será alterada durante a visualização.
*    A funcionalidade de script Transact-SQL no SSMS ou SSDT atualmente não funciona com objetos de consulta de banco de dados elástico.

## Comentários
Envie para nós seus comentários sobre sua experiência com Disqus ou Stackoverflow. Estamos interessados em todos os tipos de comentários sobre o serviço (defeitos, pontos em aberto, lacunas do recurso).

## Próximas etapas
Para começar a explorar a consulta de Banco de Dados Elástico, siga nosso tutorial passo a passo para ver um exemplo completo em funcional em execução em minutos: [Introdução à consulta de Banco de Dados Elástico](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png

<!--anchors-->

<!---HONumber=July15_HO4-->