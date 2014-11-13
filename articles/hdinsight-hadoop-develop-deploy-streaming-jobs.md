<properties urlDisplayName="" pageTitle="Desenvolver programas de streaming do Hadoop em C# para o HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="Saiba como desenvolver programas MapReduce de streaming do Hadoop em C# e como implant&aacute;-los no Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Desenvolver programas de streaming do Hadoop em C# para o HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Desenvolver programas de streaming do Hadoop em C# para o HDInsight

O Hadoop fornece uma API de streaming para o MapReduce que permite que você escreva funções de mapeamento e redução em outras linguagens além do Java. Este tutorial apresenta um cenário de contagem de palavras no qual você conta as ocorrências de uma determinada palavra nos dados de entrada fornecidos. A ilustração a seguir mostra como a estrutura do MapReduce realiza uma contagem de palavra.

![HDI.WordCountDiagram][HDI.WordCountDiagram]

Este tópico mostra como:

-   Desenvolver e testar um programa do MapReduce de streaming Hadoop usando C# em um emulador do HDInsight
-   Executar o mesmo trabalho de MapReduce no Azure HDInsight
-   Recuperar os resultados do trabalho de MapReduce

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter feito o seguinte:

-   Instale o Emulador do Azure HDInsight. Para obter instruções, consulte [Introdução ao Emulador do HDInsight][Introdução ao Emulador do HDInsight].
-   Instale o PowerShell do Azure no computador do emulador. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure]
-   Obtenha uma assinatura do Azure. Para obter instruções, consulte [Opções de compra][Opções de compra], [Ofertas para membros][Ofertas para membros] ou [Avaliação gratuita][Avaliação gratuita].

## Neste artigo

-   [Desenvolver um programa de streaming do Hadoop de contagem de palavras em C#][Desenvolver um programa de streaming do Hadoop de contagem de palavras em C#]
-   [Testar o programa no emulador][Testar o programa no emulador]
-   [Carregar os dados e os aplicativos no armazenamento de Blob do Azure][Carregar os dados e os aplicativos no armazenamento de Blob do Azure]
-   [Executar o programa MapReduce no Azure HDInsight][Executar o programa MapReduce no Azure HDInsight]
-   [Recuperar os resultados do MapReduce][Recuperar os resultados do MapReduce]
-   [Próximas etapas][Próximas etapas]

## <a name="develop"></a>Desenvolver um programa de streaming do Hadoop de contagem de palavras em C#

A solução de contagem de palavras contém dois projetos de aplicativo de console: mapeador e redutor. O aplicativo mapeador transmite cada palavra no console e o aplicativo redutor conta o número de palavras que são transmitidas de um documento. O mapeador e o redutor leem caracteres, linha por linha, do fluxo de entrada padrão (stdin) e gravam o fluxo da saída padrão (stdout).

**Para criar um aplicativo de console em C#**

1.  Abra o Visual Studio 2013.
2.  Clique em **ARQUIVO**, **Novo** e em **Projeto**.
3.  Digite ou selecione os valores a seguir:

    |-----------------|------------------------------------------|
    | Campo           | Valor                                    |
    | Modelo          | Visual C#/Windows/Aplicativo de Console |
    | Nome            | WordCountMapper                          |
    | Local           | C:\\Tutorials                            |
    | Nome da solução | WordCount                                |

4.  Clique em **OK** para criar o projeto.

**Para criar o programa mapeador**

1.  Em Gerenciador de Soluções, clique com o botão direito do mouse em **Program.cs** e em **Renomear**.
2.  Renomeie o arquivo como **WordCountMapper.cs** e pressione **ENTER**.
3.  Clique em **Sim** para confirmar a renomeação de todas as referências.
4.  Clique duas vezes em **WordCountMapper.cs** para abri-lo.
5.  Adicione a seguinte instrução usando:

        using System.IO;

6.  Substitua a função Main() por:

        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            string[] words;

            while ((line = Console.ReadLine()) != null)
            {
                words = line.Split(' ');

                foreach (string word in words)
                    Console.WriteLine(word.ToLower());
            }
        }

