<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" author="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Introdução ao emulador do HDInsight

Este tutorial apresenta o uso de clusters Hadoop para o Emulador do Microsoft HDInsight para Azure (anteriormente Visualização do Desenvolvedor do HDInsight Server). O Emulador do HDInsight é fornecido com os mesmos componentes do ecossistema do Hadoop como Azure HDInsight. Para obter detalhes, incluindo informações sobre as versões implantadas, consulte [Qual versão do Hadoop está no Azure HDInsight?][].

O Emulador do HDInsight fornece um ambiente de desenvolvimento local para o Azure HDInsight. Se estiver familiarizado com o Hadoop, você poderá começar a usar o Emulador com o HDFS. No entanto, no HDInsight, o sistema de arquivos padrão é o armazenamento de Blob do Azure (WASB, também conhecido como o Armazenamento do Azure – Blobs), portanto, em algum momento você desejará desenvolver seus trabalhos usando o WASB. Você pode começar a desenvolver no WASB usando o Emulador de Armazenamento do Azure – provavelmente desejará apenas usar um pequeno subconjunto dos dados (nenhuma alteração de configuração é necessária no Emulador do HDInsight, apenas um nome de conta de armazenamento diferente). Em seguida, você testará seus trabalhos localmente no Armazenamento do Windows Azure - novamente, usando apenas um subconjunto dos dados (requer uma alteração de configuração no Emulador do HDInsight). Finalmente, você estará pronto para mover a parte de computação do seu trabalho para o HDInsight e executar um trabalho em relação aos dados de produção.

> [WACOM.NOTE] O Emulador do HDInsight pode usar somente uma implantação de nó único.

Para obter um tutorial sobre como usar o HDInsight, consulte [Introdução ao uso do Azure HDInsight][].

**Pré-requisitos**
Antes de começar este tutorial, você deve ter o seguinte:

-   O Emulador do HDInsight requer uma versão de 64 bits do Windows. Um dos requisitos a seguir deve ser atendido:

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack 1
    -   Windows 8
    -   Windows Server 2012.
-   Instalar e configurar o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][].

## Neste tutorial

-   [Instale o Emulador do HDInsight][]
-   [Execute a amostra de contagem de palavras][]
-   [Execute as amostras de introdução][]
-   [Conectar-se ao armazenamento de Blob do Azure][]
-   [Execute o PowerShell do HDInsight][]
-   [Próximas etapas][]

## <a name="install"></a>Instale o Emulador do HDInsight

O Emulador do Microsoft HDInsight é instalável através do Microsoft Web Platform Installer.

> [WACOM.NOTE] No momento, o Emulador do HDInsight somente oferece suporte ao sistema operacional em inglês.

> [WACOM.NOTE] Se você tiver a Visualização do Desenvolvedor do Microsoft HDInsight instalada, desinstale primeiro estes dois componentes no Painel de Controle/Programas e Recursos.
>
> -   Visualização do Desenvolvedor do HDInsight
> -   Visualização do Desenvolvedor do Hortonworks Data Platform
>
> </p>

**Para instalar o Emulador do HDInsight**

1.  Abra o Internet Explorer e navegue até a [página de instalação do Emulador do Microsoft HDInsight para Azure][].
2.  Clique em **Instalar Agora**.
3.  Clique em **Executar** quando for solicitada a instalação do HDINSIGHT.exe na parte inferior da página.
4.  Clique no botão **Sim** na janela **Controle de Conta de Usuário** exibida para concluir a instalação. A janela do Web Platform Installer 4.6 será exibida.
5.  Clique em **Instalar** na parte inferior da página.
6.  Clique em **Aceito** para concordar com os termos de licenciamento.
7.  Verifique se o Web Platform Installer exibe a mensagem **os produtos a seguir foram instalados com êxito** e, em seguida, clique em **Concluir**.
8.  Clique em **Sair** para fechar a janela do Web Platform Installer 4.6.

    A instalação deve ter inserido três ícones na área de trabalho. Os três ícones estão vinculados a estes itens:

    -   **Linha de comando do Hadoop**: o prompt de comando do Hadoop do qual os trabalhos do MapReduce, Pig e Hive são executados no Emulador do HDInsight.

    -   **Status do NameNode do Hadoop**: o NameNode mantém um diretório em forma de árvore de todos os arquivos no HDFS. Ele também mantém registros de onde os dados de todos os arquivos são mantidos em um cluster Hadoop. Os clientes se comunicam com o NameNode para saber onde os nós de dados de todos os arquivos estão armazenados.

    -   **Status do MapReduce do Hadoop**: o controlador de trabalho que aloca tarefas do MapReduce para os nós de um cluster.

    A instalação também deve ter instalado vários serviços locais. Esta é uma captura de tela da janela Serviços:

    ![HDI.Emulator.Services][]

    Para obter informações sobre problemas conhecidos relacionados à instalação e à execução do HDInsight Server, consulte as [notas de versão do Emulador do HDInsight][]. O log de instalação está localizado em **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

