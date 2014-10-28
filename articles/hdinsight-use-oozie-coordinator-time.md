<properties linkid="hdinsight-use-time-based-oozie-coordinator-with-hdinsight" urlDisplayName="Use time-based Hadoop Oozie Coordinator in HDInsight" pageTitle="Use time-based Hadoop Oozie Coordinator in HDInsight | Azure" metaKeywords="" description="Use time-based Hadoop Oozie Coordinator in HDInsight, a big data solution. Learn how to define Oozie workflows and coordinators, and submit coordinator jobs." metaCanonical="" services="hdinsight" documentationCenter="" title="Use time-based Hadoop Oozie Coordinator in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Usar o Coordenador baseado em tempo do Oozie com o Hadoop no HDInsight

Saiba como definir fluxos de trabalho e coordenadores e como disparar os trabalhos do coordenador baseado em tempo. É útil examinar o [Usar o Oozie com o HDInsight][Usar o Oozie com o HDInsight] antes deste artigo.

**Tempo estimado para conclusão:** 40 minutos

## Neste artigo

1.  [O que é o Oozie][O que é o Oozie]
2.  [Pré-requisitos][Pré-requisitos]
3.  [Definir arquivo de fluxo de trabalho Oozie][Definir arquivo de fluxo de trabalho Oozie]
4.  [Implantar o projeto Oozie e preparar-se para o tutorial][Implantar o projeto Oozie e preparar-se para o tutorial]
5.  [Executar o fluxo de trabalho][Executar o fluxo de trabalho]
6.  [Próximas etapas][Próximas etapas]

## <span id="whatisoozie"></span></a>O que é o Oozie

O Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos do Hadoop. É integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para o Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

O fluxo de trabalho que você implementará contém duas ações:

![Diagrama de fluxo de trabalho][Diagrama de fluxo de trabalho]

1.  Uma ação do Hive executa um script do HiveQL para contar as ocorrências de cada tipo de nível de log em um arquivo de log log4j. Cada log log4j consiste em uma linha de campos que contém um campo [LOG LEVEL] para mostrar o tipo e a gravidade. Por exemplo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    A saída do script do Hive é semelhante a:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Para obter mais informações sobre como usar o Hive, consulte [Usar o Hive com o HDInsight][Usar o Hive com o HDInsight].

2.  Uma ação do Sqoop exporta a saída da ação do HiveQL para uma tabela no banco de dados SQL do Azure. Para obter mais informações sobre como o Sqoop, consulte [Usar o Sqoop com o HDInsight][Usar o Sqoop com o HDInsight].

> [WACOM.NOTE] Para obter as versões do Oozie com suporte em clusters HDInsight, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?][O que há de novo nas versões de cluster fornecidas pelo HDInsight?].

> [WACOM.NOTE] Este tutorial funciona no cluster HDInsight versão 2.1 e 3.0. Este artigo não foi testado no emulador do HDInsight.

## <span id="prerequisites"></span></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

-   **Uma estação de trabalho** com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure]. Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Executar scripts do Windows PowerShell][Executar scripts do Windows PowerShell].
-   **Um cluster HDInsight**. Para obter informações sobre como criar um cluster HDInsight, consulte [Provisionar clusters HDInsight][Provisionar clusters HDInsight] ou [Introdução ao HDInsight][Introdução ao HDInsight]. Você precisará dos seguintes dados para percorrer o tutorial:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <tr class="header">
    <th align="left">Propriedade do cluster</th>
    <th align="left">Nome de variável do PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descrição</th>
    </tr>
    <tr class="odd">
    <td align="left">Nome do cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">O cluster HDInsight em que você executará este tutorial.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome de usuário do cluster HDInsight</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">O nome de usuário do cluster HDInsight.</td>
    </tr>
    <tr class="odd">
    <td align="left">Senha de usuário do cluster HDInsight</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">A senha de usuário do cluster HDInsight.</td>
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
    </table>