7.  Clique em **COMPILAÇÃO** e em **Compilar Solução** para compilar o programa Mapeador.

**Para criar o programa redutor**

1.  No Visual Studio 2013, clique em **ARQUIVO**, cem **Adicionar** e em **Novo Projeto**.
2.  Digite ou selecione os valores a seguir:

    |--------|------------------------------------------|
    | Campo  | Valor                                    |
    | Modelo | Visual C#/Windows/Aplicativo de Console |
    | Nome   | WordCountReducer                         |
    | Local  | C:\\Tutorials\\WordCount                 |

3.  Desmarque a caixa de seleção **Criar diretório para a solução** e clique em **OK** para criar o projeto.
4.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Program.cs** e em **Renomear**.
5.  Renomeie o arquivo como **WordCountReducer.cs** e pressione **ENTER**.
6.  Clique em **Sim** para confirmar a renomeação de todas as referências.
7.  Clique duas vezes em **WordCountReducer.cs** para abri-lo.
8.  Adicione a seguinte instrução usando:

        using System.IO;

9.  Substitua a função Main() por:

        static void Main(string[] args)
        {
            string word, lastWord = null;
            int count = 0;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            while ((word = Console.ReadLine()) != null)
            {
                if (word != lastWord)
                {
                    if(lastWord != null)
                        Console.WriteLine("{0}[{1}]", lastWord, count);

                    count = 1;
                    lastWord = word;
                }
                else
                {
                    count += 1; 
                }
            }
            Console.WriteLine(count);
        }

10. Clique em **COMPILAÇÃO** e em **Compilar Solução** para compilar o programa Redutor.

Os executáveis do mapeador e do redutor estão localizados em:

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

## <a name="test"></a>Testar o programa no emulador

Faça o seguinte para testar o programa no Emulador:

1.  Carregue os dados no HDFS do Emulador
2.  Carregue os aplicativos do Mapeador e do Redutor no HDFS do Emulador
3.  Envie um trabalho de MapReduce de contagem de palavras
4.  Verifique o status do trabalho
5.  Recupere os resultados do trabalho

Por padrão, o emulador do HDInsight usa o HDFS como o sistema de arquivos padrão. Opcionalmente, você pode configurar o Emulador do HDInsight para usar o Armazenamento de Blob do Azure. Para obter detalhes, consulte [Introdução ao Emulador do HDInsight][1]. Nesta seção, você usará o comando *copyFromLocal* do HDFS para carregar os arquivos. A próxima seção mostra como carregar arquivos usando o PowerShell do Azure. Para obter outros métodos, consulte [Carregar dados no HDInsight][Carregar dados no HDInsight].

Este tutorial usa a seguinte estrutura de pastas:

|-----------------------------|---------------------------------------------------|
| Pasta                       | Observação                                        |
| \\WordCount                 | A pasta raiz do projeto de contagem de palavras.  |
| \\WordCount\\Apps           | A pasta dos executáveis do mapeador e do redutor. |
| \\WordCount\\Input          | A pasta do arquivo de origem do MapReduce.        |
| \\WordCount\\Output         | A pasta do arquivo de saída do MapReduce.         |
| \\WordCount\\MRStatusOutput | A pasta de saída do trabalho.                     |

</br>

Este tutorial usa os arquivos .txt localizados no diretório %hadoop\_home%.

> [WACOM.NOTE] Os comandos de HDFS do Hadoop diferenciam maiúsculas de minúsculas.

**Para copiar os arquivos de texto no emulador do HDFS**

1.  Na janela de linha de comando do Hadoop, execute o seguinte comando para criar um diretório para os arquivos de entrada:

        hadoop fs -mkdir /WordCount/
        hadoop fs -mkdir /WordCount/Input

    O caminho usado aqui é o caminho relativo. É equivalente ao seguinte:

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  Execute o comando a seguir para copiar alguns arquivos de texto na pasta de entrada no HDFS:

        hadoop fs -copyFromLocal %hadoop_home%\share\doc\hadoop\common\*.txt \WordCount\Input

