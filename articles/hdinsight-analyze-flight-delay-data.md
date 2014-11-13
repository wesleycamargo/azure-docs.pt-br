<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analisar dados de atraso de voo usando o Hadoop no HDInsight | Azure" metaKeywords="" description="Saiba como carregar dados no HDInsight, como processar os dados usando o Hive e como exportar os resultados para o Banco de Dados SQL usando o Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analisar dados de atraso de voo usando o Hadoop no HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Analisar dados de atraso de voo usando o Hadoop no HDInsight

O Hive fornece um meio para executar trabalhos MapReduce do Hadoop por meio de uma linguagem de script semelhante à SQL, chamada *[HiveQL][HiveQL]*, que pode ser aplicada para resumo, consulta e análise de grandes volumes de dados. Este tutorial mostra como usar o Hive para calcular a média de atrasos em aeroportos e como usar o Sqoop para exportar os resultados para o Banco de Dados SQL do Azure.

Um dos maiores benefícios do HDInsight é a separação do armazenamento e computação de dados. O HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados. Um processo comum do HDInsight pode ser dividido em 3 partes:

1.  Armazenamento de dados no armazenamento de blob do Azure. Esse pode ser um processo contínuo. Por exemplo, os dados climáticos, dados do sensor, logs da Web e, nesse caso, os dados de atrasos do voo são salvos no armazenamento do blob.
2.  Execução de trabalhos. Quando for a hora de processar os dados, execute um script do PowerShell (ou um aplicativo cliente) para provisionar um cluster HDInsight, executar trabalhos e excluir o cluster. Os trabalhos salvam dados de saída no armazenamento de blob do Azure. Os dados de saída são retidos mesmo depois que o cluster é excluído. Dessa forma, você só paga pelo que consumiu.
3.  Recupere a saída do armazenamento do blob ou, nesse caso, exporte os dados para um banco de dados SQL do Azure.

O diagrama a seguir ilustra o cenário e a estrutura deste artigo:

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

Observação: Os números no diagrama correspondem aos títulos das seções.

**Tempo estimado para conclusão:** 30 minutos

## Neste tutorial

-   [Pré-requisitos][Pré-requisitos]
-   [Executar um trabalho em Hive em um cluster do HDInsight novo ou existente (M1)][Executar um trabalho em Hive em um cluster do HDInsight novo ou existente (M1)]
-   [Usar o Sqoop para exportar a saída para o Banco de Dados SQL do Azure (M2)][Usar o Sqoop para exportar a saída para o Banco de Dados SQL do Azure (M2)]
-   [Próximas etapas][Próximas etapas]
-   [Apêndice A: Carregar os dados de atraso de voo para o armazenamento de Blob do Azure (A1)][Apêndice A: Carregar os dados de atraso de voo para o armazenamento de Blob do Azure (A1)]
-   [Apêndice B: Criar e carregar o script HiveQL (A2)][Apêndice B: Criar e carregar o script HiveQL (A2)]

## <span id="prerequisite"></span></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

-   Uma estação de trabalho com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure].
-   Uma assinatura do Azure. \*\*\*

**Compreender o armazenamento do HDInsight**

Os clusters do Hadoop no HDInsight usam o armazenamento de Blob do Azure para armazenamento de dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do *HDFS* da Microsoft no armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Quando você provisiona um cluster HDInsight, um contêiner de armazenamento de Blob de uma conta de armazenamento do Azure é designado como o sistema de arquivos padrão, como no HDFS. Essa conta de armazenamento é referida como a *conta de armazenamento padrão*, e o contêiner do Blob é referido como o *contêiner do Blob padrão* ou *contêiner padrão*. A conta de armazenamento padrão deve estar localizada no mesmo datacenter que o cluster HDInsight. Excluir um cluster HDInsight não exclui o contêiner padrão ou a conta de armazenamento padrão.

Além da conta de armazenamento padrão, outras contas de armazenamento do Azure podem ser vinculadas a um cluster HDInsight durante o processo de provisionamento. A vinculação ocorre para adicionar a conta de armazenamento e a chave da conta de armazenamento ao arquivo de configuração. Isso para que o cluster possa acessar essas contas de armazenamento durante o tempo de execução. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters Hadoop no HDInsight][Provisionar clusters Hadoop no HDInsight].

