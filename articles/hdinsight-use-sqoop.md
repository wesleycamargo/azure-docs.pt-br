<properties urlDisplayName="Use Hadoo Sqoop in HDInsight" pageTitle="Usar o Sqoop do Hadoop no HDInsight | Azure" metaKeywords="" description="Saiba como usar o PowerShell do Azure em uma esta&ccedil;&atilde;o de trabalho para executar importa&ccedil;&atilde;o e exporta&ccedil;&atilde;o do Sqoop entre um cluster do Hadoop e um Banco de Dados SQL do Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Usar o Sqoop do Hadoop no HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Use Sqoop com Hadoop no HDInsight

Saiba como usar o PowerShell do Azure e o SDK do .NET do HDInsight em uma estação de trabalho para executar importação e exportação do Sqoop entre um cluster HDInsight e um banco de dados SQL do Azure ou banco de dados do SQL Server.

**Tempo estimado para conclusão:** 30 minutos

## Neste artigo

-   [O que é o Sqoop?][O que é o Sqoop?]
-   [Pré-requisitos][Pré-requisitos]
-   [Compreender o cenário do tutorial][Compreender o cenário do tutorial]
-   [Preparar-se para o tutorial][Preparar-se para o tutorial]
-   [Usar o PowerShell para executar a exportação do Sqoop][Usar o PowerShell para executar a exportação do Sqoop]
-   [Usar o SDK do HDInsight para executar a exportação do Sqoop][Usar o SDK do HDInsight para executar a exportação do Sqoop]
-   [Usar o PowerShell para executar a importação do Sqoop][Usar o PowerShell para executar a importação do Sqoop]
-   [Próximas etapas][Próximas etapas]

## <span id="whatissqoop"></span></a> O que é o Sqoop?

Embora o Hadoop seja uma opção natural para o processamento de dados semiestruturadospast e não estruturados, como logs e arquivos, também pode ser necessário processar dados estruturados armazenados em bancos de dados relacionais.

O [Sqoop][Sqoop] é uma ferramenta desenvolvida para transferir dados entre clusters Hadoop e bancos de dados relacionais. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL ou MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com MapReduce ou Hive e, em seguida, exportar os dados de volta para um RDBMS. Neste tutorial, você está usando um Banco de Dados SQL como seu banco de dados relacional.

Para obter as versões do Sqoop com suporte em clusters HDInsight, consulte [O que há de novo nas versões de clusters fornecidas pelo HDInsight?][O que há de novo nas versões de clusters fornecidas pelo HDInsight?]

## <span id="prerequisites"></span></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

-   **Estação de Trabalho** - Um computador com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure]. Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Executar scripts do Windows PowerShell][Executar scripts do Windows PowerShell].

-   **Cluster do Azure HDInsight**. Para obter informações sobre como provisionar um cluster, consulte [Introdução ao HDInsight][Introdução ao HDInsight] ou [Provisionar clusters HDInsight][Provisionar clusters HDInsight]. Você precisará dos seguintes dados para percorrer o tutorial:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propriedade do cluster</th>
    <th align="left">Nome de variável do PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descrição</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome do cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Este é o nome do cluster HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome da conta de armazenamento do Azure</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">Uma conta de armazenamento do Azure disponível para o cluster HDInsight. Para este tutorial, use a conta de armazenamento padrão especificada durante o processo de provisionamento do cluster.</td>
    </tr>
    <tr class="odd">
    <td align="left">Nome do contêiner de blob do Azure</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">Para este exemplo, utilize o contêiner de armazenamento de blob do Azure usado para o sistema de arquivos do cluster HDInsight padrão. Por padrão, o contêiner tem o mesmo nome do cluster HDInsight.</td>
    </tr>
    </tbody>
    </table>

