<properties
    pageTitle="Visão geral da consulta de banco de dados elástico do Banco de Dados SQL do Azure | Microsoft Azure"
    description="Visão geral do recurso de consulta elástica"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="torsteng" />

# Visão geral da consulta de banco de dados elástico do Banco de Dados SQL do Azure (visualização)

O recurso de consulta de banco de dados elástico (em visualização) permite que você execute uma consulta Transact-SQL que abrange vários bancos de dados no SQLDB (Banco de Dados SQL do Azure). Ele permite que você realize consultas entre bancos de dados para acessar tabelas remotas e se conectar a ferramentas da Microsoft e de terceiros (Excel, Power BI, Tableau, etc.) para consultar em diferentes camadas de dados com vários bancos de dados. Usando esse recurso, você pode escalar horizontalmente para camadas de dados grandes no Banco de Dados SQL e visualizar os resultados em relatórios do BI (business intelligence).

## Documentação

* [Introdução às consultas entre bancos de dados](sql-database-elastic-query-getting-started-vertical.md)
* [Relatar entre bancos de dados na nuvem escalados horizontalmente](sql-database-elastic-query-getting-started.md)
* [Consultas do banco de dados elástico para fragmentação (particionamento horizontal)](sql-database-elastic-query-horizontal-partitioning.md)
* [Consultas de bancos de dados na nuvem com esquemas diferentes (particionamento vertical)](sql-database-elastic-query-vertical-partitioning.md)
* [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714)


## Por que usar consultas elásticas?

**Banco de Dados SQL do Azure**

Consulte em bancos de dados SQL do Azure totalmente em T-SQL. Isso permite consultas somente leitura de bancos de dados remotos. Isso fornece uma opção para que os clientes atuais do SQL Server local migrem aplicativos que usam nomes de três e quatro partes ou um servidor vinculado ao Banco de Dados SQL.

**Disponível no nível Standard** Agora há suporte para a consulta elástica no nível de desempenho Standard, além do nível de desempenho Premium. Veja a seção sobre Limitações da Preview abaixo sobre os limitações de desempenho para os níveis de desempenho inferiores.

**Enviar por push para bancos de dados remotos**

As consultas elásticas agora podem enviar por push parâmetros SQL para os bancos de dados remotos para execução.

**Execução de procedimento armazenado**

Execute chamadas remotas de procedimento armazenado ou funções remotas usando [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714).

**Flexibilidade**

Tabelas externas com elástica consulta agora podem fazer referência a tabelas remotas com um nome de tabela ou um esquema diferente.

## Cenários de consulta de banco de dados elástico

O objetivo é facilitar cenários de consultas em que vários bancos de dados acrescentam linhas em um único resultado geral. A consulta pode ser redigida pelo usuário ou pelo aplicativo diretamente, ou indiretamente por meio de ferramentas conectadas ao banco de dados. Isso é especialmente útil ao criar relatórios usando as ferramentas comerciais de BI ou de integração de dados, ou então qualquer aplicativo que não possa ser alterado. Com uma consulta elástica, é possível consultar vários bancos de dados usando a experiência familiar de conectividade do SQL Server em ferramentas como o Excel, Power BI, Tableau ou Cognos. Uma consulta elástica facilita o acesso a um conjunto inteiro de bancos de dados por meio de consultas emitidas pelo SQL Server Management Studio ou Visual Studio, além de facilitar a consulta entre bancos de dados do Entity Framework ou de outros ambientes de ORM. A Figura 1 mostra um cenário em que um aplicativo de nuvem existente (que usa a [biblioteca de clientes do banco de dados elástico](sql-database-elastic-database-client-library.md)) se baseia em uma camada de dados escalonável, e uma consulta elástica é usada para os relatórios entre bancos de dados.

**Figura 1** Consulta de banco de dados elástico usada na camada de dados escalada horizontalmente

![Consulta de banco de dados elástico usada na camada de dados em escala][1]

Os cenários do cliente para a consulta elástica são caracterizados pelas seguintes topologias:

* **Particionamento vertical – consultas entre bancos de dados** (Topologia 1): os dados são particionados verticalmente entre vários bancos de dados em uma camada de dados. Geralmente, diferentes conjuntos de tabelas residem em bancos de dados diferentes. Isso significa que o esquema é diferente em bancos de dados diferentes. Por exemplo, todas as tabelas de inventário estão em um banco de dados, enquanto todas as tabelas relacionadas à contabilidade estão em um segundo banco de dados. Casos de uso comuns com esta topologia exigem uma consulta ou compilação de relatórios entre tabelas em vários bancos de dados.
* **Particionamento horizontal – Fragmentação** (Topologia 2): os dados são particionados horizontalmente para distribuir as linhas em uma camada de dados escalada horizontalmente. Com essa abordagem, o esquema é idêntico em todos os bancos de dados participantes. Essa abordagem também é chamada de “fragmentação”. A fragmentação pode ser executada e gerenciada com (1) as bibliotecas de ferramentas do banco de dados elástico ou com (2) a autofragmentação. Uma consulta elástica é usada para consultar ou compilar relatórios em vários fragmentos.

> [AZURE.NOTE] A consulta de banco de dados elástica funciona melhor em cenários de relatórios ocasionais em que a maior parte do processamento pode ser executada na camada de dados. Para os cenários de cargas de trabalho de relatórios pesadas ou data warehouse com consultas mais complexas, considere também usar o [SQL Data Warehouse do Azure](https://azure.microsoft.com/services/sql-data-warehouse/).


## Topologias de consulta de Banco de Dados Elástico

### Topologia 1: particionamento vertical – consultas entre bancos de dados

Para começar a codificar, veja [Introdução à consulta entre bancos de dados (particionamento vertical)](sql-database-elastic-query-getting-started-vertical.md).

Uma consulta elástica pode ser usada para disponibilizar os dados localizados em um banco de dados do Banco de Dados SQL para outros bancos de dados do Banco de Dados SQL. Isso permite que consultas de um banco de dados façam referência a tabelas em qualquer outro banco de dados SQLDB remoto. A primeira etapa é definir uma fonte de dados externa para cada banco de dados remoto. A fonte de dados externa é definida no banco de dados local do qual você deseja obter acesso às tabelas localizadas no banco de dados remoto. Não são necessárias alterações no banco de dados remoto. Para cenários típicos de particionamento vertical em que bancos de dados diferentes têm esquemas diferentes, as consultas elásticas podem ser usadas para implementar casos de uso comuns, como o acesso aos dados de referência e consulta entre bancos de dados.

**Dados de referência**: a Topologia 1 é usada para o gerenciamento de dados de referência. Na figura abaixo, as duas tabelas (T1 e T2) com dados de referência são mantidas em um banco de dados dedicado. Com uma consulta elástica, agora você pode acessar as tabelas T1 e T2 remotamente em outros bancos de dados, como mostrado na figura. Use a topologia 1 se as tabelas de referência forem pequenas ou se as consultas remotas na tabela de referência tiverem predicados seletivos.

**Figura 2** Particionamento vertical - Usando a consulta elástica para consultar dados de referência

![Particionamento vertical - Usando a consulta elástica para consultar dados de referência][3]

**Consultas entre bancos de dados**: as consultas elásticas habilitam casos de uso que exigem consultas em vários bancos de dados SQLDB. A Figura 3 mostra quatro bancos de dados diferentes: CRM, Inventário, RH e Produtos. As consultas executadas em um banco de dados também precisam ter acesso a um ou todos os outros bancos de dados. Com uma consulta elástica, é possível configurar o banco de dados para esse caso, executando algumas instruções DDL simples em cada um dos quatro bancos de dados. Após essa configuração única, o acesso a uma tabela remota é tão simples quanto fazer referência a uma tabela local de suas consultas T-SQL ou de suas ferramentas de BI. Essa abordagem é recomendada se as consultas remotas não retornam grandes resultados.

**Figura 3** Particionamento vertical - Usando a consulta elástica para consultar vários bancos de dados

![Particionamento vertical - Usando a consulta elástica para consultar vários bancos de dados][4]

### Topologia 2: particionamento horizontal – fragmentação

O uso de uma consulta elástica para executar tarefas de relatórios em uma camada de dados fragmentados, ou seja, particionados horizontalmente requer um [mapa de fragmentos de banco de dados elástico](sql-database-elastic-scale-shard-map-management.md) para representar os bancos de dados da camada de dados. Normalmente, apenas um único mapa de fragmentos é usado neste cenário, e um banco de dados dedicado com recursos de consulta elástica serve como o ponto de entrada para consultas de relatórios. Somente este banco de dados dedicado precisa ter acesso ao mapa de fragmentos. A Figura 4 ilustra essa topologia e sua configuração com o banco de dados de consulta elástica e o mapa de fragmentos. Os bancos de dados na camada de dados podem ser de qualquer versão ou edição do Banco de Dados SQL do Azure. Para obter mais informações sobre a biblioteca de clientes do banco de dados elástico e sobre como criar mapas de fragmentos, veja [Gerenciamento do mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** Particionamento horizontal - Usando a consulta elástica para relatórios de camadas de dados fragmentados

![Particionamento horizontal - Usando a consulta elástica para relatórios de camadas de dados fragmentados][5]

> [AZURE.NOTE] O banco de dados dedicado da consulta de banco de dados elástico deve ser um banco de dados do Banco de Dados SQL v12. Não há nenhuma restrição sobre os próprios fragmentos.

Para começar a codificar, veja [Introdução à consulta de banco de dados elástico para particionamento horizontal (fragmentação)](sql-database-elastic-query-getting-started.md).

## Implementando consultas do banco de dados elástico

As etapas para implementar a consulta elástica para os cenários de particionamento vertical e horizontal são discutidas nas seções a seguir. Elas também fazem referência à documentação mais detalhada para os diferentes cenários de particionamento.

### Particionamento vertical - consultas entre bancos de dados

As seguintes etapas configuram consultas de banco de dados elástico para cenários de particionamento vertical que exigem o acesso a uma tabela localizada em um banco de dados SQLDB remoto com o mesmo esquema:

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource do tipo **RDBMS**
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Depois de executar as instruções DDL, você pode acessar a tabela remota “mytable” como se ela fosse uma tabela local. O Banco de Dados SQL do Azure automaticamente abre uma conexão com o banco de dados remoto, processa a solicitação no banco de dados remoto e retorna os resultados. Mais informações sobre as etapas necessárias para o cenário de particionamento vertical podem ser encontradas em [consulta elástica para o particionamento vertical](sql-database-elastic-query-vertical-partitioning.md).

### Particionamento horizontal - fragmentação

As seguintes etapas configuram consultas de banco de dados elástico para cenários de particionamento horizontal que exigem o acesso a um conjunto de tabelas que estão localizadas (normalmente) em vários bancos de dados SQLDB remotos:

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    Crie um [mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) que representa sua camada de dados usando a biblioteca de clientes do banco de dados elástico.   
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource do tipo **SHARD\_MAP\_MANAGER**
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Depois de realizar essas etapas, você pode acessar a tabela particionada horizontalmente “mytable” como se ela fosse uma tabela local. O Banco de Dados SQL do Azure automaticamente abre várias conexões paralelas com os bancos de dados remotos nos quais as tabelas estão armazenadas fisicamente, processa as solicitações nos bancos de dados remotos e retorna os resultados. Mais informações sobre as etapas necessárias para o cenário de particionamento horizontal podem ser encontradas em [consulta elástica para o particionamento horizontal](sql-database-elastic-query-horizontal-partitioning.md).

## Consultas T-SQL
Depois de definir as fontes de dados externas e as tabelas externas, é possível usar cadeias de conexão regulares do SQL Server para se conectar aos bancos de dados em que você definiu as tabelas externas. Você pode então executar instruções T-SQL em suas tabelas externas nessa conexão com as limitações descritas abaixo. Você pode encontrar mais informações e exemplos de consultas T-SQL nos tópicos da documentação sobre [particionamento horizontal](sql-database-elastic-query-horizontal-partitioning.md) e [particionamento vertical](sql-database-elastic-query-vertical-partitioning.md).

## Conectividade de ferramentas
Você pode usar cadeias de conexão regulares do SQL Server para conectar seus aplicativos e ferramentas de BI ou de integração de dados a bancos de dados que têm tabelas externas. Certifique-se de que o SQL Server tem suporte como uma fonte de dados para a ferramenta. Depois de conectado, consulte o banco de dados de consulta elástica e as tabelas externas nesse banco de dados exatamente como você faria com qualquer outro banco de dados do SQL Server ao qual você se conecta com a sua ferramenta.

## Custo

A consulta elástica está incluída no custo dos bancos de dados do Banco de Dados SQL do Azure. Observe que há suporte para as topologias nas quais os bancos de dados remotos residem em um datacenter diferente que o ponto de extremidade da consulta elástica, mas a saída de dados dos bancos de dados remotos é cobrada [às taxas normais do Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## Limitações de visualização
* A execução da primeira consulta elástica pode levar alguns minutos no nível de desempenho Standard. Esse tempo é necessário para carregar a funcionalidade de consulta elástica; o desempenho do carregamento melhora com níveis mais altos de desempenho.
* Ainda não há suporte para scripts de fontes de dados externas ou de tabelas externas do SSMS ou SSDT.
* A Importação/Exportação do Banco de Dados SQL ainda não dá suporte a tabelas externas e fontes de dados externas. Se precisar usar a função Importação/Exportação, remova esses objetos antes da exportação e depois recrie-os após a importação.
* Atualmente, a consulta de banco de dados elástico dá suporte apenas ao acesso somente leitura para tabelas externas. Você pode, no entanto, usar a funcionalidade completa do T-SQL no banco de dados no qual a tabela externa é definida. Isso pode ser útil para, por exemplo, manter os resultados temporários usando, por exemplo, SELECT <column_list> INTO <local_table>, ou para definir os procedimentos armazenados no banco de dados de consulta elástica que se referem a tabelas externas.
* Exceto nvarchar(max), não há suporte para tipos LOB em definições de tabela externa. Como uma solução alternativa, você pode criar uma exibição no banco de dados remoto que converte o tipo LOB em nvarchar(max), definir sua tabela externa na exibição em vez da tabela base e, em seguida, convertê-la novamente no tipo LOB original em suas consultas.
* Atualmente, não há suporte para estatísticas de coluna em tabelas externas. Há suporte para as estatísticas de tabelas, mas elas precisam ser criadas manualmente.

## Comentários
Compartilhe conosco seus comentários sobre sua experiência com as consultas elásticas no Disqus abaixo, nos fóruns do MSDN ou no Stackoverflow. Estamos interessados em todos os tipos de comentários sobre o serviço (defeitos, pontos em aberto, lacunas do recurso).

## Mais informações

Você pode encontrar mais informações sobre consultas entre bancos de dados e cenários de particionamento vertical nos seguintes documentos:

* [Visão geral de consultas entre bancos de dados e do particionamento vertical](sql-database-elastic-query-vertical-partitioning.md)
* Experimente nosso tutorial passo a passo para ver um exemplo completo e funcional em execução em minutos: [Introdução à consulta entre bancos de dados (particionamento vertical)](sql-database-elastic-query-getting-started-vertical.md).


Mais informações sobre os cenários de fragmentação e de particionamento horizontal estão disponíveis aqui:

* [Visão geral do particionamento horizontal e da fragmentação](sql-database-elastic-query-horizontal-partitioning.md)
* Experimente nosso tutorial passo a passo para ver um exemplo completo e funcional em execução em minutos: [Introdução à consulta de banco de dados elástico para particionamento horizontal (fragmentação)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->

<!---HONumber=AcomDC_0504_2016-->