-   **Um Banco de Dados SQL Azure**. Você deve configurar uma regra de firewall para o servidor de Banco de Dados SQL para permitir o acesso a partir de sua estação de trabalho. Para obter instruções sobre como criar um Banco de Dados SQL e configurar o firewall, consulte [Introdução ao uso do Banco de Dados SQL do Azure][Introdução ao uso do Banco de Dados SQL do Azure]. Este artigo fornece um script do PowerShell para criar a tabela do Banco de Dados SQL necessária para este tutorial.

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <tr class="header">
    <th align="left">Propriedade de banco de dados SQL</th>
    <th align="left">Nome de variável do PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descrição</th>
    </tr>
    <tr class="odd">
    <td align="left">Nome do servidor de banco de dados SQL</td>
    <td align="left">$sqlDatabaseServer</td>
    <td align="left"></td>
    <td align="left">O servidor do Banco de Dados SQL para o qual o Sqoop exportará os dados.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome de logon do banco de dados SQL</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">Nome de logon do banco de dados SQL.</td>
    </tr>
    <tr class="odd">
    <td align="left">Senha de logon do banco de dados SQL</td>
    <td align="left">$sqlDatabaseLoginPassword</td>
    <td align="left"></td>
    <td align="left">Senha de logon do banco de dados SQL.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome do banco de dados SQL</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">O Banco de Dados SQL do Azure para o qual o Sqoop exportará dados.</td>
    </tr>
    </table>

    > [WACOM.NOTE] Por padrão, um banco de dados SQL do Azure permite conexões de serviços do Azure, como o Azure HDInsight. Se essa configuração de firewall estiver desabilitada, você deverá habilitá-la no Portal de Gerenciamento do Azure. Para obter instruções sobre como criar um Banco de Dados SQL e configurar regras de firewall, consulte [Criar e configurar o Banco de Dados SQL][Criar e configurar o Banco de Dados SQL].

> [WACOM.NOTE] Preencha os valores nas tabelas. Isso poderá ser útil para percorrer este tutorial.

## <span id="defineworkflow"></span></a>Definir o fluxo de trabalho do Oozie e o script HiveQL relacionado

As definições de fluxos de trabalho do Oozie são escritas em hPDL (uma Linguagem de Definição de Processo XML). O nome do arquivo do fluxo de trabalho padrão é *workflow.xml*. Você salvará o arquivo do fluxo de trabalho localmente e o implantará no cluster HDInsight usando o PowerShell do Azure posteriormente neste tutorial.

A ação do Hive no fluxo de trabalho chama um arquivo de script HiveQL. Esse arquivo de script contém três instruções HiveQL:

1.  **A instrução DROP TABLE** exclui a tabela Hive log4j caso ela exista.
2.  **A instrução CREATE TABLE** cria uma tabela externa Hive log4j apontando para o
3.  local do arquivo de log log4j. O delimitador de campo é ",". O delimitador de linha padrão é "\\n". A tabela externa Hive é usada para evitar que o arquivo de dados seja removido do local original, caso você deseje executar o fluxo de trabalho do Oozie várias vezes.
4.  **A instrução INSERT OVERWRITE** conta as ocorrências de cada tipo de nível de log da tabela Hive log4j e salva a saída em um local do Armazenamento do Azure - Blob (WASB).

Há um erro conhecido do caminho do Hive. Você encontrará esse problema ao enviar um trabalho do Oozie. As instruções para corrigir o problema podem ser encontradas no [TechNet Wiki][TechNet Wiki].

**Para definir o arquivo de script HiveQL a ser chamado pelo fluxo de trabalho:**

1.  Crie um arquivo de texto com o seguinte conteúdo:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Existem três variáveis usadas no script:

    -   ${hiveTableName}
    -   ${hiveDataFolder}
    -   ${hiveOutputFolder}

    O arquivo de definição do fluxo de trabalho (workflow.xml neste tutorial) irá passar esses valores para esse script HiveQL em tempo de execução.

2.  Salve o arquivo como **C:\\Tutorials\\UseOozie\\useooziewf.hql** usando a codificação **ANSI (ASCII)** (use o Bloco de Notas se o seu editor de texto não fornecer a opção). Esse arquivo de script será implantado no cluster HDInsight posteriormente no tutorial.

**Para definir um fluxo de trabalho**

