<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Usar o Pig com Hadoop no HDInsight

Saiba como executar trabalhos de [Pig da Apache][] no HDInsight para analisar um arquivo de log log4j da Apache.

**Tempo estimado para conclusão:** 30 minutos

## Neste artigo

-   [Caso de uso do Pig][]
-   [Pré-requisitos][]
-   [Compreender o Pig Latin][]
-   [Enviar trabalhos de Pig usando o PowerShell][]
-   [Enviar trabalhos de Pig usando o SDK do .NET do HDInsight][]
-   [Próximas etapas][]

## <span id="usage"></span></a>Caso de uso do Pig

Os bancos de dados são excelentes para pequenos conjuntos de dados e consultas de baixa latência. No entanto, quando se trata de big data e de grandes conjuntos de dados que contêm terabytes de dados, os bancos de dados SQL tradicionais geralmente não são a solução ideal. Conforme a carga do banco de dados aumenta e o desempenho é degradado, historicamente, os administradores de banco de dados tiveram que adquirir hardware maior.

Em geral, todos os aplicativos salvam erros, exceções e outros problemas codificados em um arquivo de log para que os administradores possam examinar os problemas ou gerar determinadas métricas a partir dos dados do arquivo de log. Esses arquivos de log geralmente se tornam muito grandes e contêm uma grande quantidade de dados que devem ser processados e minerados.

Os arquivos de log são um bom exemplo de big data. Trabalhar com big data é difícil usando bancos de dados relacionais e pacotes de estatísticas/visualização. Devido às grandes quantidades de dados e à computação desses dados, software paralelo executado em dezenas, centenas ou até milhares de servidores é geralmente necessário para calcular os dados em tempo razoável. O Hadoop fornece uma estrutura MapReduce para escrever aplicativos que processam grandes quantidades de dados estruturados e não estruturados em paralelo em grandes clusters de computadores de uma maneira muito confiável e tolerante a falhas.

[O *Pig*][Pig da Apache] da Apache fornece uma linguagem de script para executar trabalhos *MapReduce* como uma alternativa a escrever código Java. A linguagem de script do Pig é chamada *Pig Latin*. As instruções de Pig Latin seguem este fluxo geral:

-   **Carregar**: Ler os dados a serem manipulados no sistema de arquivo
-   **Transformar**: Manipular os dados
-   **Despejar ou armazenar**: Dar saída nos dados para a tela ou para o armazenamento para processamento

O uso do Pig reduz o tempo necessário para escrever programas de mapeador e redutor. Isso significa que nenhum Java é necessário e não há necessidade de código clichê. Você também tem a flexibilidade de combinar código Java com Pig. Muitos algoritmos complexos podem ser escritos em menos de cinco linhas de código Pig legível.

A representação visual do que você realizará neste artigo é mostrada nas duas figuras a seguir. Estas figuras mostram um exemplo representativo do conjunto de dados para ilustrar o fluxo e a transformação dos dados à medida que você percorre as linhas de código Pig no script. A primeira figura mostra um exemplo do arquivo log4j:

![Exemplo do arquivo inteiro][]

A segunda figura mostra a transformação dos dados:

![HDI.PIG.Data.Transformation][]

Para obter mais informações sobre o Pig Latin, consulte [Manual de referência do Pig Latin 1][] e [Manual de referência do Pig Latin 2][].

## <span id="prerequisites"></span></a>Pré-requisitos

Observe os seguintes requisitos antes de começar este artigo:

-   Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight][] ou [Provisionar clusters HDInsight][]. Você precisará dos seguintes dados para percorrer o tutorial:

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
    </table>

-   Instalar e configurar o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][].

**Compreender o armazenamento do HDInsight**

O HDInsight usa o Armazenamento de Blob do Azure para armazenamento de dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][].

