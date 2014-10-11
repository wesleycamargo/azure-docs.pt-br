<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analyze flight delay data using Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Analisar dados de atraso de voo usando o Hadoop no HDInsight

O Hive fornece um meio para executar trabalhos MapReduce do Hadoop por uma linguagem de script semelhante ao SQL, chamada *[HiveQL][]*, que pode ser aplicada para resumo, consulta e análise de grandes volumes de dados. Este tutorial mostra como usar o Hive para calcular a média de atrasos em aeroportos e como usar o Sqoop para exportar os resultados para o Banco de Dados SQL.

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:

-   Um cluster Azure HDInsight. Para obter informações sobre como provisionar um cluster HDInsight, consulte [Introdução ao HDInsight][] ou [Provisionar clusters HDInsight][].
-   Uma estação de trabalho com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][].

**Tempo estimado para conclusão:** 30 minutos

## Neste tutorial

-   [Preparar-se para o tutorial][]
-   [Criar e carregar o script HiveQL][]
-   [Executar o script HiveQL][]
-   [Exportar a saída para o Banco de Dados SQL do Azure][]
-   [Próximas etapas][]

## <span id="prepare"></span></a>Preparar-se para o tutorial

Este tutorial usa o desempenho pontual de dados de voos de companhias aéreas do [Research and Innovative Technology Administration, Bureau of Transportation Statistics][] (RITA) para sua estação de trabalho. Você irá executar o seguinte:

1.  baixe os dados de desempenho pontuais do RITA para sua estação de trabalho usando um navegador da Web
2.  carregue os dados para o HDInsight usando o PowerShell do Azure
3.  prepare o Banco de Dados SQL para exportação de dados usando o PowerShell do Azure

**Compreender o armazenamento do HDInsight**

O HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][].

Quando você provisiona um cluster HDInsight, um contêiner de armazenamento de Blob é designado como o sistema de arquivos padrão, como no HDFS. Além desse contêiner, você pode adicionar mais contêineres da mesma conta de armazenamento do Azure ou de diferentes contas de armazenamento do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][].

Para simplificar o script do PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutorials/flightdelays*. Por padrão, esse contêiner tem o mesmo nome que o nome do cluster HDInsight.

A sintaxe do WASB é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Apenas a sintaxe *wasb://* tem suporte no cluster HDInsight versão 3.0. A sintaxe antiga *asv://* tem suporte nos clusters HDInsight 2.1 e 1.6, mas não tem suporte nos clusters HDInsight 3.0 e não terá suporte em versões posteriores.

> O caminho WASB é um caminho virtual. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][].

Para um arquivo armazenado no contêiner do sistema de arquivos padrão, ele pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (use o flightdelays.hql como um exemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

    tutorials/flightdelays/flightdelays.hql

A tabela a seguir lista os arquivos usados neste tutorial:

<table border="1">
<tr><th>Arquivos  </th><th>	  Descrição </th></tr>
<tr><td> \tutotirals\flightdelays\data            </td><td>	 dados de voos de entrada para o Hive </td></tr>
<tr><td> \tutorials\flightdelays\output           </td><td>	 saída para o Hive                    </td></tr>
<tr><td> \tutorials\flightdelays\flightdelays.hql </td><td>	 Arquivo de script HiveQL             </td></tr>
<tr><td> \tutorials\flightdelays\jobstatus        </td><td>	 Status do trabalho do Hadoop         </td></tr>
</table>

**Compreender a tabela interna e a tabela externa do Hive**

Existem algumas coisas que você precisa saber sobre a tabela interna e a tabela externa do Hive:

-   O comando CREATE TABLE cria uma tabela interna. O arquivo de dados deve estar localizado no contêiner padrão.
-   O comando CREATE TABLE move o arquivo de dados para a pasta /hive/warehouse/<tablename>.
-   O comando CREATE EXTERNAL TABLE cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.
-   O comando CREATE EXTERNAL TABLE não move o arquivo de dados.
-   O comando CREATE EXTERNAL TABLE não permite pastas em LOCATION. Essa é a razão pela qual o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: Introdução às tabelas internas e externas do Hive][cindygross-hive-tables].