## <a name="runwordcount"></a>Executar um trabalho do MapReduce de contagem de palavras

Agora você tem o emulador do HDInsight configurado na estação de trabalho. Você pode executar um trabalho MapReduce para testar a instalação. Primeiro, você carregará alguns arquivos de texto no HDFS e, em seguida, executará um trabalho de contagem de palavras do MapReduce para contar as frequências de palavras desses arquivos.

O programa de contagem de palavras MapReduce foi compactado em *hadoop-examples.jar*. O arquivo jar está localizado na pasta *C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT*.

A sintaxe do comando jar é:

    hadoop jar <jar> [mainClass] args...

Você também usará alguns comandos fs. Para obter mais informações sobre comandos do Hadoop, consulte o [Manual de comandos do Hadoop][].

O trabalho do MapReduce de contagem de palavra usa dois argumentos: uma pasta de entrada e uma pasta de saída. Você usará *hdfs://localhost/user/HDIUser* como a pasta de entrada e *hdfs://localhost/user/HDIUser/WordCount\_Output* como o diretório de saída. A pasta de saída não poderá ser uma pasta existente, caso contrário o trabalho do MapReduce falhará. Se você quiser executar o trabalho do MapReduce pela segunda vez, especifique uma pasta de saída diferente ou exclua a pasta de saída existente.

**Para executar o trabalho de contagem de palavras do MapReduce**

1.  Na área de trabalho, clique duas vezes na **Linha de comando do Hadoop** para abrir a janela de linha de comando do Hadoop. A pasta atual deve ser:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    Caso contrário, execute o comando a seguir:

        cd %hadoop_home%

2.  Execute o comando do Hadoop a seguir para criar uma pasta do HDFS para armazenar os arquivos de entrada e saída:

        hadoop fs -mkdir /user/HDIUser

3.  Execute o comando do Hadoop a seguir para copiar alguns arquivos locais para o HDFS:

        hadoop fs -copyFromLocal *.txt /user/HDIUser/

4.  Execute o comando a seguir para listar os arquivos da pasta /user/HDIUser:

        hadoop fs -ls /user/HDIUser

    Os arquivos a seguir devem ser exibidos:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
        Found 8 items
        -rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
        -rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
        -rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
        -rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
        -rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
        -rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
        -rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
        -rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5.  Execute o comando a seguir para processar o trabalho de contagem de palavras do MapReduce:

        hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  Execute o comando a seguir para listar as palavras com "windows" do arquivo de saída:

        hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    A saída deve ser:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
        rt-r-00000 | findstr "windows"
        windows 12
        windows+java6.  1
        windows.        3

## <a name="rungetstartedsamples"></a> Executar as amostras de introdução

A instalação do Emulador do HDInsight fornece algumas amostras para que os novos usuários comecem a aprender rapidamente os serviços no Windows com base no Apache Hadoop. Essas amostras abordam algumas tarefas que geralmente são necessárias no processamento de um grande conjunto de dados. Examine as amostras para se familiarizar com os conceitos associados ao modelo de programação do MapReduce e ao seu ecossistema.

