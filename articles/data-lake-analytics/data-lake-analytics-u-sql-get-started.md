---
title: "Introdução à linguagem U-SQL | Microsoft Docs"
description: "Aprenda os conceitos básicos da linguagem U-SQL."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4884d96e8126337f62af23316935978cfe219ec8
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="get-started-with-u-sql"></a>Introdução ao U-SQL
U-SQL é uma linguagem que combina o SQL declarativo com o C# imperativo para permitir que você processe dados em qualquer escala. Por meio da capacidade de consulta distribuída escalonável do U-SQL, você pode analisar, de modo eficiente, dados entre repositórios relacionais como o Banco de Dados SQL do Azure. Com o U-SQL, você pode processar dados não estruturados aplicando o esquema na leitura e inserindo lógica personalizada e UDFs. Além disso, o U-SQL inclui extensibilidade que lhe dá controle refinado sobre como executar em escala. 

## <a name="learning-resources"></a>Recursos de aprendizagem

Para obter informações detalhadas sobre a **sintaxe da linguagem U-SQL**, consulte a [U-SQL Language Reference](http://go.microsoft.com/fwlink/p/?LinkId=691348) (Referência da linguagem U-SQL).

Para entender a **filosofia de design do U-SQL**, confira a postagem do blog do Visual Studio [Introducing U-SQL – A Language that makes Big Data Processing Easy](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/) (Apresentando o U-SQL – Uma linguagem que torna fácil o processamento de Big Data).

## <a name="prerequisites"></a>Pré-requisitos

Antes de percorrer os exemplos do U-SQL nesse documento, leia e conclua o [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Esse tutorial explica a mecânica do uso do U-SQL com as Ferramentas do Azure Data Lake para Visual Studio.

## <a name="your-first-u-sql-script"></a>Seu primeiro script U-SQL

O script U-SQL a seguir é muito simples e nos permite explorar muitos aspectos da linguagem U-SQL.

```
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
```

Esse script não possui nenhuma etapa de transformação. Ele faz a leitura do arquivo de origem chamado `SearchLog.tsv`, o esquematiza e grava o conjunto de linhas de volta em um arquivo chamado SearchLog-first-u-sql.csv.

Observe o ponto de interrogação ao lado do tipo de dados no campo `Duration`. Isso significa que o campo `Duration` pode ser nulo.

### <a name="key-concepts"></a>Principais conceitos
* **Variáveis de conjunto de linhas**: cada expressão de consulta que produz um conjunto de linhas pode ser atribuído a uma variável. O U-SQL segue o padrão de nomenclatura de variável do T-SQL (`@searchlog`, por exemplo) no script.
* A palavra-chave **EXTRACT** lê dados de um arquivo e define o esquema na leitura. `Extractors.Tsv` é um extrator U-SQL interno para arquivos de valores separados por tabulação. Você pode desenvolver extratores personalizados.
* **OUTPUT** grava dados de um conjunto de linhas em um arquivo. `Outputters.Csv()` é um outputter U-SQL interno para criar um arquivo de valores separados por vírgulas. Você pode desenvolver outputters personalizados.

### <a name="file-paths"></a>Caminhos de arquivo

As instruções EXTRACT e OUTPUT usam caminhos de arquivo. Os caminhos de arquivo podem ser absolutos ou relativos:

Esse caminho de arquivo absoluto se refere a um arquivo em um Data Lake Store chamado `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Esse caminho de arquivo absoluto se refere a um arquivo em uma Conta de Armazenamento de Blobs do Azure chamada `myblobaccount` e em um contêiner chamado `mycontainer`:

    wasb://mycontainer@myblobaccount.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >Atualmente, não há suporte para contêineres de Armazenamento de Blobs do Azure com permissões de acesso para blobs públicos ou contêineres públicos.

Esse caminho de arquivo relativo começa com `"/"`. Ele se refere a um arquivo na conta do Data Lake Store que está associada à conta do Data Lake Analytics:

    TO "/output/SearchLog-first-u-sql.csv"

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
        TO "/output/SearchLog-transform-datetime.csv"
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

A cláusula ORDER BY do U-SQL requer o uso da cláusula FETCH em uma expressão SELECT.

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

## <a name="see-also"></a>Consulte também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar funções da janela do U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)

## <a name="let-us-know-what-you-think"></a>Queremos saber sua opinião
* [Enviar uma solicitação de recurso](http://aka.ms/adlafeedback)
* [Obter ajuda nos fóruns](http://aka.ms/adlaforums)
* [Fornecer comentários sobre o U-SQL](http://aka.ms/usqldiscuss)

