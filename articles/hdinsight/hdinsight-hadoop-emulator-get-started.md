<properties
	pageTitle="Introdução ao Emulador do Hadoop para HDInsight | Microsoft Azure"
	description="Use um emulador instalado com um tutorial do MapReduce e outros exemplos para conhecer o ecossistema do Hadoop. O Emulador do HDInsight funciona como uma área restrita do Hadoop."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="08/07/2015"
	ms.author="nitinme"/>

# Introdução ao ecossistema do Hadoop com o Emulador do HDInsight, uma área restrita do Hadoop

Este tutorial apresenta o uso de clusters Hadoop no Emulador do Microsoft HDInsight para Azure (anteriormente Visualização do Desenvolvedor do HDInsight Server). O Emulador do HDInsight é fornecido com os mesmos componentes do ecossistema do Hadoop como Azure HDInsight. Para obter detalhes, incluindo informações sobre as versões implantadas, consulte [Qual versão do Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md).

Depois de instalar o emulador, siga um tutorial do MapReduce para obter a contagem de palavras e, em seguida, execute os exemplos.

> [AZURE.NOTE]O Emulador do HDInsight inclui apenas um cluster Hadoop. Ele não inclui HBase ou Storm.


O Emulador do HDInsight fornece um ambiente de desenvolvimento local muito parecido com a área restrita do Hadoop. Se estiver familiarizado com o Hadoop, você pode começar a usar o Emulador do HDInsight utilizando o HDFS (Sistema de Arquivos Distribuído do Hadoop). No HDInsight, o sistema de arquivos padrão é o armazenamento de Blob do Azure. Por isso, talvez você queira, afinal, desenvolver seus trabalhos usando o armazenamento de Blob do Azure. Para usar o armazenamento de Blob do Azure com o Emulador do HDInsight, você deve fazer alterações na configuração do emulador.

> [AZURE.NOTE]O Emulador do HDInsight pode usar somente uma implantação de nó único.


## Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- O Emulador do HDInsight requer uma versão de 64 bits do Windows. Um dos requisitos a seguir deve ser atendido:

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack 1
	- Windows 8
	- Windows Server 2012

- **PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##<a name="install"></a>Instalar o Emulador do HDInsight

O Emulador do Microsoft HDInsight é instalável através do Microsoft Web Platform Installer.

> [AZURE.NOTE]O Emulador do HDInsight atualmente dá suporte apenas a sistemas operacionais em inglês. Se você tiver uma versão anterior do emulador instalado, desinstale os seguintes componentes em Painel de Controle/Programas e Recursos antes de instalar a versão mais recente do emulador: <ul> <li>Emulador do Microsoft HDInsight para o Azure ou HDInsight Developer Preview, o que estiver instalado</li> <li>Hortonworks Data Platform</li> </ul>


**Para instalar o Emulador do HDInsight**

1. Abra o Internet Explorer e navegue até a [página de instalação do Emulador do Microsoft HDInsight para Azure][hdinsight-emulator-install].
2. Clique em **Instalar Agora**.
3. Clique em **Executar** quando for solicitada a instalação do HDINSIGHT.exe na parte inferior da página.
4. Clique no botão **Sim** na janela **Controle de Conta de Usuário** exibida para concluir a instalação. A janela do Web Platform Installer aparece.
6. Clique em **Instalar** na parte inferior da página.
7. Clique em **Aceito** para concordar com os termos de licenciamento.
8. Verifique se o Web Platform Installer mostra **Os seguintes produtos foram instalados com êxito** e clique em **Concluir**.
9. Clique em **Sair** para fechar a janela do Web Platform Installer.

**Para verificar a instalação do Emulador do HDInsight**

A instalação deve ter inserido três ícones na área de trabalho. Os três ícones estão vinculados a estes itens:

- **Linha de comando do Hadoop**: o prompt de comando do Hadoop por meio do qual são executados trabalhos do MapReduce, do Pig e do Hive no Emulador do HDInsight.

- **Status de NameNode do Hadoop**: o NameNode mantém um diretório baseado em árvore de todos os arquivos no HDFS. Ele também controla onde são mantidos os dados de todos os arquivos em um cluster Hadoop. Os clientes se comunicam com o NameNode para saber onde os nós de dados de todos os arquivos estão armazenados.

- **Status do Hadoop YARN**: o controlador de trabalhos que aloca tarefas MapReduce para nós em um cluster.

A instalação também deve ter instalado vários serviços locais. Esta é uma captura de tela da janela Serviços:

![Serviços do ecossistema do Hadoop listados na janela do emulador.][image-hdi-emulator-services]

Os serviços relacionados ao Emulador do HDInsight não são iniciados, por padrão. Para iniciar os serviços, na linha de comando do Hadoop, execute **start\_local\_hdp\_services.cmd** em C:\\hdp (local padrão). Para iniciar automaticamente os serviços após o reinício do computador, execute **set-onebox-autostart.cmd**.

Para ver os problemas conhecidos ao instalar e executar o Emulador do HDInsight, consulte as [Notas de versão do Emulador do HDInsight](hdinsight-emulator-release-notes.md). O log de instalação está localizado em **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

##<a name="vstools"></a>Usar o Emulador com as Ferramentas do HDInsight para Visual Studio

Você pode usar as Ferramentas do HDInsight para Visual Studio para se conectar ao Emulador do HDInsight. Para obter informações sobre como usar as ferramentas do Visual Studio com clusters HDInsight no Azure, consulte [Introdução ao uso de Ferramentas do HDInsight Hadoop para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

### Instalar as ferramentas do HDInsight para o Emulador

Para obter instruções sobre como instalar as Ferramentas do HDInsight para Visual Studio, clique [aqui](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md#installation).

### Conectar ao Emulador do HDInsight

1. Abra o Visual Studio.
2. No menu **Exibir**, clique em **Gerenciador de Servidores** para abrir a janela Gerenciador de Servidores.
3. Expanda **Azure**, clique com botão direito do mouse em **HDInsight** e clique em **Conectar ao Emulador do HDInsight**.

	 ![Exibição do Visual Studio: conecte-se ao emulador do HDInsight no menu.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.png)

4. Na caixa de diálogo Conectar ao Emulador do HDInsight, confira os valores dos pontos de extremidade WebHCat, HiveServer2 e WebHDFS e clique em **Avançar**. Os valores preenchidos por padrão devem funcionar se você não fez alterações na configuração padrão do Emulador. Se você fez alterações, atualize os valores na caixa de diálogo e clique em Avançar.

	![Conecte-se à caixa de diálogo do Emulador do HDInsight com as configurações.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.dialog.png)

5. Depois que a conexão for estabelecida com êxito, clique em **Concluir**. Agora você deve ver o Emulador do HDInsight no Gerenciador de Servidores.

	![Gerenciador de Servidores mostrando o emulador local do HDInsight - uma área restrita do Hadoop - conectado.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.vs.connected.png)

Após a conexão ser estabelecida com êxito, você pode usar as ferramentas do HDInsight para VS com o Emulador, exatamente como você usaria com um cluster Azure HDInsight. Para obter instruções sobre como usar ferramentas do VS com clusters Azure HDInsight, consulte [Usando as Ferramentas do HDInsight Hadoop para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## Solução de problemas: Conectando as Ferramentas do HDInsight ao Emulador do HDInsight

1. Durante a conexão com o Emulador do HDInsight, embora a caixa de diálogo mostre HiveServer2 conectado com êxito, você deve definir manualmente a **propriedade hive.security.authorization.enabled** como **false** (falso) no arquivo de configuração em C:\\hdp\\hive-*versão*\\conf\\hive-site.xml e reiniciar o Emulador local. As Ferramentas do HDInsight para Visual Studio se conecta ao HiveServer2 somente quando você está visualizando as 100 primeiras linhas da tabela. Se não pretende usar esse tipo de consulta, você pode deixar a configuração de Hive como está.

2. Se você estiver usando alocação de IP dinâmico (DHCP) no computador que executa o Emulador do HDInsight, talvez seja necessário atualizar C:\\hdp\\hadoop-*versão*\\etc\\hadoop\\core-site.xml e alterar o valor da propriedade **hadoop.proxyuser.hadoop.hosts** para (*). Isso permite que o usuário do Hadoop se conecte de todos os hosts para representar o usuário que você inseriu no Visual Studio.

		<property>
			<name>hadoop.proxyuser.hadoop.hosts</name>
			<value>*</value>
		</property>

3. Pode ocorrer um erro quando o Visual Studio tenta se conectar ao serviço WebHCat ("erro": "Não foi possível encontrar o trabalho job\_XXXX\_0001"). Nesse caso, reinicie o serviço WebHCat e tente novamente. Para reiniciar o serviço WebHCat, inicie o MMC **Serviços**, clique com botão direito do mouse em **Apache Hadoop Templeton** (antigo nome do serviço WebHCat) e clique em **Reiniciar**.

##<a name="runwordcount"></a>Um tutorial de contagem de palavras do MapReduce

Agora que você tem o Emulador do HDInsight configurado em sua estação de trabalho, experimente este tutorial do MapReduce para testar a instalação. Primeiro, você carregará alguns arquivos de dados no HDFS e, em seguida, executará um trabalho de contagem de palavras do MapReduce para contar a frequência de palavras específicas desses arquivos.

O programa MapReduce de contagem de palavras foi empacotado em *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*. O arquivo jar está localizado na pasta *C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\hadoop\\mapreduce*.

O trabalho do MapReduce para contar palavras usa dois argumentos:

- Uma pasta de entrada. Você usará *hdfs://localhost/user/HDIUser* como pasta de entrada.
- Uma pasta de saída. Você usará **hdfs://localhost/user/HDIUser/WordCount_Output* como pasta de saída. A pasta de saída não pode ser uma pasta existente, ou o trabalho do MapReduce falhará. Se você quiser executar o trabalho do MapReduce pela segunda vez, especifique uma pasta de saída diferente ou exclua a pasta de saída existente.

**Para executar o trabalho do MapReduce de contagem de palavras**

1. Na área de trabalho, clique duas vezes em **Linha de comando do Hadoop** para abrir a janela de linha de comando do Hadoop. A pasta atual deve ser:

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	Caso contrário, execute o comando a seguir:

		cd %hadoop_home%

2. Execute os comandos do Hadoop a seguir para criar uma pasta do HDFS para armazenar os arquivos de entrada e saída:

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser

3. Execute o comando do Hadoop a seguir para copiar alguns arquivos de texto locais para o HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /user/HDIUser

4. Execute o comando a seguir para listar os arquivos da pasta /user/HDIUser:

		hadoop fs -ls /user/HDIUser

	Os arquivos a seguir devem ser exibidos:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Execute o seguinte comando para executar o trabalho do MapReduce de contagem de palavras:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Execute o comando a seguir para listar o número de palavras com "windows" do arquivo de saída:

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	A saída deve ser:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Para obter mais informações sobre comandos do Hadoop, consulte o [Manual de comandos do Hadoop][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Analise os dados do log da Web de exemplo

A instalação do Emulador do HDInsight fornece alguns exemplos como forma de Introdução aos serviços baseados em Apache Hadoop no Windows. Esses exemplos abrangem algumas tarefas geralmente necessárias ao processar um grande conjunto de dados. Aproveitando o tutorial do MapReduce acima, os exemplos o ajudarão a se familiarizar com o modelo de programação do MapReduce e seu ecossistema.

Os dados de exemplo se organizam em torno do processamento de dados de log do W3C (World Wide Web Consortium) da IIS. Há uma ferramenta de geração de dados para criar e importar os conjuntos de dados em vários tamanhos para o HDFS ou o armazenamento de Blob do Azure. (Consulte [Usar o armazenamento de Blob do Azure para o HDInsight](../hdinsight-use-blob-storage.md) para obter mais informações). Trabalhos do MapReduce, do Pig ou do Hive podem ser executados nas páginas de dados geradas pelo script do PowerShell do Azure. Os scripts do Pig e do Hive são uma camada de abstração no MapReduce e são compilados em programas MapReduce. Você pode executar uma série de trabalhos para observar os efeitos do uso dessas tecnologias diferentes e os efeitos do tamanho dos dados na execução das tarefas de processamento.

### Nesta seção

- [O cenário de dados de log do W3C da ISS](#scenarios)
- [Carregar o exemplo de dados de log do W3C](#loaddata)
- [Executar o trabalho do MapReduce em Java](#javamapreduce)
- [Executar trabalho do Hive](#hive)
- [Executar trabalho do Pig](#pig)
- [Recompilar os exemplos](#rebuild)

###<a name="scenarios"></a>Os cenários de dados de log do W3C da IIS

O cenário do W3C gera e importa dados de log do W3C da IIS, em três tamanhos, para o HDFS ou o armazenamento de Blob do Azure: 1 MB (pequeno), 500 MB (médio) e 2 GB (grande). Ele fornece três tipos de trabalho e implementa cada um deles em C#, Java, Pig e Hive.

- **totalhits**: calcula o número total de solicitações para uma determinada página.
- **avgtime**: calcula o tempo médio necessário (em segundos) para uma solicitação por página.
- **errors**: calcula o número de erros por página, por hora, para solicitações cujo status era 404 ou 500.

Essas amostras e sua documentação não fornecem um estudo detalhado ou uma implementação completa das principais tecnologias do Hadoop. O cluster usado tem apenas um único nó e, portanto, o efeito da adição de mais nós não poderá ser observado com esta versão.

###<a name="loaddata"></a>Carregar o exemplo de dados de log do W3C

A geração e a importação de dados para o HDFS são feitas por meio do script importdata.ps1 do PowerShell do Azure.

**Para importar o exemplo de dados de log do W3C**

1. Abra uma linha de comando do Hadoop na área de trabalho.
2. Altere o diretório para **C:\\hdp\\GettingStarted**.
3. Execute o comando a seguir para gerar e importar dados para o HDFS:

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted

	Se, em vez disso, você quiser carregar dados no armazenamento de Blob do Azure, consulte [Conectar ao armazenamento de Blob do Azure](#blobstorage).

4. Execute o seguinte comando na linha de comando do Hadoop para listar os arquivos importados no HDFS:

		hadoop fs -ls -R /w3c

	A saída deverá ser semelhante a esta:

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. Se quiser verificar os conteúdos do arquivo, execute o comando a seguir para exibir um dos arquivos de dados para a janela do console:

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Agora você tem os arquivos de dados criados e importados para o HDFS. Você pode começar a executar diferentes trabalhos do Hadoop.

###<a name="javamapreduce"></a> Executar trabalhos do MapReduce em Java

MapReduce é o mecanismo de computação básica do Hadoop. Por padrão, ele é implementado no Java, mas também há exemplos que aproveitam o .NET e o Hadoop Streaming que usam C#. A sintaxe para executar um trabalho do MapReduce é:

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

O arquivo jar e os arquivos de origem estão localizados na pasta C:\\Hadoop\\GettingStarted\\Java.

**Para executar um trabalho do MapReduce para calcular as visitas a uma página da Web**

1. Abra a linha de comando do Hadoop.
2. Altere o diretório para **C:\\hdp\\GettingStarted**.
3. Execute o comando a seguir para remover o diretório de saída caso a pasta exista. O trabalho do MapReduce falhará se a pasta de saída já existir.

		hadoop fs -rm -r /w3c/output

3. Execute o comando a seguir:

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	A tabela a seguir descreve os elementos do comando: <table border="1"> <tr><td>Parameter</td><td>Note</td></tr> <tr><td>w3c\_scenarios.jar</td><td>O arquivo jar se encontra em C:\\hdp\\GettingStarted\\Java folder.</td></tr> <tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>O tipo pode ser substituído por um destes: <ul> <li>microsoft.hadoop.w3c.AverageTimeTaken</li> <li>microsoft.hadoop.w3c.ErrorsByPage</li> </ul></td></tr> <tr><td>/w3c/input/small/data\_w3c\_small.txt</td><td>O arquivo de entrada pode ser substituído por um destes: <ul> <li>/w3c/input/medium/data\_w3c\_medium.txt</li> <li>/w3c/input/large/data\_w3c\_large.txt</li> </ul></td></tr> <tr><td>/w3c/output</td><td>Este é o nome da pasta de saída.</td></tr> </table>

4. Execute o comando a seguir para exibir o arquivo de saída:

		hadoop fs -cat /w3c/output/part-00000

	A saída deverá ser similar a:

		c:\Hadoop\GettingStarted>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126

	A página Default.aspx tem 3.360 visitas e assim por diante. Experimente executar os comandos novamente substituindo os valores conforme sugerido na tabela acima e observe como a saída muda com base no tipo de trabalho e no tamanho dos dados.

### <a name="hive"></a>Executar trabalhos do Hive
O mecanismo de consulta do Hive pode parecer familiar aos analistas com fortes habilidades em SQL (Structured Query Language). Esse mecanismo de consulte fornece uma interface semelhante ao SQL e um modelo de dados relacional para HDFS. O Hive usa uma linguagem denominada HiveQL, que é bastante semelhante à SQL. O Hive fornece uma camada de abstração sobre a estrutura do MapReduce baseada em Java; as consultas do Hive são compiladas no MapReduce em tempo de execução.

**Para executar um trabalho do Hive**

1. Abra uma linha de comando do Hadoop.
2. Altere o diretório para **C:\\hdp\\GettingStarted**.
3. Execute o comando a seguir para remover a pasta **/w3c/hive/input** caso ela exista. O trabalho do Hive falhará se a pasta existir.

		hadoop fs -rmr /w3c/hive/input

4. Execute o comando a seguir para criar a pasta **/w3c/hive/input** e copie os arquivos de dados para a pasta /hive/input:

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input

		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Execute o comando a seguir para processar o arquivo de script **w3ccreate.hql**. O script cria uma tabela do Hive e carrega dados para ela:

	> [AZURE.NOTE]Neste estágio, você também pode usar as Ferramentas do HDInsight para Visual Studio para executar a consulta do Hive. Abra o Visual Studio, crie um novo Projeto e, no modelo do HDInsight, selecione **Aplicativo Hive**. Aguarde o projeto se abrir e adicione a consulta como um novo item. A consulta está disponível em **C:/hdp/GettingStarted/Hive/w3c**. Após adicionar a consulta ao projeto, substitua **${hiveconf:input}** por **/w3c/hive/input** e pressione **Enviar**.

		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	A saída deverá ser semelhante a esta:

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. Execute o seguinte comando para executar o arquivo de script de HiveQL **w3ctotalhitsbypage.hql**:

	> [AZURE.NOTE]Como explicamos, você também pode executar esta consulta usando as Ferramentas do HDInsight para Visual Studio.

        C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	A tabela a seguir descreve os elementos do comando: <table border="1"> <tr><td>Arquivo</td><td>Descrição</td></tr> <tr><td>C:\\hdp\\hive-0.13.0.2.1.3.0-1981\\bin\\hive.cmd</td><td>O script de comando do Hive.</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql</td><td> Você pode substituir o arquivo de script do Hive por um destes: <ul> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql</li> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql</li> </ul> </td></tr>

	</table>

	O script do w3ctotalhitsbypage.hql HiveQL é:

		SELECT filtered.cs_uri_stem,COUNT(*)
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	O final da saída deverá ser semelhante a esta:

		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Observe que, como uma primeira etapa de cada um dos trabalhos, uma tabela será criada e dados serão carregados para ela do arquivo criado anteriormente. Você pode procurar o arquivo que foi criado examinando o nó /Hive no HDFS, usando o seguinte comando:

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Executar trabalhos do Pig

O processamento do pig usa uma linguagem de fluxo de dados chamada *Pig Latin*. Abstrações de Pig Latin fornecem estruturas de dados mais sofisticadas do que o MapReduce e, em termos de execução, estão para o Hadoop como o SQL está para sistemas de gerenciamento de bancos de dados relacionais.


**Para executar os trabalhos do Pig**

1. Abra uma linha de comando do Hadoop.
2. Altere o diretório para a pasta **C:\\hdp\\GettingStarted** pasta.
3. Execute o comando a seguir para enviar um trabalho do Pig:

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	A tabela a seguir mostra os elementos do comando: <table border="1"> <tr><td>Arquivo</td><td>Descrição</td></tr> <tr><td>C:\\hdp\\pig-0.12.1.2.1.3.0-1981\\bin\\pig.cmd</td><td>O script de comando do Pig.</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig</td><td> Você pode substituir o arquivo de script Pig Latin por um destes: <ul> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li> </ul> </td></tr> <tr><td>/w3c/input/small/data\_w3c\_small.txt</td><td> Você pode substituir o parâmetro por um arquivo maior:

	<ul>
<li>/w3c/input/medium/data_w3c_medium.txt</li>
<li>/w3c/input/large/data_w3c_large.txt</li>
</ul></td></tr> </table>

	A saída deverá ser semelhante a esta:

		(/Info.aspx,1135)
		(/UserService,1126)
		(/Default.aspx,3380)

Observe que, como os scripts do Pig são compilados para os trabalhos do MapReduce e, possivelmente, para mais de um trabalho desse tipo, você poderá ver vários trabalhos do MapReduce em execução durante o processamento de um trabalho do Pig.

<!---
### <a name="rebuild"></a>Rebuild the samples
The samples currently contain all the required binaries, so building is not required. If you'd like to make changes to the Java or .NET samples, you can rebuild them by using either the Microsoft Build Engine (MSBuild) or the included Azure PowerShell script.


**To rebuild the samples**

1. Open a Hadoop command line.
2. Run the following command:

		powershell -F buildsamples.ps1
--->

##<a name="blobstorage"></a>Conectar ao armazenamento de Blob do Azure
O Emulador do HDInsight usa o HDFS como o sistema de arquivos padrão. No entanto, o Azure HDInsight usa o armazenamento de Blob do Azure como o sistema de arquivos padrão. É possível configurar o Emulador do HDInsight para usar o armazenamento de Blob do Azure, em vez do armazenamento local. Siga as instruções abaixo para criar um contêiner de armazenamento no Azure e conectá-lo ao Emulador do HDInsight.

>[AZURE.NOTE]Para obter mais informações sobre como o HDInsight usa o armazenamento de Blob do Azure, consulte [Armazenamento de Blob do Azure Use com o HDInsight](../hdinsight-use-blob-storage.md).

Antes de começar pelas instruções abaixo, você deve ter criado uma conta de armazenamento. Para obter instruções, consulte [Como criar uma conta de armazenamento](../storage-create-storage-account.md).

**Para criar um contêiner**

1. Entre no [Portal de Visualização do Azure](https://ms.portal.azure.com/).
2. Clique em **NOVO** à esquerda, clique em **Dados + Armazenamento** e clique em **Armazenamento**.
3. Na folha Conta de Armazenamento, configure as propriedades como mostrado na captura de tela abaixo.
	
	![Criar uma conta de armazenamento](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.create.storage.png)

	Selecione **Fixar no Quadro Inicial** e clique em **Criar**.
4. Depois de criar a conta de armazenamento, da folha da conta de armazenamento nova, clique em **Contêineres** para abrir a folha de contêineres, clique em **Adicionar**.
5. Insira o nome do contêiner e clique em **Selecionar**.

	![Criar um contêiner](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.create.container.png)

Antes de poder acessar uma conta de Armazenamento do Azure, você deve adicionar o nome e a chave da conta ao arquivo de configuração.

**Para configurar a conexão a uma conta do Armazenamento do Azure**

1. Abra **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\etc\\hadoop\\core-site.xml** no Bloco de Notas.
2. Adicione a marca <property> ao lado das outras marcas <property>:

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Você deve substituir <StorageAccountName> e <StorageAccountKey> pelos valores correspondentes às informações de sua conta do Armazenamento.

3. Salve a alteração. Não é necessário reiniciar os serviços do Hadoop.

Use a seguinte sintaxe para acessar a conta do Armazenamento:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Por exemplo:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Executar o PowerShell do Azure
Alguns dos cmdlets do PowerShell do Azure também são aceitos no Emulador do HDInsight. Esses cmdlets incluem:

- Cmdlets de definição de trabalho do HDInsight

	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition
	- New-AzureHDInsightHiveJobDefinition
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

Esta é uma amostra para o envio de um trabalho do Hadoop:

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Um prompt será exibido quando você chamar Get-Credential. Você deve usar **hadoop** como nome de usuário. A senha pode ser qualquer cadeia de caracteres. O nome do cluster sempre será ****http://localhost:50111**.

Para obter mais informações sobre o envio de trabalhos do Hadoop, consulte [Enviar trabalhos do Hadoop programaticamente](hdinsight-submit-hadoop-jobs-programmatically.md). Para obter mais informações sobre os cmdlets do PowerShell do Azure para HDInsight, consulte [Referência de cmdlets do HDInsight][hdinsight-powershell-reference].


##<a name="remove"></a> Remover o Emulador do HDInsight
No computador em que o emulador está instalado, abra o Painel de Controle e, em **Programas**, clique em **Desinstalar um Programa**. Na lista de programas instalados, clique com o botão direito do mouse em **Emulador do Microsoft HDInsight para o Azure**, e clique em **Desinstalar**.


##<a name="nextsteps"></a> Próximas etapas
Neste tutorial do MapReduce, você instalou o Emulador do HDInsight (uma área restrita do Hadoop) e executou alguns trabalhos do Hadoop. Para saber mais, consulte os seguintes artigos:

- [Introdução ao uso do Azure HDInsight](../hdinsight-get-started.md)
- [Desenvolver programas MapReduce em Java para HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
- [Desenvolver programas MapReduce de streaming do Hadoop em C# para o HDInsight](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [Notas de versão do Emulador do HDInsight](hdinsight-emulator-release-notes.md)
- [Fórum do MSDN para discussão do HDInsight](http://social.msdn.microsoft.com/Forums/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: hdinsight-emulator-release-notes.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-develop-deploy-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-versions]: hdinsight-component-versioning.md

[Powershell-install-configure]: ../install-configure-powershell.md

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-hadoop-emulator-get-started/HDI.Emulator.Services.png
 

<!---HONumber=August15_HO8-->