A sintaxe do WASB é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] O caminho WASB é um caminho virtual. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Para arquivos armazenados no contêiner, ele pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (usando flightdelays.hql como um exemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Para acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

    tutorials/flightdelays/flightdelays.hql

Observe que não há uma "/" na frente do nome do blob.

**Arquivos usados neste tutorial**

A tabela a seguir lista os arquivos usados neste tutorial:

| Arquivos                                                                  | Descrição                                                                                                |
|---------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | O arquivo de script HiveQL necessário para o trabalho do Hive que será executado.                        |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | Os dados de entrada para os trabalhos do Hive.                                                           |
| \\tutorials\\flightdelays\\output                                         | O caminho de saída para o trabalho do Hive. O contêiner padrão é usado para armazenar os dados de saída. |
| \\tutorials\\flightdelays\\jobstatus                                      | A pasta de status do trabalho do Hive                                                                    |

Este tutorial usa o desempenho pontual de dados de voos de companhias aéreas do [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA). Os dados foram carregados para um contêiner de armazenamento de Blob do Azure com a permissão de acesso de Blob Público. Como esse é um contêiner de blob público, não é necessário vincular essa conta de armazenamento ao cluster HDInsight. O script do HiveQL também é carregado no mesmo contêiner do Blob. Se quiser saber como obter/carregar os dados para sua própria conta de armazenamento e como criar/carregar o arquivo de script do HiveQL, consulte o [apêndice][apêndice].

**Compreender a tabela interna e a tabela externa do Hive**

Existem algumas coisas que você precisa saber sobre a tabela interna e a tabela externa do Hive:

-   O comando CREATE TABLE cria uma tabela interna. O arquivo de dados deve estar localizado no contêiner padrão.
-   O comando CREATE TABLE move o arquivo de dados para a pasta /hive/warehouse/<tablename>.
-   O comando CREATE EXTERNAL TABLE cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.
-   O comando CREATE EXTERNAL TABLE não move o arquivo de dados.
-   O comando CREATE EXTERNAL TABLE não permite pastas em LOCATION. Essa é a razão pela qual o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: Hive Internal and External Tables Intro (HDInsight: Introdução às tabelas internas e externas do Hive)][HDInsight: Hive Internal and External Tables Intro (HDInsight: Introdução às tabelas internas e externas do Hive)].

> [WACOM.NOTE] Uma das instruções HiveQL cria uma tabela externa do Hive. A tabela externa do Hive mantém os arquivos de dados no local original. A tabela interna do Hive move os arquivos de dados para hive\\warehouse. A tabela interna do Hive requer que os arquivos de dados estejam localizados no contêiner padrão. Para dados armazenados fora do contêiner do Blob padrão, você deve usar as tabelas externas do Hive.

## <span id="runjob"></span></a>Executar o trabalho do Hive em um cluster do HDInsight novo ou existente

O Hadoop é um processamento em lote. A maneira mais econômica de executar um trabalho do Hive é provisionar um cluster para o trabalho e excluir o trabalho após ele ser concluído. O script a seguir aborda todo o processo. Para obter mais informações sobre como provisionar um cluster HDInsight e executar trabalhos do Hive, consulte [Provisionar clusters do Hadoop no HDInsight][Provisionar clusters Hadoop no HDInsight] e [Usar o Hive com o HDInsight][Usar o Hive com o HDInsight].

**Para executar as consultas do Hive usando o PowerShell**

1.  Abra o ISE do PowerShell.
2.  Copie e cole o seguinte script no painel de script:

        *** add script here

3.  Pressione **F5** para executar o script. A saída deverá ser similar a:

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

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
    <td>
    **Nome de variável**

    </td>
    <td>
    **Observação**

    </td>
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

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>Próximas etapas

Agora, você compreende como carregar arquivos para o armazenamento de Blob, como popular uma tabela Hive usando os dados do armazenamento de Blob, como executar consultas do Hive e como usar o Sqoop para exportar dados do HDFS para o Banco de Dados SQL do Azure. Para saber mais, consulte os seguintes artigos:

-   [Introdução ao HDInsight][Introdução ao HDInsight]
-   [Use o hive com o HDInsight][Usar o Hive com o HDInsight]
-   [Usar o Oozie com o HDInsight][Usar o Oozie com o HDInsight]
-   [Use o Sqoop com o HDInsight][Use o Sqoop com o HDInsight]
-   [Use o Pig com o HDInsight][Use o Pig com o HDInsight]
-   [Desenvolver programas Java MapReduce para HDInsight][Desenvolver programas Java MapReduce para HDInsight]
-   [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][Desenvolver programas de streaming do Hadoop em C# para o HDInsight]

## <span id="appendix-a"></span></a>Apêndice A - Carregar os dados de atraso de voo para o armazenamento de Blob do Azure

**Para baixar os dados de voos**

1.  Vá para [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA).
2.  Na página, selecione os valores a seguir:

    | Nome                | Valor                                                                                                                                                                                                                                                                                                                                                                                           |
    |---------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filtrar por ano     | 2013                                                                                                                                                                                                                                                                                                                                                                                            |
    | Filtrar por período | Janeiro                                                                                                                                                                                                                                                                                                                                                                                         |
    | Campos:             | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (limpar todos os outros campos) |

3.  Clique em **Download**.
4.  Descompacte o arquivo na pasta **C:\\Tutorials\\FlightDelays\\Data**. Cada arquivo é um arquivo CSV e tem aproximadamente 60 GB de tamanho.
5.  Renomeie o arquivo com o nome do mês ao qual seus dados se referem. Por exemplo, o arquivo que contém os dados de janeiro seria nomeado *January.csv*.
6.  Repita a etapa 2 e 5 para baixar um arquivo para cada um dos 12 meses em 2013. Você precisará de no mínimo um arquivo para executar o tutorial.

**Para carregar os dados de atraso de voo para o armazenamento de Blob do Azure**

1.  Abra o ISE do PowerShell.
2.  Cole o seguinte script no painel de script:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
        $destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
        #EndRegion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        if (-not (Get-AzureAccount)){ Add-AzureAccount}
        #EndRegion

        #Region - Validate user inpute
        # Validate the storage account
        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }

        # Validate the container
        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion

        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"

                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion

3.  Pressione **F5** para executar o script.

## <span id="appendix-b"></span></a>Apêndice B - Criar e carregar o script HiveQL

Usando o PowerShell do Azure, você pode executar várias instruções HiveQL uma por vez ou empacotar a instrução HiveQL em um arquivo de script. A seção mostra como criar um script HiveQL e carregá-lo no armazenamento de Blob do Azure usando o PowerShell. O Hive requer que os scripts HiveQL sejam armazenados no WASB.

O script HiveQL executará o seguinte:

1.  **Remova a tabela delays\_raw**, caso a tabela já exista.
2.  **Crie a tabela externa do Hive delays\_raw** apontando para o local do WASB com os arquivos de atraso de voo. Esta consulta especifica que os campos são delimitados por "," e que as linhas são finalizadas por "\\n". Isso representa um problema quando os valores dos campos *contêm* vírgulas porque o Hive não pode diferenciar entre uma vírgula que é um campo delimitado e uma que faz parte de um valor do campo (que é o caso nos valores de campo de ORIGIN\_CITY\_NAME e DEST\_CITY\_NAME). Para resolver isso, a consulta cria colunas TEMP para bloquear dados que estão divididos incorretamente nas colunas.
3.  **Remova a tabela de atrasos**, caso a tabela já exista;
4.  **Crie a tabela de atrasos**. É útil limpar os dados antes de continuar o processamento. Esta consulta cria uma nova tabela, *delays* a partir da tabela *delays\_raw*. Observe que as colunas TEMP (conforme mencionado anteriormente) não são copiadas e que a função *substring* é usada para remover marcas de aspas dos dados.
5.  **Calcula a média de atraso de tempo e agrupa os resultados por nome de cidade.** Ele também retornará os resultados para o WASB. Observe que a consulta removerá os apóstrofos dos dados e excluirá as linhas em que o valor de *weather\_deal*y for *null*, o que é necessário porque, por padrão, o Sqoop usado posteriormente neste tutorial normalmente não trata desses valores.

Para obter uma lista completa dos comandos HiveQL, consulte [Linguagem de Definição de Dados do Hive][HiveQL]. Cada comando HiveQL deve terminar com um ponto e vírgula.

**Para criar um arquivo de script HiveQL**

1.  Abra o ISE do PowerShell do Azure.
2.  Copie e cole o seguinte script no painel de script:

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green

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

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    A seguir estão as constantes usadas no script:

    -   **$hqlLocalFileName**: O script salva o arquivo do script HiveQL localmente antes de carregá-lo no WASB. Esse é o nome do arquivo. O valor padrão é <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>.
    -   **$hqlBlobName**: Esse é o nome do blob do arquivo do script HiveQL usado no armazenamento do Blob do Azure. O valor padrão é <u>tutorials/flightdelays/flightdelays.hql</u>. Como o arquivo será escrito diretamente no armazenamento do Blob do Azure, NÃO há "/" no início do nome do blob. Se você quiser acessar o arquivo por meio do WASB, precisará adicionar uma "/" no início do nome do arquivo.
    -   **$srcDataFolder** e **$dstDataFolder**: = "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  Execute o seguinte comando para conectar-se à sua assinatura do Azure:

        Add-AzureAccount

4.  Defina as primeiras duas variáveis e execute os comandos.

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    Estas são as variáveis e suas descrições:

    <table border="1">

    <tr>
    <td>
    **Nome de variável**

    </td>
    <td>
    **Descrição**

    </td>
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
5.  Execute os comandos a seguir para definir as instruções HiveQL:

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