-   **Banco de dados SQL do Azure**. Você deve configurar uma regra de firewall para o servidor de Banco de Dados SQL para permitir o acesso a partir de sua estação de trabalho. Para obter instruções sobre como criar um Banco de Dados SQL e configurar o firewall, consulte [Introdução ao uso do Banco de Dados SQL do Azure][Introdução ao uso do Banco de Dados SQL do Azure]. Este artigo fornece um script do PowerShell para criar a tabela do Banco de Dados SQL necessária para este tutorial.

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propriedade de banco de dados SQL</th>
    <th align="left">Nome de variável do PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descrição</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome do servidor de banco de dados SQL</td>
    <td align="left">$sqlDatabaseServer</td>
    <td align="left"></td>
    <td align="left">O servidor do banco de dados SQL para o qual o Sqoop exportará os dados ou a partir do qual ele importará os dados.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome de logon do banco de dados SQL</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">Nome de logon do banco de dados SQL.</td>
    </tr>
    <tr class="odd">
    <td align="left">Senha de logon do banco de dados SQL</td>
    <td align="left">$sqlDatabasePassword</td>
    <td align="left"></td>
    <td align="left">Senha de logon do banco de dados SQL.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome do banco de dados SQL</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">O banco de dados SQL do Azure para o qual o Sqoop exportará os dados ou a partir do qual ele importará os dados.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] Por padrão, um banco de dados SQL do Azure permite conexões de serviços do Azure, como o Azure HDInsight. Se essa configuração de firewall estiver desabilitada, você deverá habilitá-la no Portal de Gerenciamento do Azure. Para obter instruções sobre como criar um Banco de Dados SQL e configurar regras de firewall, consulte [Criar e configurar o Banco de Dados SQL][Criar e configurar o Banco de Dados SQL].

-   **SQL Server**. Se o cluster do HDInsight estiver na mesma rede virtual do Azure que um SQL Server, é possível usar as etapas neste artigo para importar e exportar dados para um banco de dados SQL Server. Para obter mais informações, consulte os seguintes artigos.

    > [WACOM.NOTE] \> O Azure HDInsight oferece suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupos de afinidade.

    -   Para **criar e configurar uma rede virtual**, consulte [Tarefas de configuração de rede virtual][Tarefas de configuração de rede virtual].

        -   Ao usar o SQL Server **no datacenter**, é preciso configurar a rede virtual como *site a site* ou *ponto a site*.

            > [WACOM.NOTE] Para redes virtuais **ponto a site**, o SQL Server deve estar executando o aplicativo de configuração de cliente VPN, que está disponível no **Painel** da sua configuração da Rede virtual do Azure.

        -   Ao usar o SQL Server em uma **Máquina Virtual do Azure**, qualquer configuração de rede virtual pode ser usada, desde que a máquina virtual que hospeda o SQL Server seja membro da mesma rede virtual que o HDInsight.

    -   Para **provisionar um cluster HDInsight em uma rede virtual**, consulte [Provisionar clusters Hadoop no HDInsight usando opções de personalização][Provisionar clusters Hadoop no HDInsight usando opções de personalização]

    > [WACOM.NOTE] O SQL Server também deve permitir Autenticação do SQL. Você deve usar o logon do SQL para as etapas neste artigo.

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propriedade de banco de dados SQL</th>
    <th align="left">Nome de variável do PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descrição</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome do SQL Server</td>
    <td align="left">$sqlDatabaseServer</td>
    <td align="left"></td>
    <td align="left">O SQL Server para o qual o Sqoop exportará os dados ou por meio do qual ele importará os dados.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome de logon do SQL Server</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">Um nome de logon do SQL.</td>
    </tr>
    <tr class="odd">
    <td align="left">Senha de logon do SQL</td>
    <td align="left">$sqlDatabasePassword</td>
    <td align="left"></td>
    <td align="left">A senha de logon do SQL.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome do banco de dados SQL Server</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">O banco de dados ao qual o Sqoop exportará os dados ou por meio do qual ele importará os dados.</td>
    </tr>
    </tbody>
    </table>