As amostras são organizadas com base no processamento dos cenários de dados de log do IIS W3C. Uma ferramenta de geração de dados é fornecida para criar e importar os conjuntos de dados de vários tamanhos para o HDFS ou WASB (armazenamento de Blob do Azure). Consulte [Usar o armazenamento de Blob do Azure para HDInsight][] para obter mais informações). Em seguida, os trabalhos do MapReduce, do Pig ou do Hive poderão ser executados nas páginas de dados gerados pelo script de PowerShell. Observe que os scripts do Pig e do Hive usados são compilados para os programas MapReduce. Os usuários podem executar uma série de trabalhos para observar, por eles mesmos, os efeitos de uso dessas tecnologias diferentes e os efeitos do tamanho dos dados na execução das tarefas de processamento.

### Nesta seção

-   [Os cenários de dados de log do IIS w3c][]
-   [Carregar dados de log do w3c de amostra][]
-   [Executar trabalhos do Java MapReduce][]
-   [Executar trabalhos do Hive][]
-   [Executar trabalhos do Pig][]
-   [Recompilar as amostras][]

### <a name="scenarios"></a>Os cenários de dados de log do IIS w3c

O cenário do w3c gera e importa dados de log do IIS W3C em três tamanhos para o HDFS ou o WASB: 1 MB, 500 MB e 2 GB. Ele fornece três tipos de trabalho e implementa cada um deles em C#, Java, Pig e Hive.

-   **totalhits**: calcula o número total de solicitações para uma determinada página
-   **avgtime**: calcula o tempo médio necessário (em segundos) para uma solicitação por página
-   **errors**: calcula o número de erros por página, por hora, para as solicitações cujo status eram 404 ou 500

Essas amostras e sua documentação não fornecem um estudo detalhado ou uma implementação completa das principais tecnologias do Hadoop. O cluster usado tem apenas um único nó e, portanto, o efeito da adição de mais nós não poderá ser observado com esta versão.

### <a name="loaddata"></a>Cenários de dados de log do W3c de amostra

A geração e a importação dos dados para o HDFS é feita usando o importdata.ps1 de script do PowerShell.

**Para importar dados de log do w3c de amostra:**

1.  Abra a linha de comando do Hadoop na área de trabalho.
2.  Execute o comando a seguir para alterar o diretório para **C:\Hadoop\GettingStarted**:

        cd \Hadoop\GettingStarted

3.  Execute o comando a seguir para gerar e importar dados para o HDFS:

        powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    Em vez disso, se você quiser carregar dados para o WASB, consulte [Conectar-se ao armazenamento de Blob do Azure][].

4.  Execute o comando a seguir a partir da linha de comando do Hadoop para listar os arquivos importados no HDFS:

        hadoop fs -lsr /w3c

    A saída deverá ser semelhante a esta:

        c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
        -rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
        -rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
        -rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5.  Execute o comando a seguir para exibir um dos arquivos de dados para a janela do console:

        hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Agora você tem o arquivo de dados criado e importado para o HDFS. Você pode executar diferentes trabalhos do Hadoop.

### <a name="javamapreduce"></a> Executar trabalhos do Java MapReduce

MapReduce é o mecanismo de computação básica do Hadoop. Por padrão, ele é implementado no Java, mas também há exemplos que aproveitam o .NET e o Hadoop Streaming que usam C#. A sintaxe para executar um trabalho do MapReduce é:

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

O arquivo jar e os arquivos de origem estão localizados na pasta C:\Hadoop\GettingStarted\Java.

**Para executar um trabalho do MapReduce para calcular as visitas à página da Web**

1.  Abra a linha de comando do Hadoop.
2.  Execute o comando a seguir para alterar o diretório para **C:\Hadoop\GettingStarted**:

        cd \Hadoop\GettingStarted

3.  Execute o comando a seguir para remover o diretório de saída caso a pasta exista. O trabalho do MapReduce falhará se a pasta de saída já existir.

        hadoop fs -rmr /w3c/output