6.  Execute os seguintes comandos para gravar o arquivo de script do Hive na estação de trabalho e carregá-lo no WASB:

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

## preparar banco de dados SQL

**Para preparar o Banco de Dados SQL (mescle o script do Sqoop a este)**

1.  Abra o ISE do PowerShell.
2.  Copie e cole o seguinte script no painel de script:

        [CmdletBinding()]
        Param(

            # SQL database server variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseUsername,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"

        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                    [origin_city_name] ASC
                )
                )"

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
            $sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress   
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
        }
        else
        {
            $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
            if (! $dbServer)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green

            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 

            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
        }
        else
        {
            $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
            if (! $db)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    > WACOM.NOTE O script usa um serviço REST, http://bot.whatismyipaddress.com, para recuperar o seu endereço de IP externo. O endereço IP é usado para criar uma regra de firewall para seu servidor do Banco de Dados SQL.

    A seguir estão algumas das constantes usadas no script:

    -   **$ipAddressRestService**: O valor padrão é <u>http://bot.whatismyipaddress.com</u>. Trata-se de um serviço REST de endereço IP público para obter seu endereço IP externo. Você pode usar outros serviços se desejar. O endereço IP externo recuperado usando o serviço será usado para criar uma regra de firewall para seu servidor do banco de dados SQL do Azure, para que você possa acessar o banco de dados por meio da estação de trabalho (usando o script do PowerShell).
    -   **$fireWallRuleName**: Esse é o nome da regra de firewall do servidor do banco de dados SQL do Azure. O nome padrão é <u>FlightDelay</u>. Você pode renomeá-lo se desejar.
    -   **$sqlDatabaseMaxSizeGB**: Esse valor é usado apenas ao criar um novo servidor do banco de dados SQL do Azure. O valor padrão é <u>10GB</u>. 10GB são suficientes para este tutorial.
    -   **$sqlDatabaseName**: Esse valor é usado apenas ao criar um novo banco de dados SQL do Azure. O valor padrão é <u>HDISqoop</u>. Se você renomeá-lo, deverá atualizar o script do PowerShell do Sqoop de acordo.

3.  Pressione **F5** para executar o script. Você precisa inserir suas credenciais de assinatura do Azure e os seguintes valores:

    -   **sqlDatabaseServer**: Insira o nome do servidor do banco de dados SQL do Azure no local onde quiser exportar a saída do Hive. Não insira nada para criar um.
    -   **sqlDatabaseUsername**: Insira o logon do banco de dados. Você deve especificá-lo se quiser criar um novo servidor do banco de dados ou usar um existente.
    -   **sqlDatabasePassword**: Insira a senha do logon do banco de dados. Você deve especificá-lo se quiser criar um novo servidor do banco de dados ou usar um existente.
    -   **sqlDatabaseLocation**: Insira a região. Ela é usada somente se você quiser criar um novo servidor do banco de dados.
    -   **sqlDatabaseName**: Insira o nome do banco de dados SQL do Azure. Não insira nada para criar um novo. O nome do banco de dados padrão é **HDISqoop**.

4.  Valide a saída do script. Tenha certeza de que o script foi executado com sucesso.

Se você optar por usar um método diferente para carregar os arquivos, verifique se o caminho do arquivo é *tutorials/flightdelays/data*. A sintaxe para acessar os arquivos é:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* é a pasta virtual que você criou quando carregou os arquivos. Verifique se há 12 arquivos, um para cada mês.

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [Pré-requisitos]: #prerequisite
  [Executar um trabalho em Hive em um cluster do HDInsight novo ou existente (M1)]: #runjob
  [Usar o Sqoop para exportar a saída para o Banco de Dados SQL do Azure (M2)]: #exportdata
  [Próximas etapas]: #nextsteps
  [Apêndice A: Carregar os dados de atraso de voo para o armazenamento de Blob do Azure (A1)]: #appdendix-a
  [Apêndice B: Criar e carregar o script HiveQL (A2)]: #appendix-b
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Provisionar clusters Hadoop no HDInsight]: ../hdinsight-provision-clusters/
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [apêndice]: #appendix
  [HDInsight: Hive Internal and External Tables Intro (HDInsight: Introdução às tabelas internas e externas do Hive)]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Usar o Hive com o HDInsight]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Usar o Oozie com o HDInsight]: ../hdinsight-use-oozie/
  [Use o Sqoop com o HDInsight]: ../hdinsight-use-sqoop/
  [Use o Pig com o HDInsight]: ../hdinsight-use-pig/
  [Desenvolver programas Java MapReduce para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Desenvolver programas de streaming do Hadoop em C# para o HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