3.  Execute o comando a seguir para listar os arquivos carregados:

        hadoop fs -ls \WordCount\Input

**Para implantar o mapeador e o Redutor no emulador do HDFS**

1.  Abra a linha de comando do Hadoop na sua área de trabalho e crie a pasta /Apps no HDFS
     hadoop fs -mkdir /WordCount/Apps

2.  Execute os seguintes comandos:

        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  Execute o comando a seguir para listar os arquivos carregados

        hadoop fs -ls /WordCount/Apps

    Você deverá ver os dois arquivos .exe.

**Para executar o trabalho MapReduce usando o PowerShell do HDInsight**

1.  Abra o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure].
2.  Execute os seguintes comandos para definir as variáveis:

        $clusterName = "http://localhost:50111"

        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "/WordCount/Input/"
        $mrOutput = "/WordCount/Output"
        $mrStatusOutput = "/WordCount/MRStatusOutput"

    O nome do cluster do emulador do HDInsight é "http://localhost:50111".

3.  Execute os seguintes comandos para definir o trabalho de streaming:

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

4.  Execute o seguinte comando para criar um objeto de credencial:

        $creds = Get-Credential -Message "Enter password" -UserName "hadoop"

    Você receberá um prompt para inserir a senha. A senha pode ser qualquer cadeia de caracteres. O nome de usuário deve ser "hadoop".

5.  Execute os seguintes comandos para enviar o trabalho MapReduce e aguarde a conclusão do trabalho:

        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
        Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

    Quando concluído, você receberá uma saída semelhante à seguinte:

        StatusDirectory : /WordCount/MRStatusOutput
        ExitCode        : 
        Name            : mrWordCountStreamingJob
        Query           : 
        State           : Completed
        SubmissionTime  : 11/15/2013 7:18:16 PM
        Cluster         : http://localhost:50111
        PercentComplete : map 100%  reduce 100%
        JobId           : job_201311132317_0034

    Você pode ver a ID do trabalho na saída, por exemplo, *job-201311132317-0034*.

**Para verificar o status do trabalho**

1.  Na área de trabalho, clique em **Status do YARN do Hadoop** ou navegue até **http://localhost:50030/jobtracker.jsp**.
2.  Localize o trabalho usando a ID do trabalho na categoria **EXECUTANDO** ou **CONCLUÍDO**.
3.  Se um trabalho falhar, você poderá encontrá-lo na categoria **COM FALHA**. Você também poderá abrir os detalhes do trabalho e localizar informações úteis para depuração.

**Para exibir a saída do HDFS**

1.  Abra a linha de comando do Hadoop.
2.  Execute os comandos a seguir para exibir a saída:

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    Você pode acrescentar "|more" no final do comando para obter a exibição da página.

## <span id="upload"></span></a>Carregar dados no armazenamento de Blob do Azure

O Azure HDInsight usa o armazenamento de Blob do Azure como o sistema de arquivos padrão. Você pode configurar um cluster HDInsight para usar armazenamento de Blob adicional para os arquivos de dados. Nesta seção, você criará uma conta de armazenamento e carregará os arquivos de dados para o armazenamento de Blob. Os arquivos de dados são os arquivos .txt no diretório %hadoop\_home%\\share\\doc\\hadoop\\common.

**Para criar um armazenamento de Blob e um contêiner**

1.  Abra o PowerShell do Azure.
2.  Defina as variáveis e execute os comandos:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  Execute o comando a seguir para criar uma conta de armazenamento e um contêiner de armazenamento de Blob na conta

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName -Context $destContext

4.  Execute os comandos a seguir para verificar a conta de armazenamento e o contêiner:

        Get-AzureStorageAccount -StorageAccountName $storageAccountName
        Get-AzureStorageContainer -Context $destContext

**Para carregar os arquivos de dados**

1.  Na janela PowerShell do Azure, configure os valores para a pasta local e as pastas de destino.

        $localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
        $destFolder = "WordCount/Input"

    Observe que a pasta do arquivo de origem local é **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common**, e que a pasta de destino é **WordCount/Input**. O local de origem é o local dos arquivos .txt no Emulador do HDInsight. O destino é a estrutura de pasta que será refletida no contêiner do Blob do Azure.