> [WACOM.NOTE] Preencha os valores nas tabelas acima. Isso poderá ser útil para percorrer este tutorial.

## <span id="scenario"></span></a>Compreender o cenário

O cluster HDInsight é fornecido com alguns dados de exemplo. Você irá usar os dois seguintes:

-   Um arquivo de log log4j localizado em */example/data/sample.log*. Os seguintes logs são extraídos do arquivo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

-   Uma tabela Hive chamada *hivesampletable* que faz referência ao arquivo de dados localizado em */hive/warehouse/hivesampletable*. A tabela contém alguns dados de dispositivo móvel. O esquema da tabela Hive é:

    | Campo                | Tipo de dados |
    |----------------------|---------------|
    | clientid             | string        |
    | querytime            | string        |
    | market               | string        |
    | deviceplatform       | string        |
    | devicemake           | string        |
    | devicemodel          | string        |
    | state                | string        |
    | country              | string        |
    | querydwelltime       | double        |
    | sessionid            | bigint        |
    | sessionpagevieworder | bigint        |

Primeiro, você exportará o sample.log e a hivesampletable para o banco de dados SQL ou SQL Server e, em seguida, importará a tabela que contém os dados de dispositivos móveis de volta para o HDInsight usando o seguinte caminho:

    /tutorials/usesqoop/importeddata

### Compreender o armazenamento do HDInsight

O HDInsight usa o Armazenamento de Blob do Azure para armazenamento de dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Ao provisionar um cluster HDInsight, uma conta de Armazenamento do Azure e um contêiner de Armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão, exatamente como no HDFS. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][Provisionar clusters HDInsight]. Para simplificar o script do PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutoriais/usesqoop*. Por padrão, esse contêiner tem o mesmo nome do cluster HDInsight.
A sintaxe WASB é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Apenas a sintaxe *wasb://* tem suporte no cluster HDInsight versão 3.0. A sintaxe antiga *asv://* tem suporte nos clusters HDInsight 2.1 e 1.6, mas não tem suporte nos clusters HDInsight 3.0 e não terá suporte em versões posteriores.

> [WACOM.NOTE] O caminho WASB é um caminho virtual. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Um arquivo armazenado no contêiner do sistema de arquivos padrão pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (use o sample.log como um exemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

    example/data/sample.log

## <span id="prepare"></span></a>Preparar-se para o tutorial

Você criará duas tabelas no banco de dados SQL ou no SQL Server. Elas são usadas pela exportação do Sqoop mais adiante neste tutorial. Você também precisará pré-processar os arquivos sample.log para que possam ser processados pelo Sqoop.

### Criar tabelas SQL

**Para o Banco de Dados SQL Azure**

1.  Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell\_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][Iniciar o Windows PowerShell no Windows 8 e no Windows]).

2.  Copie o seguinte script no painel de scripts e, em seguida, defina as primeiras quatro variáveis:

        #SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>" 
        $sqlDatabaseLogin = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseName = "<SQLDatabaseName>" 

        $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos][Pré-requisitos] deste tutorial.

3.  Acrescente o seguinte script no painel de scripts. Estas são as instruções SQL que definem as duas tabelas e seus índices de cluster. O Banco de Dados SQL requer um índice clusterizado.

        # SQL query strings for creating tables and clustered indexes
        $cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
            [t1] [nvarchar](50), 
            [t2] [nvarchar](50), 
            [t3] [nvarchar](50), 
            [t4] [nvarchar](50), 
            [t5] [nvarchar](50), 
            [t6] [nvarchar](50), 
            [t7] [nvarchar](50))"

        $cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

        $cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50), 
        [querytime] [nvarchar](50), 
        [market] [nvarchar](50), 
        [deviceplatform] [nvarchar](50), 
        [devicemake] [nvarchar](50), 
        [devicemodel] [nvarchar](50), 
        [state] [nvarchar](50), 
        [country] [nvarchar](50), 
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder][bigint])"

        $cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