1.  Crie um arquivo de texto com o seguinte conteúdo:

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/> 

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg> 
                <arg>${sqlDatabaseConnectionString}</arg> 
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg> 
                <arg>--export-dir</arg> 
                <arg>${hiveOutputFolder}</arg> 
                <arg>-m</arg> 
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    Existem duas ações definidas no fluxo de trabalho. A ação de início é *RunHiveScript* Se a ação for executada *ok*, a próxima ação será *RunSqoopExport*.

    O RunHiveScript possui várias variáveis. Você irá passar os valores ao enviar o trabalho do Oozie de sua estação de trabalho usando o PowerShell do Azure.

	<table border="1">
    <tr><th> Variáveis de fluxo de trabalho </th><th> Descrição </th></tr>
    <tr><td> ${jobTracker}                  </td><td> Especifique a URL do controlador do trabalho do Hadoop. Use <strong>jobtrackerhost:9010</strong> no cluster HDInsight versão 2.0 e 3.0.                                                            </td></tr>
    <tr><td> ${nameNode}                    </td><td> Especifique a URL do namenode do Hadoop. Use o endereço WASB do sistema de arquivos padrão. Por exemplo, <i>wasb://&lt;NomeContêiner&gt;@&lt;NomeContaArmazenamento&gt;.blob.core.windows.net</i>. </td></tr>
    <tr><td> ${queueName}                   </td><td> Especifica o queuename ao qual o trabalho será enviado. Use <strong>padrão</strong>.                                                                                                               </td></tr>
	</table>

	<table border="1">
    <tr><th> Variável de ação do Hive </th><th> Descrição  </th></tr>
    <tr><td> ${hiveDataFolder}        </td><td> O diretório de origem do comando Hive Create Table.                   </td></tr>
    <tr><td> ${hiveOutputFolder}      </td><td> A pasta de saída da instrução INSERT OVERWRITE.                       </td></tr>
    <tr><td> ${hiveTableName}         </td><td> O nome da tabela Hive que faz referência aos arquivos de dados log4j. </td></tr>
	</table>

	<table border="1">
    <tr><th> Variável de ação do Sqoop      </th><th> Descrição </th></tr>
    <tr><td> ${sqlDatabaseConnectionString} </td><td> Cadeia de conexão do Banco de Dados SQL.                                                                              </td></tr>
    <tr><td> ${sqlDatabaseTableName}        </td><td> A tabela do Banco de Dados SQL para onde os dados serão exportados.                                                   </td></tr>
    <tr><td> ${hiveOutputFolder}            </td><td> A pasta de saída para a instrução Hive INSERT OVERWRITE. Essa é a mesma pasta para export-dir de Exportação do Sqoop. </td></tr>
	</table>

    Para obter mais informações sobre o fluxo de trabalho do Oozie e como usar ações de fluxo de trabalho, consulte a [Documentação do Apache Oozie 4.0][Documentação do Apache Oozie 4.0] (para a versão 3.0 do cluster HDInsight) ou a [Documentação do Oozie Apache 3.3.2][Documentação do Oozie Apache 3.3.2] (para a versão 2.1 do cluster HDInsight).

2.  Salve o arquivo como **C:\\Tutorials\\UseOozie\\workflow.xml** usando a codificação ANSI (ASCII) (use o Bloco de Notas se o seu editor de texto não fornecer a opção).

**Para definir o coordenador**

1.  Crie um arquivo de texto com o seguinte conteúdo:

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
           <action>
              <workflow>
                 <app-path>${wfPath}</app-path>
              </workflow>
           </action>
        </coordinator-app>

    Há cinco variáveis usadas no arquivo de definição:

	<table border="1">
    <tr><th> Variável          </th><th> Descrição </th></tr>
    <tr><td> ${coordFrequency} </td><td> Tempo de pausa do trabalho. A frequência sempre é expressa em minutos.                                                                                                                         </td></tr>
    <tr><td> ${coordStart}     </td><td> Hora de início do trabalho.                                                                                                                                                                    </td></tr>
    <tr><td> ${coordEnd}       </td><td> Hora de término do trabalho.                                                                                                                                                                   </td></tr>
    <tr><td> ${coordTimezone}  </td><td> O Oozie processa os trabalhos do coordenador em um fuso horário fixo sem horário de verão (normalmente UTC). Esse fuso horário é referenciado como o “Fuso horário de processamento do Oozie”. </td></tr>
    <tr><td> ${wfPath}         </td><td> O caminho para o workflow.xml. Se o nome do arquivo do fluxo de trabalho não for o nome do arquivo padrão (workflow.xml), você deverá especificá-lo.                                           </td></tr>
	</table>

2.  Salve o arquivo como **C:\\Tutorials\\UseOozie\\coordinator.xml** usando a codificação ANSI (ASCII) (use o Bloco de Notas se o seu editor de texto não fornecer a opção).

## <span id="deploy"></span></a>Implantar o projeto Oozie e preparar-se para o tutorial

Você executará um script do PowerShell do Azure para realizar o seguinte:

-   Copiar o script do HiveQL (useoozie.hql) para o armazenamento de Blob do Azure, wasb:///tutorials/useoozie/useoozie.hql.
-   Copiar workflow.xml para wasb:///tutorials/useoozie/workflow.xml.
-   Copiar coordinator.xml para wasb:///tutorials/useoozie/coordinator.xml.
-   Copiar o arquivo de dados (/example/data/sample.log) para wasb:///tutorials/useoozie/data/sample.log.
-   Criar uma tabela do Banco de Dados SQL para armazenar os dados de exportação do Sqoop. O nome da tabela é *log4jLogCount*.

**Compreender o armazenamento do HDInsight**

O HDInsight usa o Armazenamento de Blob do Azure para armazenamento de dados. Ele é chamado *WASB* ou *Armazenamento do Windows Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Ao provisionar um cluster HDInsight, uma conta de Armazenamento do Azure e um contêiner de Armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão, exatamente como no HDFS. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][Provisionar clusters HDInsight]. Para simplificar o script do PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutoriais/useoozie*. Por padrão, esse contêiner tem o mesmo nome do cluster HDInsight.
A sintaxe WASB é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Apenas a sintaxe *wasb://* tem suporte no cluster HDInsight versão 3.0. A sintaxe antiga *asv://* tem suporte nos clusters HDInsight 2.1 e 1.6, mas não tem suporte nos clusters HDInsight 3.0 e não terá suporte em versões posteriores.

> [WACOM.NOTE] O caminho WASB é um caminho virtual. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Um arquivo armazenado no contêiner do sistema de arquivos padrão pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (usando workflow.xml como um exemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

    tutorials/useoozie/workflow.xml

**Compreender a tabela interna e a tabela externa do Hive**

Existem algumas coisas que você precisa saber sobre a tabela interna e a tabela externa do Hive:

-   O comando CREATE TABLE cria uma tabela interna, também conhecida como uma tabela gerenciada. O arquivo de dados deve estar localizado no contêiner padrão.
-   O comando CREATE TABLE move o arquivo de dados para a pasta /hive/warehouse/<tablename> no contêiner padrão.
-   O comando CREATE EXTERNAL TABLE cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.
-   O comando CREATE EXTERNAL TABLE não move o arquivo de dados.
-   O comando CREATE EXTERNAL TABLE não permite nenhuma subpasta dentro da pasta especificada na cláusula LOCATION. Essa é a razão pela qual o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: Hive Internal and External Tables Intro (HDInsight: Introdução às tabelas internas e externas do Hive)][HDInsight: Hive Internal and External Tables Intro (HDInsight: Introdução às tabelas internas e externas do Hive)].

**Para preparar-se para o tutorial**

1.  Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell\_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][Iniciar o Windows PowerShell no Windows 8 e no Windows]).
2.  No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure. Esse método de adicionar uma conexão de assinatura expira e, depois de 12 horas, você precisará executar o cmdlet novamente.

    > [WACOM.NOTE] Se houver várias assinaturas do Azure e a assinatura padrão não for a que você deseja usar, use o cmdlet **Select-AzureSubscription** para selecionar a assinatura atual.

3.  Copie o script a seguir no painel de script e defina as seis primeiras variáveis

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial  
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos][Pré-requisitos] deste tutorial.

4.  Acrescente o seguinte ao script no painel de script:

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {       
            Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

5.  Clique em **Executar Script** ou pressione **F5** para executar o script. A saída deverá ser similar a:

    ![Saída de preparação do tutorial][Saída de preparação do tutorial]

## <span id="run"></span></a>Executar o projeto Oozie

Atualmente, o PowerShell do Azure não fornece nenhum cmdlet para definir trabalhos do Oozie. Você pode usar o cmdlet
Invoke-RestMethod PowerShell para invocar os serviços Web do Oozie. A API de Serviços Web do Oozie é uma API JSON REST HTTP. Para obter mais informações sobre a API de Serviços Web do Oozie, consulte a [Documentação do Apache Oozie 4.0][Documentação do Apache Oozie 4.0] (para a versão 3.0 do cluster HDInsight) ou a [Documentação do Oozie Apache 3.3.2][Documentação do Oozie Apache 3.3.2] (para a versão 2.1 do cluster HDInsight).

**Para enviar um trabalho do Oozie**

1.  Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell\_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][Iniciar o Windows PowerShell no Windows 8 e no Windows]).