2.  Execute os comandos a seguir para obter uma lista dos arquivos .txt na pasta do arquivo de origem:

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

3.  Execute o trecho a seguir para copiar os arquivos:

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
        }

4.  Execute o comando a seguir para listar os arquivos carregados:

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**Para carregar os aplicativos de contagem de palavras**

1.  Na janela PowerShell do Azure, configure as seguintes variáveis:

        $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
        $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
        $blobFolder = "WordCount/Apps"

    Observe que a pasta de destino é **WordCount/Apps**, que é a estrutura que será refletida no contêiner do Blob do Azure.

2.  Execute os comandos a seguir para copiar os aplicativos:

        Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
        Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3.  Execute o comando a seguir para listar os arquivos carregados:

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    Você deverá ver os dois arquivos do aplicativo listados.

## <a name="run"></a>Executar o trabalho MapReduce no Azure HDInsight

Esta seção oferece um script de PowerShell que realiza todas as tarefas relacionadas à execução de um trabalho de MapReduce. A lista de tarefas inclui:

1.  Provisione um cluster HDInsight

    1.  Crie uma conta de armazenamento que será usada como o sistema de arquivos padrão do cluster HDInsight
    2.  Crie um contêiner de armazenamento de Blob
    3.  Crie um cluster HDInsight

2.  Enviar o trabalho de MapReduce

    1.  Crie uma definição do trabalho de streaming MapReduce
    2.  Envie um trabalho MapReduce
    3.  Aguarde a conclusão do trabalho
    4.  Exiba o erro padrão
    5.  Exiba a saída padrão

3.  Excluir o cluster

    1.  Exclua o cluster HDInsight
    2.  Exclua a conta de armazenamento usada como o sistema de arquivos padrão do cluster HDInsight

**Para executar o script do PowerShell**

1.  Abra o Bloco de Notas.
2.  Copie e cole o seguinte código:

        # ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
        $subscriptionName = "<AzureSubscriptionName>"
        $stringPrefix = "<StringForPrefix>"     ### prefix to cluster, storage account, and container names
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = 1

        $clusterName = $stringPrefix + "hdicluster"

        $storageAccountName_Default = $stringPrefix + "hdistore"
        $containerName_Default =  $stringPrefix + "hdicluster"

        # ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        Select-AzureSubscription $subscriptionName

        #====== CREATE A STORAGE ACCOUNT ======
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #====== CREATE A BLOB STORAGE CONTAINER ======
        Write-Host "Create a Blob storage container" -ForegroundColor Green
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

        New-AzureStorageContainer -Name $containerName_Default -Context $destContext

        #====== CREATE AN HDINSIGHT CLUSTER ======
        Write-Host "Create an HDInsight cluster" -ForegroundColor Green
        $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

        Select-AzureSubscription $subscriptionName
        New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config

        #====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
        Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

        #====== RUN A STREAMING MAPREDUCE JOB ======
        Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
        Select-AzureSubscription $subscriptionName
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

        #====== DELETE THE HDINSIGHT CLUSTER ======
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Select-AzureSubscription $subscriptionName
        Remove-AzureHDInsightCluster -Name $clusterName 

        #====== DELETE THE STORAGE ACCOUNT ======
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Defina as quatro primeiras variáveis no script. O **$stringPrefix** é usado como prefixo da cadeia especificada para o nome do cluster HDInsight, o nome da conta de armazenamento e o nome do contêiner de armazenamento de Blob. Como seus nomes devem ter entre 3 e 24 caracteres, certifique-se de que a cadeia especificada e os nomes usados por este script juntos não excedam o limite de caracteres para o nome. Use tudo em minúsculas no $stringPrefix.

    **$storageAccountName\_Data** e **$containerName\_Data** são a conta de armazenamento e o contêiner já criados nas etapas anteriores. Por isso, você deve fornecer seus nomes. Eles são usados para armazenar os arquivos de dados e os aplicativos. $location deve corresponder ao local da conta de armazenamento de dados.