4.  Execute o comando a seguir:

        hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	A tabela a seguir descreve os elementos do comando:

    <table border="1">
    <tr>
    <td>
    Parâmetro
    </td>
    <td>
    Observação
    </td>
    </tr>
    <tr>
    <td>
    w3c_scenarios.jar
    </td>
    <td>
    O arquivo jar está localizado na pasta C:\Hadoop\GettingStarted\Java.
    </td>
    </tr>
    <tr>
    <td>
    microsoft.hadoop.w3c.TotalHitsForPage
    </td>
    <td>
    O tipo pode ser substituído por um dos procedimentos a seguir:
	<ul>
    <li>microsoft.hadoop.w3c.AverageTimeTaken</li>
    <li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul>
    </td>
    </tr>
    <tr>
    <td>
    /w3c/input/small/data_w3c_small.txt
    </td>
    <td>
    O arquivo de entrada pode ser substituído por um dos procedimentos a seguir:
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
    <li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
    </td>
    </tr>
    <tr>
    <td>
    /w3c/output
    </td>
    <td>
    Este é o nome da pasta de saída.
    </td>
    </tr>
    </table>
5.  Execute o comando a seguir para exibir o arquivo de saída:

        hadoop fs -cat /w3c/output/part-00000

    A saída deverá ser similar a:

        c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130

    Portanto, a página Default.aspx terá 3.409 visitas e assim por diante.

### <a name="hive"></a>Executar trabalhos do Hive

Os analistas com fortes habilidades SQL se sentirão familiarizados com o mecanismo de consulta Hive. Esse mecanismo de consulte fornece uma interface semelhante ao SQL e um modelo de dados relacional para HDFS. O Hive usa uma linguagem denominada HiveQL (ou HQL), que é um dialeto do SQL.

**Para executar um trabalho do Hive**

1.  Abra a linha de comando do Hadoop.
2.  Altere o diretório para a pasta **C:\Hadoop\GettingStarted**
3.  Execute o comando a seguir para remover a pasta **/w3c/hive/input** caso ela exista. O trabalho do Hive falhará se a pasta existir.

        hadoop fs -rmr /w3c/hive/input

4.  Execute o comando a seguir para criar a pasta **/w3c/hive/input** e copie o arquivo de dados da estação de trabalho para o HDFS:

        hadoop fs -mkdir /w3c/hive/input
        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  Execute o comando a seguir para processar o arquivo de script **w3ccreate.hql**. O script cria uma tabela do Hive e carrega dados para ela:

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    O script do HiveQL é:

        DROP TABLE w3c;

        CREATE TABLE w3c(
         logdate string,
         logtime string,
         c_ip string,
         cs_username string,
         s_ip string,
         s_port string,
         cs_method string,
         cs_uri_stem string,
         cs_uri_query string,
         sc_status int,
         sc_bytes int,
         cs_bytes int,
         time_taken int,
         cs_agent string, 
         cs_Referrer string)
        ROW FORMAT delimited
        FIELDS TERMINATED BY ' ';

        LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    A saída deverá ser semelhante a esta:

        c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea    te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
        Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
        Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
        OK
        Time taken: 0.616 seconds
        OK
        Time taken: 0.139 seconds
        Loading data to table default.w3c
        Moved to trash: hdfs://localhost:8020/apps/hive/warehouse/w3c
        OK
        Time taken: 0.573 seconds

6.  Execute o comando a seguir para processar o arquivo de script do HiveQL **w3ctotalhitsbypate.hql**.

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

    A tabela a seguir descreve os elementos do comando:

    <table border="1">
    <tr>
    <td>
    Arquivo
    </td>
    <td>
    Descrição
    </td>
    </tr>
    <tr>
    <td>
    C:\Hadoop\hive-0.9.0\bin\hive.cmd
    </td>
    <td>
    O script de comando do Hive.
    </td>
    </tr>
    <tr>
    <td>
    C:\Hadoop\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql
    </td>
    <td>
    Você pode substituir o arquivo de script do Hive por um destes:
	<ul>
    <li>C:\Hadoop\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
    <li>C:\Hadoop\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
    </td>
    </tr>
    </p>
    </table>
    O script do w3ctotalhitsbypage.hql HiveQL é:

        SELECT filtered.cs_uri_stem,COUNT(*) 
        FROM (
          SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
        ) filtered
        GROUP BY (filtered.cs_uri_stem);

    O final da saída deverá ser semelhante a esta:

        MapReduce Total cumulative CPU time: 3 seconds 47 msec
        Ended Job = job_201310291309_0006
        MapReduce Jobs Launched:
        Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.047 sec   HDFS Read: 1058546 HDFS W
        rite: 53 SUCCESS
        Total MapReduce CPU Time Spent: 3 seconds 47 msec
        OK
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130
        Time taken: 34.68 seconds