2.  Copie o script a seguir no painel de script e defina as catorze primeiras variáveis (ignore a 6ª, $storageUri).

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF/coordinator variables
        $coordStart = "2014-03-21T13:45Z"
        $coordEnd = "2014-03-21T13:45Z"
        $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
        $coordTimezone = "UTC"  #UTC/GMT

        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos][Pré-requisitos] deste tutorial.

    $coordstart e $coordend são a hora de início e de término do fluxo de trabalho. Para localizar o horário UTC/GMT, pesquise "hora utc" no bing.com. O $coordFrequency é a frequência, em minutos, na qual você deseja executar o fluxo de trabalho.

3.  Acrescente o texto a seguir ao script. Esta parte define a carga do Oozie:

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>oozie.coord.application.path</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>wfPath</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>coordStart</name>
               <value>$coordStart</value>
           </property>

           <property>
               <name>coordEnd</name>
               <value>$coordEnd</value>
           </property>

           <property>
               <name>coordFrequency</name>
               <value>$coordFrequency</value>
           </property>

           <property>
               <name>coordTimezone</name>
               <value>$coordTimezone</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>admin</value>
           </property>

        </configuration>
        "@

    > [WACOM.NOTE] A principal diferença ao se comparar com o arquivo de carga de envio do fluxo de trabalho é a variável **oozie.coord.application.path**. Ao enviar um trabalho de fluxo de trabalho, use **oozie.wf.application.path** em vez disso.

4.  Acrescente o texto a seguir ao script. Esta parte verifica o status do serviço web do Oozie:

        function checkOozieServerStatus()
        {
            Write-Host "Checking Oozie server status..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieServerSatus = $jsonResponse[0].("systemMode")
            Write-Host "Oozie server status is $oozieServerSatus..."

            if($oozieServerSatus -notmatch "NORMAL")
            {
                Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                exit 1
            }
        }

5.  Acrescente o texto a seguir ao script. Esta parte cria um trabalho Oozie:

        function createOozieJob()
        {
            # create Oozie job
            Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
            Write-Host "`n--------`n$OoziePayload`n--------"
            $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieJobId = $jsonResponse[0].("id")
            Write-Host "Oozie job id is $oozieJobId..."

            return $oozieJobId
        }

    > [WACOM.NOTE] Ao enviar uma tarefa do fluxo de trabalho, faça outra chamada do serviço Web para iniciar o trabalho depois de ele ser criado. Nesse caso, o trabalho do coordenador é disparado por tempo. O trabalho será iniciado automaticamente.

6.  Acrescente o texto a seguir ao script. Esta parte verifica o status do trabalho do Oozie:

        function checkOozieJobStatus($oozieJobId)
        {
            # get job status
            Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

            Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
            $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")

            while($JobStatus -notmatch "SUCCEEDED|KILLED")
            {
                Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
                Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
                $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
                $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
                $JobStatus = $jsonResponse[0].("status")
            }

            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
            if($JobStatus -notmatch "SUCCEEDED")
            {
                Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                exit -1
            }
        }

7.  (Opcional) Acrescente o texto a seguir ao script.

        function listOozieJobs()
        {
            Write-Host "Listing Oozie jobs..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 

            write-host "Job ID                                   App Name        Status      Started                         Ended"
            write-host "----------------------------------------------------------------------------------------------------------------------------------"
            foreach($job in $response.workflows)
            {
                Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
            }
        }

        function ShowOozieJobLog($oozieJobId)
        {
            Write-Host "Showing Oozie job info..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
            write-host $response
        }

        function killOozieJob($oozieJobId)
        {
            Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
            $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
            $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
        }

8.  Acrescente ao script o seguinte:

        checkOozieServerStatus
        # listOozieJobs
        $oozieJobId = createOozieJob($oozieJobId)
        checkOozieJobStatus($oozieJobId)
        # ShowOozieJobLog($oozieJobId)
        # killOozieJob($oozieJobId)

    Remova os sinais \# se desejar executar as funções adicionais.

9.  Se o seu cluster HDInsight for a versão 2.1, substitua "https://$clusterName.azurehdinsight.net:443/oozie/v2/“ por “https://$clusterName.azurehdinsight.net:443/oozie/v1/“. A versão 2.1 do cluster HDInsight não oferece suporte à versão 2 dos serviços web.