> [WACOM.NOTE] Uma das instruções HiveQL cria uma tabela externa do Hive. A tabela externa do Hive mantém os arquivos de dados no local original. A tabela interna do Hive move os arquivos de dados para hive\\warehouse. A tabela externa requer que os arquivos de dados sejam localizados no contêiner WASB de sistema de arquivos padrão. Se você escolheu armazenar os arquivos de dados de voos em um contêiner que não seja o contêiner de Blob padrão, deverá usar as tabelas internas do Hive.

**Para baixar os dados de voos**

1.  Vá para [Research and Innovative Technology Administration, Bureau of Transportation Statistics][] (RITA).
2.  Na página, selecione os valores a seguir:

	<table border="1">
    <tr><th> Nome               </th><th> Valor </th><th>
    <tr><td> Filtrar por ano     </td><td>	2012 </td></tr>
    <tr><td> Filtrar por período </td><td>	Janeiro </td></tr>
    <tr><td> Campos:             </td><td>	*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (limpar todos os outros campos) </td></tr>
	</table>

3.  Clique em **Download**. Cada arquivo pode levar até 15 minutos para baixar.
4.  Descompacte o arquivo na pasta **C:\\Tutorials\\FlightDelays\\Data**. Cada arquivo é um arquivo CSV e tem aproximadamente 60 GB de tamanho.
5.  Renomeie o arquivo com o nome do mês ao qual seus dados se referem. Por exemplo, o arquivo que contém os dados de janeiro seria nomeado *January.csv*.
6.  Repita a etapa 2 e 5 para baixar um arquivo para cada um dos 12 meses em 2012. Você precisará de no mínimo um arquivo para executar o tutorial.

**Para carregar os dados de atraso de voo para o armazenamento de Blob do Azure**

1.  Abra o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][].
2.  Execute o seguinte comando para conectar-se à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure.

3.  Defina as primeiras três variáveis e execute os comandos.

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $ContainerName = "<BlobStorageContainerName>"

        $localFolder = "C:\Tutorials\FlightDelays\Data"
        $destFolder = "tutorials/flightdelays/data"

        $month = "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    Estas são as variáveis e suas descrições:

    <table border="1">

    <tr>
    <th>
    Nome de variável
    </th>
    <th>
    Descrição

    </th>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Nome da sua assinatura do Azure.

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    A conta de Armazenamento do Azure usada para armazenar os arquivos de dados de voo. É recomendável usar a conta de armazenamento padrão.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    O contêiner de armazenamento de Blob do Azure usado para armazenar arquivos de dados de voo. É recomendável usar o contêiner de Blob do sistema de arquivos do cluster HDInsight padrão. Por padrão, o contêiner tem o mesmo nome do cluster HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $localFolder

    </td>
    <td>
    Essa é a pasta em sua estação de trabalho onde estão armazenados os arquivos de atraso de voo.

    </td>
    </tr>

    <tr>
    <td>
    $destFolder

    </td>
    <td>
    Esse é o caminho WASB onde os dados de atraso de voo serão carregados. O caminho do Hadoop (HDInsight) diferencia maiúsculas de minúsculas.

    </td>
    </tr>

    <tr>
    <td>
    $month

    </td>
    <td>
    Se você não baixou todos os 12 arquivos, precisará atualizar esta variável

    </td>
    </tr>

    </table>
    </p>