4.  Acrescente o seguinte script no painel de script para executar os comandos SQL:

        Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = $sqlDatabaseConnectionString
        $conn.Open()

        Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.Connection = $conn
        $cmd.CommandText = $cmdCreateLog4jTable
        $ret = $cmd.ExecuteNonQuery()
        $cmd.CommandText = $cmdCreateLog4jClusteredIndex
        $cmd.ExecuteNonQuery()

        Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
        $cmd.CommandText = $cmdCreateMobileTable
        $cmd.ExecuteNonQuery()
        $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
        $cmd.ExecuteNonQuery()

        Write-Host "Close connection ..." -ForegroundColor Green        
        $conn.close()

        Write-Host "Done" -ForegroundColor Green

5.  Clique em **Executar Script** ou pressione **F5** para executar o script.
6.  Use o [Portal de gerenciamento do Azure][Portal de gerenciamento do Azure] para examinar as tabelas e os índices clusterizados.

**Para SQL Server**

1.  Abra o **SQL Server Management Studio** e conecte-se ao SQL Server.

2.  Crie um novo banco de dados chamado **sqoopdb**.

3.  Selecione o banco de dados **sqoopdb** e, então, selecione **Nova consulta** na faixa de opções na parte superior do SQL Server Management Studio.

4.  Insira o seguinte na janela de consulta.

        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50), 
         [t2] [nvarchar](50), 
         [t3] [nvarchar](50), 
         [t4] [nvarchar](50), 
         [t5] [nvarchar](50), 
         [t6] [nvarchar](50), 
         [t7] [nvarchar](50))

        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50), 
         [querytime] [nvarchar](50), 
         [market] [nvarchar](50), 
         [deviceplatform] [nvarchar](50), 
         [devicemake] [nvarchar](50), 
         [devicemodel] [nvarchar](50), 
         [state] [nvarchar](50), 
         [country] [nvarchar](50), 
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])

5.  Use a tecla de função **F5** ou selecione **! Execute** na faixa de opções para executar a consulta. A seguinte mensagem deve aparecer abaixo da consulta.

        Command(s) completed successfully.

6.  Feche o SQL Server Management Studio.

### Gerar dados

Neste tutorial, você exportará um arquivo de log log4j (um arquivo delimitado) e uma tabela Hive para o banco de dados SQL. O arquivo delimitado é */example/data/sample.log*. No início do tutorial, você vê alguns exemplos de logs de log4j. No arquivo de log, há algumas linhas vazias e algumas outras linhas semelhantes a:

    java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
        at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Isso está correto para outros exemplos que usam esses dados, mas é preciso remover essas exceções antes de ser possível importar para o banco de dados SQL ou SQL Server. Haverá falha na exportação do Sqoop se houver uma cadeia de caracteres vazia ou uma linha com um número menor de elementos que o número de campos definidos na tabela do banco de dados SQL. A tabela log4jlogs contém sete campos de tipo de cadeia de caracteres.

**Para pré-processar o arquivo sample.log**

1.  Abra o ISE do Windows PowerShell.
2.  No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure. Esse método de adicionar uma conexão de assinatura expira e, depois de 12 horas, você precisará fazer logon novamente.

    > [WACOM.NOTE] Se houver várias assinaturas do Azure e a assinatura padrão não for a que você deseja usar, use o cmdlet **Select-AzureSubscription** para selecionar a assinatura atual.

3.  Copie o seguinte script no painel de scripts e, em seguida, defina as primeiras duas variáveis:

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $sourceBlobName = "example/data/sample.log"
        $destBlobName = "tutorials/usesqoop/data/sample.log"

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos][Pré-requisitos] deste tutorial.