Observe que, como uma primeira etapa de cada um dos trabalhos, uma tabela será criada e dados serão carregados para ela do arquivo criado anteriormente. Você pode procurar o arquivo criado, examinando o nó /Hive no HDFS usando o comando a seguir:

    hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Executar trabalhos do Pig

O processamento do Pig usa uma linguagem de fluxo de dados, denominada *Pig Latin*. As abstrações de Pig Latin fornecem estruturas de dados mais sofisticadas que o MapReduce e executam para Hadoop o mesmo executado pelo SQL para sistemas RDBMS.

**Para executar os trabalhos do Pig:**

1.  Abra a linha de comando do Hadoop.
2.  Altere o diretório para a pasta C:\\Hadoop\\GettingStarted.
3.  Execute o comando a seguir para enviar um trabalho do Pig:

        C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    A tabela a seguir mostra os elementos do comando:

    <table border="1">
    <tr>
    <td>
    Arquivo
    </td>
    <td>
    Descrição
    </td>
    </tr>
    <tr>
    <td>
    C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd
    </td>
    <td>
    O script de comando do Pig.
    </td>
    </tr>
    <tr>
    <td>
    C:\Hadoop\GettingStarted\Pig\w3c\TotalHitsForPage.pig
    </td>
    <td>
    Você pode substituir o arquivo de script de Pig Latin por um destes:
	<ul>
    <li>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li>
    <li>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li>
	</ul>
    </td>
    </tr>
    <tr>
    <td>
    /w3c/input/small/data_w3c_small.txt
    </td>
    <td>
    Você pode substituir o parâmetro por um arquivo maior:
	<ul>
    <li>/w3c/input/medium/data_w3c_medium.txt</li>
    <li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
    </td>
    </tr>
    </table>
    A saída deverá ser semelhante a esta:

        (/Info.aspx,1115)
        (/UserService,1130)
        (/Default.aspx,3409)

Observe que, como os scripts do Pig são compilados para os trabalhos do MapReduce e, possivelmente, para mais de um trabalho desse tipo, os usuários poderão ver vários trabalhos do MapReduce em execução durante o processamento de um trabalho do Pig.

### <a name="rebuild"></a>Recompilar as amostras

No momento, as amostras contêm todos os binários necessários, portanto, a compilação não é necessária. Se quiser fazer alterações nas amostras de Java ou .NET, você poderá recompilá-las usando msbuild ou o script do PowerShell incluído.

**Para recompilar as amostras**

1.  Abra a linha de comando do Hadoop.
2.  Execute o comando a seguir:

        powershell -F buildsamples.ps1

## <a name="blobstorage"></a>Conectar-se ao armazenamento de Blob do Azure

O Azure HDInsight usa o armazenamento de Blob do Azure como o sistema de arquivos padrão. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o armazenamento de Blob do Azure para HDInsight].

É possível configurar um cluster local no Emulador do HDInsight para usar o armazenamento de Blob do Azure em vez do armazenamento local. A seção inclui:

-   conectar-se ao emulador de armazenamento
-   conectar-se ao armazenamento de Blob do Azure
-   configurar um armazenamento de Blob do Azure como o sistema de arquivos padrão para o Emulador do HDInsight

### Conectar-se ao emulador de armazenamento

O Emulador de Armazenamento do Azure é fornecido com o [SDK do Azure para .NET][]. O emulador de armazenamento não é iniciado automaticamente. Você deve iniciá-lo manualmente. O nome do aplicativo é *Emulador de Armazenamento do Azure*. Para iniciar/parar os emuladores, clique com botão direito do mouse no ícone azul do Azure na bandeja de sistema do Windows e, em seguida, clique em Mostrar Interface do Usuário do Emulador de Armazenamento.