4.  Execute os seguintes comandos para carregar e listar os arquivos:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB        
        foreach ($item in $month){
            $fileName = "$localFolder\$item.csv"
            $blobName = "$destFolder/$item.csv"

            Write-Host "Copying $fileName to $blobName" -BackgroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder

Se você optar por usar um método diferente para carregar os arquivos, verifique se o caminho do arquivo é *tutorials/flightdelays/data*. A sintaxe para acessar os arquivos é:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* é a pasta virtual que você criou quando carregou os arquivos. Verifique se há 12 arquivos, um para cada mês.

**Para preparar o Banco de Dados SQL**

1.  Abra o PowerShell do Azure.
2.  Execute o seguinte comando para conectar-se à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure.

3.  Defina as primeiras seis variáveis no script a seguir e execute os comandos:

        # Azure subscription name
        $subscriptionName = "<AzureSubscriptionName>"

        # SQL database server variables
        $sqlDatabaseServer = ""  # specify the Azure SQL database server name if you have one created. Otherwise use "".
        $sqlDatabaseUsername = "<SQLDatabaseUserName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseLocation = "<MicrosoftDataCenter>"   #For example, West US.

        # SQL database variables
        $sqlDatabaseName = ""  # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        $sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        $sqlDatabaseTableName = "AvgDelays"
        $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
            (
                [origin_city_name] ASC
            )
            )"

    Estas são as variáveis e suas descrições:

    <table border="1">

    <tr>
    <th>
    Nome de variável

    </th>
    <th>
    Descrição
    </th>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Nome da sua assinatura do Azure.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    O nome do servidor de Banco de Dados SQL usado pelo Sqoop para o qual exportar dados. Se você deixá-lo como está, o script criará um para você. Caso contrário, especifique um Banco de Dados SQL ou SQL Server existente.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Nome de usuário do Banco de Dados SQL/SQL Server.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Senha de usuário do Banco de Dados SQL/SQL Server.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseLocation

    </td>
    <td>
    Somente será usado se quiser que o script crie um servidor de Banco de Dados SQL para você.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    O nome do Banco de Dados SQL usado pelo Sqoop para o qual exportar dados. Se você deixá-lo como está, o script criará um para você. Caso contrário, especifique um Banco de Dados SQL ou SQL Server existente.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseMaxSizeGB

    </td>
    <td>
    Somente será usado se quiser que o script crie um Banco de Dados SQL para você.

    </td>
    </tr>

    </table>
    </p>
4.  Execute os seguintes comandos para criar o servidor/banco de dados/tabela do Banco de Dados SQL.

        # Select the current Azure subscription in case there are multiple subscriptions
        Select-AzureSubscription $subscriptionName

        # create a new Azure SQL Database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
            Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
        }

        # Create a new SQL database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB

            Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
        }

        #Create AvgDelays table
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

## <span id="createscript"></span></a>Criar e carregar o script HiveQL

Usando o PowerShell do Azure, você pode executar várias instruções HiveQL uma por vez ou empacotar a instrução HiveQL em um arquivo de script. Neste tutorial, você irá criar um script HiveQL. O arquivo de script deve ser carregado no WASB. Na próxima seção, você irá executar o arquivo de script usando o PowerShell do Azure.

O script HiveQL executará o seguinte:

1.  **Remova a tabela delays\_raw**, caso a tabela já exista.
2.  **Crie a tabela externa do Hive delays\_raw** apontando para o local do WASB com os arquivos de atraso de voo. Esta consulta especifica que os campos são delimitados por "," e que as linhas são finalizadas por "\\n". Isso representa um problema quando os valores dos campos *contêm* vírgulas porque o Hive não pode diferenciar entre uma vírgula que é um campo delimitado e uma que faz parte de um valor do campo (que é o caso nos valores de campo de ORIGIN\_CITY\_NAME e DEST\_CITY\_NAME). Para resolver isso, a consulta cria colunas TEMP para bloquear dados que estão divididos incorretamente nas colunas.
3.  **Remova a tabela de atrasos**, caso a tabela já exista;
4.  **Crie a tabela de atrasos**. É útil limpar os dados antes de continuar o processamento. Esta consulta cria uma nova tabela, *delays* a partir da tabela *delays\_raw*. Observe que as colunas TEMP (conforme mencionado anteriormente) não são copiadas e que a função *substring* é usada para remover marcas de aspas dos dados.
5.  **Calcula a média de atraso de tempo e agrupa os resultados por nome de cidade.** Ele também retornará os resultados para o WASB. Observe que a consulta removerá os apóstrofos dos dados e excluirá as linhas em que o valor de *weather\_deal*y for *null*, o que é necessário porque, por padrão, o Sqoop usado posteriormente neste tutorial normalmente não trata desses valores.

Para obter uma lista completa dos comandos HiveQL, consulte [Linguagem de Definição de Dados do Hive][HiveQL]. Cada comando HiveQL deve terminar com um ponto e vírgula.

**Para criar um arquivo de script HiveQL**

