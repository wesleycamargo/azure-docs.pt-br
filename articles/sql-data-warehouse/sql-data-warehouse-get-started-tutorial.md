---
title: "SQL Data Warehouse - tutorial de Introdução | Microsoft Docs"
description: "Tutorial de Introdução do Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: fe9de0ffad3fe5d4acbf3caf2f08101f6a13daaf
ms.openlocfilehash: 12f500c01671799612b0d1988e0d07bbfda600da


---
# <a name="get-started-with-sql-data-warehouse"></a>Introdução ao SQL Data Warehouse

Tutorial de Introdução do Azure SQL Data Warehouse. Este tutorial ensina as noções básicas de provisionamento e carregamento de dados em um SQL Data Warehouse, bem como algumas noções básicas sobre dimensionamento, pausa e ajuste. 

**Tempo estimado para conclusão:** 75 minutos

## <a name="prerequisites"></a>Pré-requisitos


### <a name="sign-up-for-microsoft-azure"></a>Inscreva-se no Microsoft Azure
Se ainda não tiver uma conta do Microsoft Azure, você deverá inscrever-se para usar este serviço. Se já tiver uma conta, você poderá ignorar esta etapa. 

1. Navegue até as páginas de conta [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Crie uma conta gratuita do Azure ou uma conta de compra.
3. Siga as instruções

### <a name="install-appropriate-sql-client-driver-and-tools"></a>Instalar o Driver do Cliente SQL e as ferramentas apropriadas

A maioria das ferramentas de cliente SQL pode se conectar ao Azure SQL Data Warehouse usando JDBC, ODBC ou ADO.net. Devido à complexidade do produto e ao grande número de recursos T-SQL aos quais o SQL Data Warehouse dá suporte, nem todos os aplicativos cliente podem ser totalmente compatíveis com o SQL Data Warehouse.

Se você está executando um sistema operacional Windows, é recomendável usar o [Visual Studio] ou o [SQL Server Management Studio].


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Criar um Azure SQL Data Warehouse

> [!NOTE]
> A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável.  Para obter mais informações, confira [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>


### <a name="create-a-sql-data-warehouse"></a>Criar um SQL Data Warehouse
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Bancos de dados** > **SQL Data Warehouse**.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Preencher os detalhes da implantação

    **Nome do banco de dados**: escolha qualquer item que desejar. Se você tiver várias instâncias do SQL DW, recomendamos que os nomes incluam detalhes como sua região, ambiente etc., por exemplo, *mydw-westus-1-test*

    **Assinatura:** sua assinatura do Azure

    **Grupo de Recursos**: crie um novo (ou use um existente, se você planeja usar o Azure SQL Data Warehouse com outros serviços)
    > [!NOTE]
    > Os serviços em um grupo de recursos devem ter o mesmo ciclo de vida. Grupos de recursos são úteis para a administração de recursos como controle de acesso de escopo e implantação de modelo. Leia mais sobre grupos de recursos do Azure e as práticas recomendadas [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)
    >

    **Origem**: banco de dados em branco

    **Servidor**: selecione o servidor que você criou em [Pré-requisitos].

    **Agrupamento**: mantenha o agrupamento padrão SQL_Latin1_General_CP1_CI_AS

    **Selecionar o desempenho**: recomendamos o uso do padrão de 400 DWU

4. Escolha **Fixar no painel**
    ![Fixar no painel](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Relaxe e aguarde até que o SQL Data Warehouse do Azure realize a implantação! É normal que esse processo leve vários minutos. O portal será notificado quando a instância terminar a implantação. 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>Conectar-se ao Azure SQL Data Warehouse por meio do SQL Server (servidor lógico)

Este tutorial usa o SQL Server Management Studio para conectar-se a nosso SQL Data Warehouse. Outras ferramentas podem ser usadas para se conectar ao SQL Data Warehouse por meio dos conectores com suporte: ADO.NET, JDBC, ODBC e PHP. Lembre-se de que a funcionalidade pode ser limitada para ferramentas sem suporte pela Microsoft.


### <a name="get-connection-information"></a>Obter informações de conexão

Para conectar-se ao SQL Data Warehouse, você deve se conectar por meio do SQL Server (servidor lógico) criado em [Pré-requisitos].

1. Selecione o SQL Data Warehouse no painel ou pesquise-o em seus recursos.

    ![Painel do SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Localize o nome completo para o servidor lógico.

    ![Selecionar o nome do servidor](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Abra o SSMS e usar o Pesquisador de Objetos para se conectar a este servidor usando as credenciais que você criou em [Pré-requisitos]

    ![Conectar com SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Se tudo ocorrer corretamente, agora você deverá estar conectado à instância do SQL Server (servidor lógico). Você pode usar as credenciais do servidor para se autenticar em qualquer banco de dados no servidor como o proprietário do banco de dados. No entanto, como prática recomendada, você deve criar usuários e logons diferentes para cada banco de dados. Vamos explorar a criação de usuário em [Criar um usuário para o SQL Data Warehouse](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse). 

## <a name="create-a-user-for-sql-data-warehouse"></a>Criar um usuário para o SQL Data Warehouse

### <a name="why-create-a-separate-user"></a>Por que criar um usuário separado?

Usamos a conexão com o SQL Server (servidor lógico), com credenciais de servidor na etapa anterior, para criar um novo usuário para o SQL Data Warehouse. Há duas razões principais pelas quais convém criar um logon/usuário separado para o SQL DW.

1.  Os usuários da organização devem usar uma conta diferente para autenticação. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas.

2. Por padrão, o logon de administrador do servidor ao qual você está conectado no momento usa uma classe de recurso menor. As classes de recurso ajudam a controlar a alocação de memória e os ciclos de CPU fornecidos para uma consulta. Os usuários em **smallrc** recebem uma quantidade menor de memória e podem tirar proveito da simultaneidade superior. Por outro lado, os usuários atribuídos a **xlargerc** recebem grandes quantidades de memória e, assim, um número menor dessas consultas pode ser executado simultaneamente. Para carregar dados de uma maneira que otimiza melhor a compactação, convém certificar-se de que o usuário que carrega os dados faz parte de uma classe de recursos maior. Leia mais sobre classes de recursos [aqui](./sql-data-warehouse-develop-concurrency.md#resource-classes):

### <a name="creating-a-user-of-a-larger-resource-class"></a>Criar um usuário de uma classe de recurso maior

1. Consultar o banco de dados **mestre** do servidor

    ![Nova consulta no mestre](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Nova consulta em Master1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Criar um logon de servidor e usuário

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. Consultar o banco de dados do SQL DataWarehouse, criar um novo usuário de banco de dados com base no logon de servidor 
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. Conceder controle do banco de dados ao usuário
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Se o nome do banco de dados contiver hifens, coloque-o entre colchetes! 
    >

5. Adicione o usuário de banco de dados à função de classe do recurso **xlargerc**
    ```sql
    EXEC sp_addrolemember 'xlargerc', 'LoadingUser';
    ```

6. Fazer logon no banco de dados com suas novas credenciais

    ![Fazer logon com o novo logon](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>Carregando dados

### <a name="defining-external-data"></a>Definindo dados externos
1. Criar uma chave mestra e definir uma fonte de dados externa

    ```sql
    CREATE MASTER KEY;

    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. Definir os formatos de arquivo externos

    O comando ```CREATE EXTERNAL FILE FORMAT``` é usado para especificar o formato dos dados externos dos quais você está carregando. Para os dados de táxis públicos de Nova York, usamos os dois formatos para armazenar os dados no armazenamento de Blobs do Azure

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  Criar um esquema para o formato de arquivo externo

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. Crie as tabelas externas. Essas tabelas fazem referência a dados armazenados no armazenamento de blobs do Azure ou HDFS. 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
    )
    WITH
    (
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Create Table as Select (CTAS)

5. Carrega os dados de tabelas externas em sua instância do SQL Data Warehouse. 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > Você está carregando vários GBs de dados e compactando-os em índices Columnstore de cluster de alto desempenho. Execute a consulta DMV a seguir e faça uma pausa para o café ou lanche enquanto o Azure SQL Data Warehouse faz o trabalho pesado.
    >

6. Criar uma nova consulta e ver como seus dados chegam com DMV (Exibição de Gerenciamento Dinâmico)

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. Exibir todas as consultas do sistema

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. Veja os dados carregados sem problemas no Azure SQL Data Warehouse

    ![Ver dados carregados](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>Consultando dados 

### <a name="scan-query-with-scaling"></a>Verificar a consulta com o dimensionamento

Vamos ter uma boa noção de como o dimensionamento afeta a velocidade de suas consultas.

Antes de começar, vamos expandir nossa operação até 100 DWUs para lhe dar uma ideia de como a computação como um nó pode ser executada por conta própria.

1. Vá para o portal e selecione a instância do SQL Data Warehouse

2. Selecione dimensionar na folha SQL Data Warehouse. 

    ![Dimensionar DW no portal](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Reduza a barra de desempenho para 100 DWU e clique em Salvar.

    ![Dimensionar e salvar](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Aguarde a conclusão da operação de dimensionamento.

    > [!NOTE]
    > Observe que operações de dimensionamento **eliminam** as consultas em execução e evitam que novas consultas sejam executadas.
    >
    
5. Faça uma operação de verificação nos dados de viagem, selecionando os milhões de entradas principais para todas as colunas. Se está ansioso para continuar rapidamente, fique à vontade para selecionar menos linhas.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

Anote o tempo necessário para executar esta operação.

6. Dimensione sua instância para 400 DWU. Lembre-se de que cada 100 DWU adicionam outro nó de computação ao Azure SQL Data Warehouse.

7. Execute a consulta novamente! Você deve notar uma diferença significativa. 

> [!NOTE]
> O Azure SQL Data Warehouse é uma plataforma MPP (Processamento Paralelo Massivo). Consultas e operações que podem tornar o trabalho paralelo entre vários nós aproveitam a verdadeira capacidade do Azure SQL Data Warehouse.
>

### <a name="join-query-with-statistics"></a>Consulta de junção com estatísticas

1. Executar uma consulta que une a tabela de Datas à tabela de Viagens

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    Como você pode esperar, a consulta demora muito mais quando você embaralha dados entre os nós, especialmente em um cenário de associação, como esta consulta.

2. Vamos ver como essa consulta difere ao criarmos estatísticas na coluna que estamos associando, executando o seguinte:

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > O SQL DW não gerencia automaticamente as estatísticas para você. As estatísticas são importantes para o desempenho da consulta, e é altamente recomendável criar e atualizar as estatísticas.
    > 
    > **Você obterá mais benefícios se tiver estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.**
    >

3. Execute novamente a consulta de Pré-requisitos e observe as diferenças de desempenho. Embora as diferenças no desempenho da consulta não sejam tão drásticas quanto o aumento, você deve observar uma aceleração perceptível. 

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para consultar e explorar. Confira nossas melhores práticas recomendadas ou dicas.

Se tiver terminado de explorar por hoje, pause a instância! Em produção, você pode obter uma enorme economia pausando e dimensionando para atender às suas necessidades de negócios.

![Pausar](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Leituras úteis

[Gerenciamento de simultaneidade e carga de trabalho]

[Práticas recomendadas para o Azure SQL Data Warehouse]

[Consultar monitoramento]

[Dez principais práticas recomendadas para a criação de um Data Warehouse relacional em grande escala]

[Migrando dados para o Azure SQL Data Warehouse]


[Gerenciamento de simultaneidade e carga de trabalho]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Práticas recomendadas para o Azure SQL Data Warehouse]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Consultar monitoramento]: sql-data-warehouse-manage-monitor.md
[Dez principais práticas recomendadas para a criação de um Data Warehouse relacional em grande escala]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[Migrando dados para o Azure SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Pré-requisitos]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO3-->