4.  Acrescente o seguinte script no painel de scripts:

        # Define the connection string
        $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

        # Create block blob objects referencing the source and destination blob.
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

        # Define a MemoryStream and a StreamReader for reading from the source file
        $stream = New-Object System.IO.MemoryStream
        $stream = $sourceBlob.OpenRead()
        $sReader = New-Object System.IO.StreamReader($stream)

        # Define a MemoryStream and a StreamWriter for writing into the destination file
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        # process the source blob
        $exString = "java.lang.Exception:"
        while(-Not $sReader.EndOfStream){
            $line = $sReader.ReadLine()
            $split = $line.Split(" ")

            # remove the "java.lang.Exception" from the first element of the array
            # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
            if ($split[0] -eq $exString){
                #create a new ArrayList to remove $split[0]
                $newArray = [System.Collections.ArrayList] $split
                $newArray.Remove($exString)

                # update $split and $line
                $split = $newArray
                $line = $newArray -join(" ")
            }

            # remove the lines that has less than 7 elements
            if ($split.count -ge 7){
                write-host $line
                $writeStream.WriteLine($line)
            }
        }

        # Write to the destination blob
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

5.  Clique em **Executar Script** ou pressione **F5** para executar o script.
6.  Para examinar o arquivo de dados modificado, você pode usar o Portal de Gerenciamento do Azure ou uma ferramenta de Gerenciador de Armazenamento do Azure ou o PowerShell do Azure. A [Introdução ao HDInsight][Introdução ao HDInsight] tem um código de exemplo sobre como usar o PowerShell para baixar um arquivo e exibir o conteúdo do arquivo.

## <span id="export"></span></a>Usar o PowerShell para executar a exportação do Sqoop

Nesta seção, você usará o PowerShell do Azure para executar o comando de exportação do Sqoop para exportar uma tabela Hive e um arquivo de dados para um banco de dados SQL do Azure ou SQL Server. A próxima seção fornece um exemplo de .NET do HDInsight.

> [WACOM.NOTE] Além das informações de cadeia de caracteres de conexão, as etapas nesta seção devem funcionar para o Banco de Dados SQL do Azure ou SQL Server. Essas etapas foram testadas com relação à seguinte configuração:
>
> -   **Configuração ponto a site da rede virtual do Azure** - Uma rede virtual conectando o cluster HDInsight a um SQL Server em um datacenter privado. Consulte [Configurar um VPN ponto a site no Portal de Gerenciamento][Configurar um VPN ponto a site no Portal de Gerenciamento] para obter mais informações.
> -   **Azure HDInsight 3.1** - Consulte [Provisionar clusters Hadoop no HDInsight usando opções de personalização][Provisionar clusters Hadoop no HDInsight usando opções de personalização] para obter informações sobre a criação de um cluster em uma rede virtual
> -   **SQL Server 2014** - Configurado para permitir Autenticação SQL e executando o pacote de configuração do cliente VPN para conectar-se com segurança à rede virtual

**Para exportar o arquivo de log log4j**

1.  Abra o ISE do Windows PowerShell.
2.  No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure.

3.  Copie o seguinte script no painel de scripts e, em seguida, defina as primeiras sete variáveis:

        # Define the cluster variables
        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccount>"
        $containerName = "<BlobStorageContainerName>"

        # Define the SQL database variables
        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $databaseName = "<SQLDatabaseName>"

        $tableName_log4j = "log4jlogs"

        # Connection string for Azure SQL Database.
        # Comment if using SQL Server
        $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
        # Connection string for SQL Server.
        # Uncomment if using SQL Server.
        #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

        $exportDir_log4j = "/tutorials/usesqoop/data"

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos][Pré-requisitos] deste tutorial.

    Observe que $exportDir\_log4j não tem o nome do arquivo sample.log especificado. O Sqoop exportará os dados de todos os arquivos dessa pasta.

4.  Acrescente o seguinte script no painel de scripts:

        # Submit a Sqoop job
        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

    Observe que o delimitador de campo é **\\0x20**, que é o espaço. O delimitador é definido no script do PowerShell de pré-processamento do arquivo sample.log. Para obter informações sobre **-m 1**, consulte o [Guia do usuário do Sqoop][Sqoop].