10. Clique em **Executar Script** ou pressione **F5** para executar o script. A saída será semelhante a:

    ![Saída do fluxo de trabalho da execução do tutorial][Saída do fluxo de trabalho da execução do tutorial]

11. Conecte-se ao Banco de Dados SQL para ver os dados exportados.

**Para verificar o log de erros do trabalho**

Para solucionar problemas de um fluxo de trabalho, o arquivo de log Oozie pode ser encontrado em C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log do headnode do cluster. Para obter informações sobre RDP, consulte [Administrando clusters HDInsight usando o Portal de Gerenciamento][Administrando clusters HDInsight usando o Portal de Gerenciamento].

**Para executar o tutorial novamente**

Para executar novamente o fluxo de trabalho, execute o seguinte procedimento:

-   exclua o arquivo de saída do script do Hive
-   exclua os dados na tabela log4jLogsCount

Este é um exemplo de script do PowerShell que você pode usar:

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

## <span id="nextsteps"></span></a>Próximas etapas

Neste tutorial, você aprendeu como definir um fluxo de trabalho do Oozie, um coordenador do Oozie e como executar um trabalho de coordenador do Oozie usando o PowerShell do Azure. Para saber mais, consulte os seguintes artigos:

-   [Introdução ao HDInsight][Introdução ao HDInsight]
-   [Introdução ao emulador do HDInsight][Introdução ao emulador do HDInsight]
-   [Usar o armazenamento de blobs do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight]
-   [Administrar o HDInsight usando o PowerShell][Administrar o HDInsight usando o PowerShell]
-   [Carregar dados no HDInsight][Carregar dados no HDInsight]
-   [Use o Sqoop com o HDInsight][Usar o Sqoop com o HDInsight]
-   [Use o hive com o HDInsight][Usar o Hive com o HDInsight]
-   [Use o Pig com o HDInsight][Use o Pig com o HDInsight]
-   [Desenvolver trabalhos de streaming do Hadoop em C# para o HDInsight][Desenvolver trabalhos de streaming do Hadoop em C# para o HDInsight]
-   [Desenvolver programas Java MapReduce para HDInsight][Desenvolver programas Java MapReduce para HDInsight]

  [Usar o Oozie com o HDInsight]: ../hdinsight-use-oozie/
  [O que é o Oozie]: #whatisoozie
  [Pré-requisitos]: #prerequisites
  [Definir arquivo de fluxo de trabalho Oozie]: #defineworkflow
  [Implantar o projeto Oozie e preparar-se para o tutorial]: #deploy
  [Executar o fluxo de trabalho]: #run
  [Próximas etapas]: #nextsteps
  [Diagrama de fluxo de trabalho]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
  [Usar o Hive com o HDInsight]: ../hdinsight-use-hive/
  [Usar o Sqoop com o HDInsight]: ../hdinsight-use-sqoop/
  [O que há de novo nas versões de cluster fornecidas pelo HDInsight?]: ../hdinsight-component-versioning/
  [Instalar e configurar o PowerShell do Azure]: ../install-and-configure-powershell/
  [Executar scripts do Windows PowerShell]: http://technet.microsoft.com/pt-br/library/ee176949.aspx
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Introdução ao uso do Banco de Dados SQL do Azure]: ../sql-database-get-started/
  [Criar e configurar o Banco de Dados SQL]: ../sql-database-create-configure/
  [TechNet Wiki]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [Documentação do Apache Oozie 4.0]: http://oozie.apache.org/docs/4.0.0/
  [Documentação do Oozie Apache 3.3.2]: http://oozie.apache.org/docs/3.3.2/
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [HDInsight: Hive Internal and External Tables Intro (HDInsight: Introdução às tabelas internas e externas do Hive)]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Iniciar o Windows PowerShell no Windows 8 e no Windows]: http://technet.microsoft.com/pt-br/library/hh847889.aspx
  [Saída de preparação do tutorial]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
  [Saída do fluxo de trabalho da execução do tutorial]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png
  [Administrando clusters HDInsight usando o Portal de Gerenciamento]: ../hdinsight-administer-use-management-portal/
  [Introdução ao emulador do HDInsight]: ../hdinsight-get-started-emulator/
  [Administrar o HDInsight usando o PowerShell]: ../hdinsight-administer-use-powershell/
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
  [Use o Pig com o HDInsight]: ../hdinsight-use-pig/
  [Desenvolver trabalhos de streaming do Hadoop em C# para o HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Desenvolver programas Java MapReduce para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
