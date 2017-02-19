---
title: "Tutorial: introdução à linguagem U-SQL do Azure Data Lake Analytics | Microsoft Docs"
description: Use este tutorial para saber mais sobre a linguagem U-SQL do Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 96747e898b2d84cbce9251758a90941f85112dd0
ms.openlocfilehash: 7aa3844b9bdc2c5372c6e54b05296dcde6b1c05f


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Tutorial: introdução à linguagem U-SQL da Análise do Azure Data Lake
O U-SQL é uma linguagem que combina as vantagens do SQL com o poder expressivo do seu próprio código para processar dados em qualquer escala. Por meio da capacidade de consulta distribuída escalonável do U-SQL, você pode analisar, de modo eficiente, dados entre repositórios relacionais como o Banco de Dados SQL do Azure. Com o U-SQL, você pode processar dados não estruturados aplicando o esquema na leitura e inserindo lógica personalizada e UDFs. Além disso, o U-SQL inclui extensibilidade que lhe dá controle refinado sobre como executar em escala. Para saber mais sobre a filosofia de design por trás do U-SQL, veja a postagem de blog do Visual Studio [Introducing U-SQL – A Language that makes Big Data Processing Easy (Apresentando U-SQL – Uma linguagem que torna o processamento de Big Data fácil)](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

O U-SQL difere de ANSI ou T-SQL em alguns sentidos. Por exemplo, palavras-chave como SELECT devem estar apenas em letras maiúsculas.

 Seu sistema de tipo e de linguagem de expressão, em cláusulas SELECT e predicados WHERE, estão em C#. Isso significa que os tipos de dados são os tipos C#, que usam a semântica C# NULL e que as operações de comparação dentro de um predicado seguem uma sintaxe C# (por exemplo, um == "foo"). Isso também significa que os valores são objetos .NET completos, de modo que você pode usar facilmente qualquer método para operar no objeto (por exemplo, "f o o o".Split(' ')).

Para obter mais informações sobre U-SQL, veja [Referência da linguagem U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

### <a name="prerequisites"></a>Pré-requisitos
Se você ainda não fez isso, leia e conclua o [Tutorial: desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Depois que você tiver concluído o tutorial, retorne a este artigo.

No tutorial, você executou um trabalho do Azure Data Lake Analytics com o seguinte script U-SQL:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Esse script não possui nenhuma etapa de transformação. Ele faz a leitura do arquivo de origem chamado SearchLog.tsv, o esquematiza e grava o conjunto de linhas de volta em um arquivo chamado SearchLog-first-u-sql.csv.

Observe o ponto de interrogação ao lado do tipo de dados no campo **Duração**. Isso significa que o campo **Duração** pode ser nulo.

No script, você encontrará os conceitos e as palavras-chave a seguir:

* Variáveis de conjunto de linhas: cada expressão de consulta que produz um conjunto de linhas pode ser atribuído a uma variável. O U-SQL segue o padrão de nomenclatura variável do T-SQL (por exemplo, (@searchlog,) no script.

 >[!NOTE]
 >A atribuição não força a execução. Ela simplesmente nomeia a expressão de modo que você possa criar expressões mais complexas.
* EXTRACT: usando essa palavra-chave, você pode definir um esquema na leitura. O esquema é especificado por um nome de coluna e um par de nome do tipo C# por coluna. O esquema usa o que chamamos de extrator (por exemplo, Extractors.Tsv()) para extrair arquivos tsv. Você pode desenvolver extratores personalizados.
* OUTPUT: esta palavra-chave usa um conjunto de linhas e o serializa. O Outputters.Csv() grava um arquivo separado por vírgula no local especificado. Você também pode desenvolver criadores de saída personalizados.

 >[!NOTE]
 >Os dois caminhos são caminhos relativos. Você também pode usar caminhos absolutos. Por exemplo:    
 >     adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
 >
 >Você deve usar um caminho absoluto para acessar os arquivos nas contas de armazenamento vinculadas.  A sintaxe para os arquivos armazenados na conta de armazenamento do Azure vinculada é: wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >Atualmente, não há suporte para contêineres de Armazenamento de Blobs do Azure com permissões de acesso para blobs públicos ou contêineres públicos.

## <a name="use-scalar-variables"></a>Usar variáveis escalares
Você pode usar variáveis escalares também para facilitar a manutenção do seu script. O script U-SQL anterior também pode ser escrito da seguinte forma:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformar conjuntos de linhas
Use **SELECT** para transformar conjuntos de linhas:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

A cláusula WHERE usa uma [expressão booliana C#](https://msdn.microsoft.com/library/6a71f45d.aspx). Você pode usar a linguagem de expressão do C# para fazer suas próprias expressões e funções. Você pode até mesmo executar uma filtragem mais complexa combinando-os com associações lógicas (ANDs) e desassociações (ORs).

O script a seguir usa o método DateTime.Parse() e um conjunto.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >A segunda consulta está operando no resultado do primeiro conjunto de linhas, que cria uma composição dos dois filtros. Também é possível reutilizar um nome de variável; o escopo dos nomes é definido lexicalmente.

## <a name="aggregate-rowsets"></a>Agregar conjuntos de linhas
O U-SQL fornece a você as conhecidas funções ORDER BY, GROUP BY e agregações.

A consulta a seguir localiza a duração total por região e então exibe as cinco maiores durações, na ordem.

Os conjuntos de linhas do U-SQL não mantêm a ordem para a consulta seguinte. Portanto, para solicitar uma saída, você precisa adicionar ORDER BY à instrução OUTPUT:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

A cláusula ORDER BY do U-SQL deve ser combinada com a cláusula FETCH em uma expressão SELECT.

A cláusula HAVING do U-SQL pode ser usada para restringir a saída aos grupos que satisfazem a condição de HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Join data
O U-SQL fornece operadores de junção comum, como INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN, SEMI JOIN, para unir não só tabelas, mas quaisquer conjuntos de linhas (mesmo aqueles gerados de arquivos).

O script a seguir une o log de pesquisa com um log de impressão de anúncio e fornece os anúncios para a cadeia de caracteres de consulta de determinada data.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


O U-SQL dá suporte apenas à sintaxe de junção em conformidade com ANSI: predicado Rowset1 JOIN Rowset2 ON. _Não_ há suporte para a sintaxe antiga do predicado FROM Rowset1, Rowset2 WHERE.
O predicado em JOIN deve ser uma junção de igualdade e não ter expressão. Se você quiser usar uma expressão, adicione-o à cláusula select de um rowset anterior. Se você quiser fazer uma comparação diferente, pode movê-lo para a cláusula WHERE.

## <a name="create-databases-table-valued-functions-views-and-tables"></a>Criar bancos de dados e tabelas, funções com valor de tabela, exibições e tabelas
Em U-SQL, você pode usar dados no contexto de um banco de dados e esquema e não precisa sempre ler ou gravar arquivos.

Todos os scripts U-SQL são executados com um banco de dados padrão (mestre) e um esquema padrão (dbo) como seu contexto padrão. Você pode criar seu próprio banco de dados ou esquema. Para alterar o contexto, use a instrução USE.

### <a name="create-a-tvf"></a>Criar um TVF
No script U-SQL anterior, você repetiu o uso de EXTRACT para ler do mesmo arquivo de origem. Com o TVF (função com valor de tabela) do U-SQL, você pode encapsular os dados para uma futura reutilização.  

O script a seguir cria um TVF chamado *Searchlog()* no banco de dados e esquema padrão:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

O script a seguir mostra como usar o TVF definido no script anterior:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Criar exibições
Se você tiver somente uma expressão de consulta que deseja abstrair e não quiser criar um parâmetro com base nela, poderá criar uma exibição em vez de uma função com valor de tabela.

O script a seguir cria uma exibição chamada *SearchlogView* no banco de dados e esquema padrão:

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

O script a seguir demonstra o uso da exibição definida:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Criar tabelas
Assim como ocorre com tabelas de banco de dados relacional, com o U-SQL você pode criar uma tabela com um esquema predefinido ou criar uma tabela que infere o esquema da consulta que popula a tabela (também conhecido como CREATE TABLE AS SELECT ou CTAS).

Cria um banco de dados e duas tabelas pelo uso do script a seguir:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SearchLogDb;
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    DISTRIBUTED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Consultar tabelas
Você pode consultar tabelas como aquelas criadas no script anterior, da mesma maneira que consulta os arquivos de dados. Em vez de criar um conjunto de linhas usando EXTRACT, agora você pode simplesmente consultar o nome da tabela.

Para ler das tabelas, modifique o script de transformação que você usou anteriormente:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

 >[!NOTE]
 >Atualmente você não pode executar SELECT em uma tabela no mesmo script em que você criou tal tabela.

## <a name="conclusion"></a>Conclusão
Este tutorial aborda apenas uma pequena parte do U-SQL. Devido a seu escopo limitado, o tutorial não discutiu muitos outros benefícios do U-SQL. Por exemplo, você pode:

* Usar CROSS APPLY para descompactar partes de cadeias de caracteres, matrizes e mapas em linhas.
* Operar conjuntos particionados de dados (conjuntos de arquivos e tabelas particionadas).
* Desenvolva operadores definidos pelo usuário, como extratores, criadores de saída, processadores e agregadores definidos pelo usuário em C#.
* Usar funções de janela U-SQL.
* Gerencie o código U-SQL com exibições, funções com valor de tabela e procedimentos armazenados.
* Executar código personalizado arbitrário em seus nós de processamento.
* Conecte-se a Bancos de Dados SQL do Azure e faça a federação de consultas entre eles e seus dados U-SQL e Azure Data Lake.

## <a name="see-also"></a>Consulte também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar funções da janela do U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Queremos saber sua opinião
* [Enviar uma solicitação de recurso](http://aka.ms/adlafeedback)
* [Obter ajuda nos fóruns](http://aka.ms/adlaforums)
* [Fornecer comentários sobre o U-SQL](http://aka.ms/usqldiscuss)



<!--HONumber=Dec16_HO4-->