1.  Abra o PowerShell do Azure.
2.  Execute o seguinte comando para conectar-se à sua assinatura do Azure:

        Add-AzureAccount

3.  Defina as primeiras duas variáveis e execute os comandos.

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    Estas são as variáveis e suas descrições:

    <table border="1">

    <tr>
    <th>
    Nome de variável

    </th>
    <th>
    Descrição

    </th>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    A conta de Armazenamento do Azure usada para armazenar os arquivos de script HiveQL. Os scripts do PowerShell fornecidos neste tutorial exigem os arquivos de dados de voo e o arquivo de script localizados na mesma conta de Armazenamento do Azure e no contêiner de armazenamento de Blob.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    O contêiner de armazenamento de Blob usado para armazenar o arquivo de script HiveQL. Os scripts do PowerShell fornecidos neste tutorial exigem os arquivos de dados de voo e o arquivo de script localizados na mesma conta de Armazenamento do Azure e no contêiner de armazenamento de Blob.

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    Os nomes dos arquivos locais para o script HiveQL antes de serem carregados no WASB. Para simplificar o script do PowerShell, você gravará o arquivo localmente e usará o cmdlet Set-AzureStorageBlobContent para carregar o arquivo de script no HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    Esse é o nome do arquivo de script com o caminho no WASB

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    Essa é a pasta no WASB onde o script HiveQL efetua pull dos dados.

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    Essa é a pasta no WASB para a qual o script HiveQL envia a saída. Mais adiante no tutorial, você usará o Sqoop para exportar os dados dessa pasta para o Banco de Dados SQL do Azure.

    </td>
    </tr>

    </table>
    </p>
4.  Execute os comandos a seguir para definir as instruções HiveQL:

        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

        $hqlDropDelays = "DROP TABLE delays;"

        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;" 

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

5.  Execute os seguintes comandos para gravar o arquivo de script do Hive na estação de trabalho e carregá-lo no WASB:

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    A saída deverá ser similar a:

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

## <span id="executehqlscript"></span></a>Executar o script HiveQL

Existem vários cmdlets do PowerShell do Azure, que você pode usar para executar o Hive. Este tutorial usa o Invoke-Hive. Para obter outros métodos, consulte [Usar o Hive com o HDInsight][]. Usando o Invoke-Hive, você pode executar uma instrução HiveQL ou um script HiveQL. Você irá usar o script HiveQL, que você criou e carregou no armazenamento de Blob do Azure.

Há um erro conhecido do caminho do Hive. As instruções para corrigir o problema podem ser encontradas no [TechNet Wiki][].

**Para executar as consultas do Hive usando o PowerShell**

1.  Abra o PowerShell do Azure.
2.  Execute o comando a seguir para alterar o diretório atual:

    cd \\Tutorials\\FlightDelays\\

    Essa etapa é necessária porque você irá baixar uma cópia da saída do Hive em sua estação de trabalho. Por padrão, você não tem permissão de gravação na pasta do PowerShell.

3.  Execute o seguinte comando para conectar-se à sua assinatura do Azure:

        Add-AzureAccount

4.  Defina as primeiras três variáveis e execute-as:

        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureBlobStorageContainerName>"
        $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
        $outputBlobName = "tutorials/flightdelays/output/000000_0"

    Estas são as variáveis e suas descrições:

    <table border="1">

    <tr>
    <th>
    Nome de variável

    </th>
    <th>
    Descrição

    </th>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    O cluster HDInsight que executará o script do Hive e a exportação do Sqoop.

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    A conta de Armazenamento do Azure usada para armazenar o script HiveQL. Consulte <a href = "#createScript">Criar e carregar o script HiveQL</a>.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    O contêiner de armazenamento de Blob usado para armazenar o script HiveQL. Consulte <a href = "#createScript">Criar e carregar o script HiveQL</a>.

    </td>
    </tr>

    <tr>
    <td>
    $hqlScriptFile

    </td>
    <td>
    Esse é o URI do arquivo de script HiveQL.

    </td>
    </tr>

    <tr>
    <td>
    $outputBlobName

    </td>
    <td>
    Esse é o arquivo de saída do script HiveQL. O nome padrão é *000000\_0*.

    </td>
    </tr>

    </table>
    </p>