> [WACOM.NOTE] A mensagem de erro a seguir poderá ser exibida ao iniciar o emulador de armazenamento:

>     The process cannot access the file because it is being used by another process.

> Isso ocorre porque um dos serviços do Hive no Hadoop também usa a porta 10000. Para resolver o problema, execute o procedimento a seguir:

> 1.  Interrompa os serviços do Hive no Hadoop usando services.msc: Apache Hadoop Hiveserver e Apache Hadoop Hiveserver2.
> 2.  Inicie o emulador de armazenamento de Blob.
> 3.  Reinicie os dois serviços do Hive no Hadoop.

A sintaxe para acessar o emulador de armazenamento é:

    wasb://<ContainerName>@storageemulator

Por exemplo:

    hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] Se a mensagem de erro a seguir for exibida:

>     ls: No FileSystem for scheme: wasb

> É porque você ainda está usando a versão de Visualização do Desenvolvedor. Siga as instruções fornecidas na seção Instalar o Emulador do HDInsight neste artigo para desinstalar a versão de visualização do desenvolvedor e, em seguida, reinstalar o aplicativo.

### Conectar-se ao armazenamento de Blob do Azure

Para obter instruções sobre como criar uma conta de armazenamento, consulte [Como criar uma conta de armazenamento][].

**Para criar um contêiner**

1.  Entre no [Portal de Gerenciamento][].
2.  Clique em **ARMAZENAMENTO** à esquerda. Você verá uma lista de contas de armazenamento sob a sua assinatura.
3.  Clique na conta de armazenamento onde deseja criar o contêiner da lista.
4.  Clique em **CONTÊINERES** na parte superior da página.
5.  Clique em **ADICIONAR** na parte inferior da página.
6.  Insira o **NOME** e selecione **ACESSO**. Você pode usar qualquer um dos três níveis de acesso. O padrão é **Particular**.
7.  Clique em **OK** para salvar as alterações. Você verá o novo contêiner listado no portal.

Antes de poder acessar uma conta de Armazenamento do Azure, você deve adicionar o nome e a chave da conta ao arquivo de configuração.

**Para configurar a conexão a uma conta de Armazenamento do Azure**

1.  Abra **C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf\core-site.xml** no Bloco de Notas.
2.  Adicione a marca \<property\> ao lado das outras marcas \<property\>:

        <property>
            <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
            <value><StorageAccountKey></value>
        </property>

    Você precisa substituir \<StorageAccountName\> e \<StorageAccountKey\> pelos valores que correspondem às informações de sua conta de armazenamento.

3.  Salve a alteração. Não é necessário reiniciar os serviços do Hadoop.

Use a sintaxe a seguir para acessar a conta de armazenamento:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Por exemplo:

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

### Use um contêiner de armazenamento de Blob do Azure como o sistema de arquivos padrão

Também é possível usar um contêiner de armazenamento de Blob do Azure como o sistema de arquivos padrão, como é o caso no Azure HDInsight.

**Para configurar o sistema de arquivos padrão usando um contêiner de armazenamento de Blob do Azure**

1.  Abra **C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf\core-site.xml** no Bloco de Notas.
2.  Encontre a marca \<property\> a seguir:

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <value>hdfs://localhost:8020</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

3.  Substitua a marca pelas duas marcas \<property\> a seguir:

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <!--<value>hdfs://localhost:8020</value>-->
          <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

        <property>
          <name>dfs.namenode.rpc-address</name>
          <value>hdfs://localhost:8020</value>
          <description>A base for other temporary directories.</description>
        </property>

    Você precisa substituir \<StorageAccountName\> e \<StorageAccountKey\> pelos valores que correspondem às informações de sua conta de armazenamento.

4.  Salve as alterações.
5.  Abra a linha de comando do Hadoop na área de trabalho no modo elevado (Executar como administrador).
6.  Execute os comandos a seguir para reiniciar os serviços do Hadoop:

        C:\Hadoop\stop-onebox.cmd
        C:\Hadoop\start-onebox.cmd

