<properties 
   pageTitle="Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio | Azure" 
   description="Saiba como instalar ferramentas do Data Lake para o Visual Studio, como desenvolver e testar scripts U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/26/2015"
   ms.author="jgao"/>

# Tutorial: introdução à linguagem U-SQL da Análise do Azure Data Lake

Saiba como desenvolver scripts U-SQL.

O U-SQL é uma linguagem que unifica as vantagens do SQL com o poder expressivo do seu próprio código para processar todos os dados em qualquer escala. A capacidade de consulta distribuída escalonável do U-SQL permite a análise eficiente dos dados no repositório e entre repositórios relacionais, como o Banco de Dados SQL do Azure. Ele permite que você processe dados não estruturados aplicando o esquema na leitura, na inserção de lógica personalizada e em UDFs, e inclui extensibilidade para habilitar o controle refinado sobre como executar em grande escala. Para saber mais sobre a filosofia de design por trás do U-SQL, consulte a esta [postagem de blog do Visual Studio](http://blogs.msdn.com/b/visualstudio/archive/2015/09/28/introducing-u-sql.aspx).

Existem algumas diferenças de T-SQL ou ANSI SQL. Por exemplo, as palavras-chave, como SELECT, precisam estar em MAIÚSCULAS.

É um sistema de inserção e de linguagem de expressão em cláusulas SELECT, em que os predicados e afins estão em C#. Isso significa que os tipos de dados são os tipos C# e os tipos de dados usam a semântica C# NULL, e as operações de comparação dentro de um predicado seguem uma sintaxe C# (por exemplo, um == "foo"). Isso também significa que os valores são objetos totalmente .NET, permitindo que você use facilmente qualquer método para operar no objeto (por exemplo, "f o o o".Split(' ')).

Para saber mais, consulte [Referência U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

**Pré-requisitos**

- **Visual Studio 2015, Visual Studio 2013 atualização 4 ou Visual Studio 2012 com Visual C++ instalado** 
- **SDK do Microsoft Azure para .NET versão 2.5 ou posterior**. Instale-o usando o [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Ferramentas do Data Lake para Visual Studio](http://aka.ms/adltoolsvs)**. 
	
	Quando as Ferramentas do Data Lake para Visual Studio forem instaladas, você verá um menu do **Data Lake** no Visual Studio:
	
	![Menu Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Conhecimento básico da Análise Data Lake e das Ferramentas do Data Lake para Visual Studio**. Para começar. confira:
 
	- [Introdução à Análise do Azure Data Lake usando o Portal de Visualização do Azure](data-lake-analytics-get-started-portal.md).
	- [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)

- **Uma conta da Análise do Data Lake**. Veja [Criar uma conta da Análise do ADL (Azure Data Lake)](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
- **Carregue os dados de exemplo na conta da Análise Data Lake.** Confira [Carregar SearchLog.tsv na conta padrão de Armazenamento do Data Lake](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

	As Ferramentas do Data Lake não dão suporte à criação de contas da Análise do Data Lake. Você precisa criá-la usando o Portal de Visualização do Azure, Azure PowerShell, SDK do .NET ou a CLI do Azure. Para executar o trabalho da Análise Data Lake, você precisará de alguns dados. Embora as Ferramentas do Data Lake deem suporte ao carregamento de dados, você poderá usar o portal para carregar os dados de exemplo para deixar o tutorial mais fácil de acompanhar.

## Conectar-se ao Azure do Visual Studio

Antes de criar e testar qualquer script U-SQL, é preciso se conectar ao Azure.

**Para conectar-se à Análise Data Lake**

1. Abra o Visual Studio.
2. No menu **Data Lake**, clique em **Opções e Configurações**.
4. Clique em **Entrar** ou em **Alterar Usuário** se alguém estiver conectado e siga as instruções para entrar.
5. Clique em **OK** para fechar a caixa de diálogo Opções e Configurações.

**Para procurar suas contas da Análise Data Lake**

1. No Visual Studio, abra o **Gerenciador de Servidores** pressionando **CTRL+ALT+S**.
2. No **Gerenciador de Servidores**, expanda **Azure** e **Análise Data Lake**. Você deverá ver uma lista das suas contas da Análise Data Lake, caso haja alguma. Não é possível criar contas da Análise Data Lake a partir do estúdio. Para criar uma conta, confira [Introdução à Análise Azure Data Lake usando o Portal de Visualização do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução à Análise Azure Data Lake usando o Azure PowerShell](data-lake-get-started-powershell.md)


## Desenvolver seus primeiros scripts U-SQL 

O objetivo principal desta seção é entender o processo de escrever e testar um aplicativo U-SQL usando ferramentas Data Lake para Visual Studio. As instruções U-SQL também são usadas em outros tutoriais de Análises Data Lake. Ele simplesmente lê um arquivo delimitado por tabulação (tsv) e gera o arquivo como um arquivo delimitado por vírgulas (csv).
 
**Para criar e enviar um trabalho da Análise Date Lake**

1. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
2. Selecione o tipo Projeto do U-SQL.

	![novo projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
	
3. Clique em **OK**. O Visual Studio cria uma solução com um arquivo Script.usql.
4. Insira o seguinte script no painel de script:

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

		The next section in this article will explain the script in details.  You just need to focus on understanding the development and testing process in this section.
5. Ao lado do botão **Enviar**, especifique a conta de análise.
5. No **Gerenciador de Soluções**, clique com botão direito do mouse em **Script.usql** e clique em **Criar Script**. Verifique o resultado no painel Saída. Se você tiver um erro no script, verá aqui uma saída de erro. 
6. No **Gerenciador de Soluções**, clique com botão direito do mouse em **Script.usql** e clique em **Enviar Script**.
7. Verifique a **Análise de Conta**, e clique em **Enviar**. Os resultados do envio e o link do trabalho ficarão disponíveis na janela de resultados das Ferramentas do Date Lake para Visual Studio quando o envio for concluído.
8. Você pode clicar no botão **Atualizar** para ver o status do trabalho mais recente e atualizar a tela. Aguarde até que o trabalho seja concluído com êxito. Se houver falha no trabalho, é provável que esteja faltando o arquivo original. Você pode usar a guia "erro" na ferramenta para ver o erro retornado do serviço. Consulte a seção Pré-requisito deste tutorial. Para saber mais, consulte [Monitorar e solucionar problemas com trabalhos de análise do Azure Data Lake](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

	
**Para ver a saída do trabalho**

1. No **Gerenciador de Servidores**, expanda o **Azure**, expanda a **Análise Data Lake**, expanda sua conta da Análise Data Lake, expanda **Contas de Armazenamento**, clique com o botão direito do mouse na conta padrão de Armazenamento do Data Lake e clique em **Gerenciador**. 
2.  Clique duas vezes em **Saída** para abrir a pasta
3.  Clique duas vezes em **SearchLog-frist-u-sql.csv**.
4.  Também é possível clicar duas vezes no arquivo de saída no modo de exibição de gráficos a fim de navegar diretamente até o arquivo de saída.

## Noções básicas sobre o primeiro script U-SQL

Vamos examinar melhor o script que você escreveu na última seção:

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
            TO "/output/SearchLog-first-usql.csv"
        USING Outputters.Csv();

Esse script não possui nenhuma etapa de transformação. Ele lê a partir do arquivo de origem chamado **SearchLog.tsv**, esquematiza-o e gera o conjunto de linhas de volta em um arquivo chamado **SearchLog-from-adltools.csv**.

Observe o ponto de interrogação ao lado do tipo de dados do campo Duração. Isso significa que o campo Duração pode ser null.

Alguns conceitos e palavras-chave encontrados no script:

- **Variáveis de conjunto de linhas**: cada expressão de consulta que produz um conjunto de linhas pode ser atribuído a uma variável. O U-SQL segue o padrão de nomenclatura variável do T-SQL, por exemplo, **@searchlog** no script. Observe que a atribuição não força a execução. Ele simplesmente nomeia a expressão e dá a você a capacidade de criar expressões mais complexas.
- **EXTRACT** lhe dá a capacidade de definir um esquema na leitura. O esquema é especificado por um nome de coluna e um par de nome do tipo C# por coluna. Ele usa um **Extrator**, por exemplo, **Extractors.Tsv()**, para extrair arquivos tsv. Você pode desenvolver extratores personalizados.
- **SAÍDA** usa um conjunto de linhas e o serializa. O Outputters.Csv() cria como saída um arquivo separado por vírgulas no local indicado. Você também pode desenvolver Saídas personalizadas.
- Observe que os dois caminhos são caminhos relativos. Você também pode usar caminhos absolutos. Por exemplo, 
    
        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Você deve usar um caminho absoluto para acessar os arquivos nas contas de armazenamento vinculadas. A sintaxe para os arquivos armazenados na conta do Armazenamento do Azure vinculada é:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Atualmente, não há suporte para o contêiner de Blob do Azure com permissões de acesso de blobs públicos ou de contêineres públicos.

## Usar variáveis escalares

Você pode usar variáveis escalares também para facilitar a manutenção do seu script. O primeiro script U-SQL também pode ser escrito da seguinte forma:

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
      
##Transformar conjuntos de linhas

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

A cláusula WHERE usa [expressão booliana C#](https://msdn.microsoft.com/library/6a71f45d.aspx). Você pode usar a linguagem de expressão do C# para fazer suas próprias expressões e funções. Você pode até mesmo executar uma filtragem mais complexa combinando-os com associações lógicas (e) e desassociações (ou).

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
        
Observe que a segunda consulta está operando no resultado do primeiro conjunto de linhas e, portanto, o resultado é uma composição de dois filtros. Também é possível reutilizar um nome de variável; os nomes passam lexicalmente.

##Agregar conjuntos de linhas

O U-SQL fornece as conhecidas funções **ORDER BY**, **GROUP BY** e agregações.

A consulta a seguir localiza a duração total por região e gera as 5 principais durações na ordem.

Os conjuntos de linhas do U-SQL não mantêm a ordem para a consulta seguinte. Portanto, para solicitar uma saída, você precisa adicionar ORDER BY na instrução de saída, conforme mostrado abaixo:

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
        
A cláusula U-SQL ORDER BY deve ser combinada com a cláusula FETCH em uma expressão SELECT.

A cláusula U-SQL HAVING pode ser usada para restringir a saída aos grupos que satisfazem a condição HAVING:

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


## Join data

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


O U-SQL só dá suporte à sintaxe de junção compatível com ANSI: predicado Rowset1 JOIN Rowset2 ON. Não há suporte para o predicado FROM Rowset1, Rowset2 WHERE. O predicado em JOIN deve ser uma junção de igualdade e não ter expressão. Se você quiser usar uma expressão, adicione-o à cláusula select de um rowset anterior. Se você quiser fazer uma comparação diferente, pode movê-lo para a cláusula WHERE.

        
## Criar bancos de dados e tabelas, funções com valor de tabela, exibições e tabelas

O U-SQL permite que você use dados no contexto de banco de dados e esquema. Portanto, você não precisa sempre ler ou gravar em arquivos.

Todos os scripts U-SQL são executados com um banco de dados padrão (mestre) e um esquema padrão (dbo) como seu contexto padrão. Você pode criar seu próprio banco de dados e/ou esquema. Para alterar o contexto, use a instrução **USE** para alterar o contexto.


### Criar uma função com valor de tabela (TVF)

No script U-SQL anterior, você repetiu usando a leitura EXTRACT do mesmo arquivo de origem. A função com valor de tabela do U-SQL permite que você encapsule os dados para uma futura reutilização.

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
        
### Criar exibições

Se você tiver somente uma expressão de consulta que deseja abstrair e não quiser parametrizá-la, pode criar uma exibição em vez de uma função com valor de tabela.

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
    
O script a seguir demonstra usando o modo de exibição definido:

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

### Criar tabelas 

Semelhante à tabela de banco de dados relacional, o U-SQL permite que você crie uma tabela com um esquema predefinido ou crie uma tabela e interprete o esquema da consulta que preenche a tabela (também conhecido como CREATE TABLE AS SELECT ou CTAS).

O script a seguir cria um banco de dados e duas tabelas:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
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
                    PARTITIONED BY HASH (UserId)
    );
    
    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;
    
    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC) 
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### Consultar tabelas

Você pode consultar as tabelas (criadas no script anterior) da mesma maneira que faz consulta sobre os arquivos de dados. Em vez de criar um conjunto de linhas usando EXTRACT, você agora pode simplesmente consultar o nome da tabela.

O script de transformação que você usou anteriormente será modificado para ler nas tabelas:

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

Observe que, no momento, não é possível executar SELECT em uma tabela no mesmo script onde você cria a tabela.


##Conclusão

O que é abordado no tutorial é apenas uma pequena parte do U-SQL. Por causa do escopo deste tutorial, não é possível abordar tudo, como:

- Usar CROSS APPLY para descompactar partes de cadeias de caracteres, matrizes e mapas em linhas.
- Operar conjuntos particionados de dados (conjuntos de arquivos e tabelas particionadas).
- Desenvolver operadores definidos pelo usuário, como extratores, criadores de saída, processadores, agregadores definidos pelo usuário em C#.
- Usar funções de janela U-SQL.
- Gerenciar o código de U-SQL com exibições, funções com valor de tabela e procedimentos armazenados.
- Executar código personalizado arbitrário em seus nós de processamento. 
- Conectar-se a bancos de dados SQL Azure e federar consultas entre eles e seus dados U-SQL e Azure Data Lake.

## Consulte também 

- [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Usando funções da janela do U-SQL para trabalhos da Análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)
- [Monitorar e solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal de Visualização do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## Queremos saber sua opinião

- [Sugerir nova lista de pendências de documentação](data-lake-analytics-documentation-backlog.md)
- [Enviar uma solicitação de recurso](http://aka.ms/adlafeedback)
- [Obter ajuda nos fóruns](http://aka.ms/adlaforums)
- [Fornecer comentários sobre o U-SQL](http://aka.ms/usqldiscuss)

<!---HONumber=Nov15_HO1-->