5.  Execute o comando a seguir para invocar o hive

        Use-AzureHDInsightCluster $clusterName

        Invoke-Hive –File $hqlScriptFile

6.  Execute o comando a seguir para verificar os resultados:

        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 

        cat ".\$outputBlobName" | findstr /B "Ch"

    A saída deverá ser similar a:

        Champaign/Urbana 19.023255813953487
        Charleston 24.93975903614458
        Charleston/Dunbar 16.954545454545453
        Charlotte 16.88616981831665
        Charlotte Amalie 12.88888888888889
        Charlottesville 25.444444444444443
        Chattanooga 19.883561643835616
        Cheyenne 9.875
        Chicago 16.77321649680922
        Chico 12.340909090909092
        Christiansted 12.318181818181818

    Entre cada cidade e tempo de atraso, há um delimitador que não está visível na janela de saída do PowerShell. É "\\001". Você usará esse delimitador ao executar a exportação do Sqoop.

## <span id="exportdata"></span></a>Exportar a saída de trabalho do Hive para o Banco de Dados SQL do Azure

A última etapa é executar a exportação do Sqoop para exportar os dados para o Banco de Dados SQL. Você criou o Banco de Dados SQL e a tabela AvgDelays anteriormente no tutorial.

**Para exportar os dados para o Banco de Dados SQL**

1.  Abra o PowerShell do Azure.
2.  Execute o seguinte comando para conectar-se à sua assinatura do Azure:

        Add-AzureAccount

3.  Defina as primeiras cinco variáveis e execute os comandos:

        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    Estas são as variáveis e suas descrições:

    <table border="1">

    <tr>
    <th>
    Nome de variável

    </th>
    <th>
    Observação

    </th>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Nome do cluster HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    O servidor de Banco de Dados SQL para o qual o Sqoop exportará dados.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Nome de usuário do Banco de Dados SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Senha de usuário do Banco de Dados SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    O Banco de Dados SQL para o qual o Sqoop exportará dados. O nome padrão é \*HDISqoop".

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    O Banco de Dados SQL para o qual o Sqoop exportará dados. O nome do arquivo padrão é AvgDelays. Esta é a tabela que você criou anteriormente no tutorial.

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    Esse é o local do arquivo de saída do Hive. O Sqoop exportará os arquivos desse local para o Banco de Dados SQL.

    </td>
    </tr>

    </table>
    </p>
4.  Execute o comando a seguir para exportar os dados:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Conecte-se ao Banco de Dados SQL e consulte a média dos atrasos de voo por cidade na tabela *AvgDelays*:

    ![HDI.FlightDelays.AvgDelays.Dataset][]

## <span id="nextsteps"></span></a>Próximas etapas

Agora, você compreende como carregar arquivos para o armazenamento de Blob, como popular uma tabela Hive usando os dados do armazenamento de Blob, como executar consultas do Hive e como usar o Sqoop para exportar dados do HDFS para o Banco de Dados SQL do Azure. Para saber mais, consulte os seguintes artigos:

-   [Introdução ao HDInsight][]
-   [Use o hive com o HDInsight][Usar o Hive com o HDInsight]
-   [Usar o Oozie com o HDInsight][]
-   [Use o Sqoop com o HDInsight][]
-   [Use o Pig com o HDInsight][]
-   [Desenvolver programas Java MapReduce para HDInsight][]
-   [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][]

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Preparar-se para o tutorial]: #prepare
  [Criar e carregar o script HiveQL]: #createscript
  [Executar o script HiveQL]: #executehqlscript
  [Exportar a saída para o Banco de Dados SQL do Azure]: #exportdata
  [Próximas etapas]: #nextsteps
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Usar o Hive com o HDInsight]: ../hdinsight-use-hive/
  [TechNet Wiki]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [1]: #createScript
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Usar o Oozie com o HDInsight]: ../hdinsight-use-oozie/
  [Use o Sqoop com o HDInsight]: ../hdinsight-use-sqoop/
  [Use o Pig com o HDInsight]: ../hdinsight-use-pig/
  [Desenvolver programas Java MapReduce para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Desenvolver programas de streaming do Hadoop em C# para o HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