5.  Clique em **Executar Script** ou pressione **F5** para executar o script.
6.  Use o [Portal de Gerenciamento do Azure][Portal de gerenciamento do Azure] para examinar os dados exportados.

**Para exportar a tabela Hive hivesampletable**

1.  Abra o ISE do Windows PowerShell.
2.  No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure.

3.  Copie o seguinte script no painel de scripts e, em seguida, defina as primeiras sete variáveis:

        # Define the cluster variables
        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccount>"
        $containerName = "<BlobStorageContainerName>"

        # Define the SQL database variables
        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "SQLDatabasePassword>"
        $databaseName = "SQLDatabaseName"

        $tableName_mobile = "mobiledata"

        # Connection string for Azure SQL Database.
        # Comment if using SQL Server
        $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
        # Connection string for SQL Server.
        # Uncomment if using SQL Server
        #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

        $exportDir_mobile = "/hive/warehouse/hivesampletable"

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos][Pré-requisitos] deste tutorial.

4.  Acrescente o seguinte script no painel de scripts:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"


        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Clique em **Executar Script** ou pressione **F5** para executar o script.
6.  Use o [Portal de Gerenciamento do Azure][Portal de gerenciamento do Azure] para examinar os dados exportados.

## <span id="export-sdk"></span></a>Usar o SDK do .NET do HDInsight para executar a exportação do Sqoop

A seguir, um exemplo em C# usa o SDK do .NET do HDInsight para executar exportação do Sqoop. Para obter informações gerais sobre como usar o SDK do .NET do HDInsight, consulte [Enviar trabalhos Hadoop de forma programática][Enviar trabalhos Hadoop de forma programática].

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.IO;
    using System.Threading;
    using System.Security.Cryptography.X509Certificates;
    using Microsoft.WindowsAzure.Management.HDInsight;
    using Microsoft.Hadoop.Client;

    namespace sqoopSDKSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Set the variables
                string subscriptionID = "<AzureSubscriptionID>";
                string clusterName = "<HDInsightClusterName>";
                string certFriendlyName = "<AzureCertificateFriendlyName>";
                string sqlDatabaseServerName = "<SQLDatabaseServerName>";
                string sqlDatabaseLogin = "<SQLDatabaseLogin>";
                string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                string sqlDatabaseDatabaseName = "hdisqoop";
                string sqlDatabaseTableName = "log4jlogs";

                cmdExport = @"export";
                // Connection string for using Azure SQL Database.
                // Comment if using SQL Server
                cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                // Connection string for using SQL Server.
                // Uncomment if using SQL Server
                //cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
                cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
                cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";

                SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
                {
                    Command = cmdExport,
                    StatusFolder = "/tutorials/usesqoop/jobStatus"
                };

                // Get the certificate object from certificate store using the friendly name to identify it
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Submit the Hive job
                var jobClient = JobSubmissionClientFactory.Connect(creds);
                JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);

                // Wait for the job to complete
                WaitForJobCompletion(jobResults, jobClient);

                // Print the Hive job output
                System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);

                StreamReader reader = new StreamReader(stream);
                Console.WriteLine(reader.ReadToEnd());

                Console.WriteLine("Press ENTER to continue.");
                Console.ReadLine();
            }

            private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
            {
                JobDetails jobInProgress = client.GetJob(jobResults.JobId);
                while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
                {
                    jobInProgress = client.GetJob(jobInProgress.JobId);
                    Thread.Sleep(TimeSpan.FromSeconds(10));
                }
            }
        }
    }

Para executar um arquivo script, você pode substituir:

    Command = cmdExport,

por:

    File = "/tutorials/usesqoop/sqoopexport.txt",

O arquivo script deve estar localizado no WASB.