Durante o processo de provisionamento do HDInsight, uma conta de Armazenamento do Azure e um contêiner de armazenamento de Blob específico dessa conta são designados como o sistema de arquivos padrão. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][]. Para simplificar o script do PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutorials/usepig*. Por padrão, esse contêiner tem o mesmo nome do cluster HDInsight.
A sintaxe WASB é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Apenas a sintaxe *wasb://* tem suporte no cluster HDInsight versão 3.0. A sintaxe antiga *asv://* tem suporte nos clusters HDInsight 2.1 e 1.6, mas não tem suporte nos clusters HDInsight 3.0 e não terá suporte em versões posteriores.

> [WACOM.NOTE] O caminho WASB é um caminho virtual. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][].

Um arquivo armazenado no contêiner do sistema de arquivos padrão pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (usando sample.log como um exemplo. Este arquivo é o arquivo de dados usado neste tutorial):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

    example/data/sample.log

Neste artigo, você usará um arquivo de exemplo log4j que vem com clusters HDInsight armazenados em *\\example\\data\\sample.log*. Para obter informações sobre como carregar arquivos, consulte [Carregar dados no HDInsight][].

## <span id="understand"></span></a> Compreender o Pig Latin

Nesta sessão, você examinará algumas instruções do Pig Latin e os resultados, depois de executar as instruções. Na próxima sessão, você executará o PowerShell para executar as instruções do Pig.