4.  Verifique o restante das variáveis.
5.  Salve o arquivo de script.
6.  Abra o PowerShell do Azure.
7.  Execute o seguinte comando para definir a política de execução para remotesigned:

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Quando solicitado, insira o nome de usuário e a senha do cluster HDInsight. Certifique que o campo de senha tenha pelo menos 10 caracteres e contenha uma letra maiúscula, uma letra minúscula, um número e um caractere especial. Se você não desejar que as credenciais sejam solicitadas, consulte [Trabalhando com senhas, cadeias de caracteres e credencias seguras no Windows PowerShell][Trabalhando com senhas, cadeias de caracteres e credencias seguras no Windows PowerShell]

Para obter uma amostra do SDK do .NET do HDInsight para o envio de trabalhos de streaming do Hadoop, consulte [Enviar trabalhos Hadoop de forma programática][Enviar trabalhos Hadoop de forma programática].

## <a name="retrieve"></a>Recuperar a saída do trabalho MapReduce

Esta seção mostra como baixar e exibir a saída. Para obter informações sobre como exibir os resultados no Excel, consulte [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive][Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive] e [Conectar o Excel ao HDInsight com o Power Query][Conectar o Excel ao HDInsight com o Power Query].

**Para recuperar a saída**

1.  Abra a janela do PowerShell do Azure.
2.  Defina os valores e execute os comandos:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<TheDataStorageAccountName>"
        $containerName = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-00000"

3.  Execute os seguintes comandos para criar um objeto de contexto de armazenamento do Azure:

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  Execute os comandos a seguir para baixar e exibir a saída:

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

## <span id="nextsteps"></span></a>Próximas etapas

Neste tutorial, você aprendeu a desenvolver um trabalho MapReduce de streaming do Hadoop, a testar o aplicativo no emulador do HDInsight e a escrever um script do PowerShell para provisionar um cluster HDInsight e executar um MapReduce no cluster. Para saber mais, consulte os seguintes artigos:

-   [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight]
-   [Introdução ao emulador do HDInsight][Introdução ao Emulador do HDInsight]
-   [Desenvolver programas Java MapReduce para HDInsight][Desenvolver programas Java MapReduce para HDInsight]
-   [Usar o armazenamento de blobs do Azure com o HDInsight][Usar o armazenamento de blobs do Azure com o HDInsight]
-   [Administrar o HDInsight usando o PowerShell][Administrar o HDInsight usando o PowerShell]
-   [Carregar dados no HDInsight][Carregar dados no HDInsight]
-   [Use o hive com o HDInsight][Use o hive com o HDInsight]
-   [Use o Pig com o HDInsight][Use o Pig com o HDInsight]

  [HDI.WordCountDiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "Fluxo do aplicativo de contagem de palavras MapReduce"
  [Introdução ao Emulador do HDInsight]: ../hdinsight-get-started-emulator/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Opções de compra]: http://azure.microsoft.com/pt-br/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/pt-br/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [Desenvolver um programa de streaming do Hadoop de contagem de palavras em C#]: #develop
  [Testar o programa no emulador]: #test
  [Carregar os dados e os aplicativos no armazenamento de Blob do Azure]: #upload
  [Executar o programa MapReduce no Azure HDInsight]: #run
  [Recuperar os resultados do MapReduce]: #retrieve
  [Próximas etapas]: #nextsteps
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
  [Trabalhando com senhas, cadeias de caracteres e credencias seguras no Windows PowerShell]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Enviar trabalhos Hadoop de forma programática]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Conectar o Excel ao HDInsight com o Power Query]: ../hdinsight-connect-excel-power-query/
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [Desenvolver programas Java MapReduce para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Usar o armazenamento de blobs do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Administrar o HDInsight usando o PowerShell]: ../hdinsight-administer-use-powershell/
  [Use o hive com o HDInsight]: ../hdinsight-use-hive/
  [Use o Pig com o HDInsight]: ../hdinsight-use-pig/