## <span id="import"></span></a>Usar o PowerShell para executar a importação do Sqoop

Nesta seção, você importará os logs log4j (que você exportou para o banco de dados SQL) de volta no HDInsight.

1.  Abra o ISE do Windows PowerShell.
2.  No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure.

3.  Copie o seguinte script no painel de scripts e, em seguida, defina as primeiras sete variáveis:

        # Define the cluster variables
        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccount>"
        $containerName = "<BlobStorageContainerName>"

        # Define the SQL database variables
        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "SQLDatabasePassword>"
        $databaseName = "SQLDatabaseName"

        $tableName_log4j = "log4jlogs"

        # Connection string for Azure SQL Database
        # Comment if using SQL Server
        $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
        # Connection string for SQL Server
        # Uncomment if using SQL Server
        #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

        $tableName_mobile = "mobiledata"
        $targetDir_mobile = "/tutorials/usesqoop/importeddata/"

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos][Pré-requisitos] deste tutorial.

4.  Acrescente o seguinte script no painel de scripts:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Clique em **Executar Script** ou pressione **F5** para executar o script.
6.  Para examinar o arquivo de dados modificado, você pode usar o Portal de Gerenciamento do Azure ou uma ferramenta de Gerenciador de Armazenamento do Azure ou o PowerShell do Azure. A [Introdução ao HDInsight][Introdução ao HDInsight] tem um código de exemplo sobre como usar o PowerShell para baixar um arquivo e exibir o conteúdo do arquivo.

## <span id="nextsteps"></span></a>Próximas etapas

Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

-   [Usar o Oozie com o HDInsight][Usar o Oozie com o HDInsight]: use a ação do Sqoop no fluxo de trabalho do Oozie.
-   [Analisar dados de atraso de voo usando o HDInsight][Analisar dados de atraso de voo usando o HDInsight]: use o Hive para analisar dados de atraso de voos e use o Sqoop para exportar dados para o banco de dados SQL.
-   [Carregar dados no HDInsight][Carregar dados no HDInsight]: localize outros métodos de carregamento de dados no HDInsight/Armazenamento de blob do Azure.

  [O que é o Sqoop?]: #whatissqoop
  [Pré-requisitos]: #prerequisites
  [Compreender o cenário do tutorial]: #scenario
  [Preparar-se para o tutorial]: #prepare
  [Usar o PowerShell para executar a exportação do Sqoop]: #export
  [Usar o SDK do HDInsight para executar a exportação do Sqoop]: #export-sdk
  [Usar o PowerShell para executar a importação do Sqoop]: #import
  [Próximas etapas]: #nextsteps
  [Sqoop]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
  [O que há de novo nas versões de clusters fornecidas pelo HDInsight?]: ../hdinsight-component-versioning/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell
  [Executar scripts do Windows PowerShell]: http://technet.microsoft.com/pt-br/library/ee176949.aspx
  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Introdução ao uso do Banco de Dados SQL do Azure]: ../sql-database-get-started/
  [Criar e configurar o Banco de Dados SQL]: ../sql-database-create-configure/
  [Tarefas de configuração de rede virtual]: http://msdn.microsoft.com/pt-br/library/azure/jj156206.aspx
  [Provisionar clusters Hadoop no HDInsight usando opções de personalização]: /pt-br/documentation/articles/hdinsight-provision-clusters/
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Iniciar o Windows PowerShell no Windows 8 e no Windows]: http://technet.microsoft.com/pt-br/library/hh847889.aspx
  [Portal de gerenciamento do Azure]: https://manage.windowsazure.com/
  [Configurar um VPN ponto a site no Portal de Gerenciamento]: http://msdn.microsoft.com/pt-br/library/azure/dn133792.aspx
  [Enviar trabalhos Hadoop de forma programática]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Usar o Oozie com o HDInsight]: ../hdinsight-use-oozie/
  [Analisar dados de atraso de voo usando o HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