1.  Carregue os dados do sistema de arquivos e, em seguida, exiba os resultados

        LOGS = LOAD 'wasb:///example/data/sample.log';
        DUMP LOGS;

    A saída deverá ser semelhante a esta:

        (2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
        (2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
        (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
        (2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
        (2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
        (2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
        (2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
        (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
        ...

2.  Percorra cada linha no arquivo de dados para localizar uma correspondência nos 6 níveis de log:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  Filtre as linhas que não têm uma correspondência. Por exemplo, as linhas vazias.

        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        DUMP FILTEREDLEVELS;

    A saída deverá ser semelhante a esta:

        (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
        ...

4.  Agrupe todos os níveis de log em sua própria linha:

        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        DUMP GROUPEDLEVELS;

    A saída deverá ser semelhante a esta:

        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE), ...

5.  Para cada grupo, conte as ocorrências dos níveis de log. Estas são as frequências de cada nível de log:

        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        DUMP FREQUENCIES;

    A saída deverá ser semelhante a esta:

        (INFO,3355)
        (WARN,361)
        (DEBUG,15608)
        (ERROR,181)
        (FATAL,37)
        (TRACE,29950)

6.  Classifique as frequências em ordem decrescente:

        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;   

    A saída deverá ser semelhante a esta:

        (TRACE,29950)
        (DEBUG,15608)
        (INFO,3355)
        (WARN,361)
        (ERROR,181)
        (FATAL,37)

## <span id="powershell"></span></a>Enviar trabalhos de Pig usando o PowerShell

Esta seção fornece instruções sobre como usar os cmdlets do PowerShell. Antes de entrar nesta seção, você deve primeiro configurar o ambiente local e configurar a conexão ao Azure. Para obter detalhes, consulte [Introdução ao Azure HDInsight][] e [Administrar o HDInsight usando o PowerShell][].

**Para executar o Pig Latin usando o PowerShell**

1.  Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell\_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][]).
2.  No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure. Esse método de adicionar uma conexão de assinatura expira e, depois de 12 horas, você precisará executar o cmdlet novamente.

    > [WACOM.NOTE] Se houver várias assinaturas do Azure e a assinatura padrão não for a que você deseja usar, use o cmdlet **Select-AzureSubscription** para selecionar a assinatura atual.

3.  Copie e cole as seguintes linhas no painel de scripts:

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  Defina a variável $clusterName.

5.  Acrescente as seguintes linhas ao painel de scripts. Essas linhas definem a cadeia de consulta Pig Latin, e cria uma definição de trabalho do Pig:

        # Create the Pig job definition
        $0 = '$0';
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;" 

        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

    Você também pode usar a opção -File para especificar um arquivo script Pig no HDFS. O comutador -StatusFolder coloca o log de ​​erro padrão e o arquivo de saída padrão na pasta.

6.  Acrescente as seguintes linhas para enviar o trabalho do Pig:

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  Acrescente as seguintes linhas para aguardar a conclusão do trabalho do Pig:

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  Acrescente as seguintes linhas para imprimir a saída do trabalho do Pig:

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] Um dos cmdlets Get-AzureHDInsightJobOut tem um comentário para encurtar a saída na tela a seguir.

9.  Pressione **F5** para executar o script:
    ![HDI.Pig.PowerShell][]

    O trabalho Pig calcula as frequências de diferentes tipos de log.

## <span id="sdk"></span></a>Enviar trabalhos de Pig usando o SDK do .NET do HDInsight

A seguir, um exemplo de envio de trabalho de Pig usando o SDK do .NET do HDInsight. Para obter instruções para criar um aplicativo C# para enviar trabalhos do Hadoop, consulte [Enviar trabalhos do Hadoop de forma programática][].

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

    namespace SubmitPigJobs
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Set the variables
                string subscriptionID = "<Azure subscription ID>";
                string certFriendlyName = "<certificate friendly name>";
        
                string clusterName = "<HDInsight cluster name>";
                string statusFolderName = @"/tutorials/usepig/status";

                string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                    "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                    "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                    "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                    "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                    "RESULT = order FREQUENCIES by COUNT desc;" +
                    "DUMP RESULT;";

                // Define the Pig job
                PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
                {
                    Query = queryString,
                    StatusFolder = statusFolderName
                };

                // Get the certificate object from certificate store using the friendly name to identify it
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Create a hadoop client to connect to HDInsight
                var jobClient = JobSubmissionClientFactory.Connect(creds);

                // Run the MapReduce job
                Console.WriteLine("----- Submit the Pig job ...");
                JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);

                // Wait for the job to complete
                Console.WriteLine("----- Wait for the Pig job to complete ...");
                WaitForJobCompletion(mrJobResults, jobClient);

                // Display the error log
                Console.WriteLine("----- The Pig job error log.");
                using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                // Display the output log
                Console.WriteLine("----- The Pig job output log.");
                using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                Console.WriteLine("----- Press ENTER to continue.");
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

## <span id="nextsteps"></span></a>Próximas etapas

Embora o Pig permita que você execute a análise de dados, outras linguagens incluídas com o HDInsight também podem ser de seu interesse. O Hive fornece uma linguagem de consulta semelhante ao SQL que permite que você consulte facilmente dados armazenados no HDInsight, enquanto os trabalhos de MapReduce escritos em Java permitem que você execute análise de dados complexos. Para obter mais informações, consulte o seguinte:

-   [Introdução ao Azure HDInsight][]
-   [Carregar dados no HDInsight][]
-   [Enviar trabalhos Hadoop de forma programática][Enviar trabalhos do Hadoop de forma programática]
-   [Use o hive com o HDInsight][]

  [Pig da Apache]: http://pig.apache.org/
  [Caso de uso do Pig]: #usage
  [Pré-requisitos]: #prerequisites
  [Compreender o Pig Latin]: #understand
  [Enviar trabalhos de Pig usando o PowerShell]: #powershell
  [Enviar trabalhos de Pig usando o SDK do .NET do HDInsight]: #sdk
  [Próximas etapas]: #nextsteps
  [Exemplo do arquivo inteiro]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Manual de referência do Pig Latin 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Manual de referência do Pig Latin 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
  [Administrar o HDInsight usando o PowerShell]: ../hdinsight-administer-use-powershell/
  [Iniciar o Windows PowerShell no Windows 8 e no Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Enviar trabalhos do Hadoop de forma programática]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Use o hive com o HDInsight]: ../hdinsight-use-hive/