7.  Execute o comando a seguir para testar a conexão com o sistema de arquivos padrão:

        hadoop fs -ls /

    Os comandos a seguir listam o conteúdo na mesma pasta:

        hadoop fs -ls wasb:///
        hadoop fs -ls wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/
        hadoop fs -ls wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

    Para acessar o HDFS, execute o comando a seguir:

        hadoop fs -ls hdfs://localhost:8020/

## <a name="powershell"></a> Executar o PowerShell do HDInsight

Alguns dos cmdlets do PowerShell do HDInsight recebem suporte no Emulador do HDInsight. Esses cmdlets incluem:

-   Cmdlets de definição de trabalho do HDInsight

    -   New-AzureHDInsightSqoopJobDefinition
    -   New-AzureHDInsightStreamingMapReduceJobDefinition
    -   New-AzureHDInsightPigJobDefinition
    -   New-AzureHDInsightHiveJobDefinition
    -   New-AzureHDInsightMapReduceJobDefinition
-   Start-AzureHDInsightJob
-   Get-AzureHDInsightJob
-   Wait-AzureHDInsightJob

Esta é uma amostra para o envio de um trabalho do Hadoop:

    $creds = Get-Credential (hadoop as username, password can be anything)
    $hdinsightJob = <JobDefinition>
    Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Um prompt será exibido quando você chamar Get-Credential. Você deve usar **hadoop** como o nome de usuário. A senha pode ser qualquer cadeia de caracteres. O nome do cluster é sempre **<http://localhost:50111>**.

Para obter mais informações para o envio de trabalhos do Hadoop, consulte [Enviar trabalhos do Hadoop de forma programática][]. Para obter mais informações sobre os cmdlets do PowerShell do HDInsight, consulte [Referência a cmdlets do HDInsight][].

## <a name="nextsteps"></a>Próximas etapas

Neste tutorial, você instalará um Emulador do HDInsight e executará alguns trabalhos do Hadoop. Para saber mais, consulte os seguintes artigos:

-   [Introdução ao uso do Azure HDInsight][]
-   [Desenvolver programas Java MapReduce para HDInsight][]
-   [Desenvolver programas MapReduce de streaming do Hadoop em C# para o HDInsight][]
-   [Notas de versão do emulador do HDInsight][notas de versão do Emulador do HDInsight]
-   [Fórum do MSDN para discussão do HDInsight][]

  [Qual versão do Hadoop está no Azure HDInsight?]: ../hdinsight-component-versioning/
  [Introdução ao uso do Azure HDInsight]: ../hdinsight-get-started/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Instale o Emulador do HDInsight]: #install
  [Execute a amostra de contagem de palavras]: #runwordcount
  [Execute as amostras de introdução]: #rungetstartedsamples
  [Conectar-se ao armazenamento de Blob do Azure]: #blobstorage
  [Execute o PowerShell do HDInsight]: #powershell
  [Próximas etapas]: #nextsteps
  [página de instalação do Emulador do Microsoft HDInsight para Azure]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
  [HDI.Emulator.Services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png
  [notas de versão do Emulador do HDInsight]: ../hdinsight-emulator-release-notes/
  [Manual de comandos do Hadoop]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html
  [Usar o armazenamento de Blob do Azure para HDInsight]: ../hdinsight-use-blob-storage/
  [Os cenários de dados de log do IIS w3c]: #scenarios
  [Carregar dados de log do w3c de amostra]: #loaddata
  [Executar trabalhos do Java MapReduce]: #javamapreduce
  [Executar trabalhos do Hive]: #hive
  [Executar trabalhos do Pig]: #pig
  [Recompilar as amostras]: #rebuild
  [SDK do Azure para .NET]: http://azure.microsoft.com/en-us/downloads/
  [Como criar uma conta de armazenamento]: ../storage-create-storage-account/
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [Enviar trabalhos do Hadoop de forma programática]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Referência a cmdlets do HDInsight]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [Desenvolver programas Java MapReduce para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Desenvolver programas MapReduce de streaming do Hadoop em C# para o HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Fórum do MSDN para discussão do HDInsight]: http://social.msdn.microsoft.com/Forums/en-US/hdinsight
