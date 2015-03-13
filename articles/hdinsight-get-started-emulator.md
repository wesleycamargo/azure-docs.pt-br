<properties 
	pageTitle="Introdução ao emulador do HDInsight | Azure" 
	description="Saiba como usar o emulador do HDInsight para Azure." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	author="nitinme" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="nitinme"/>

# Introdução ao emulador do HDInsight 

Este tutorial apresenta o uso de clusters Hadoop no Emulador do Microsoft HDInsight para Azure (anteriormente Visualização do Desenvolvedor do HDInsight Server). O Emulador do HDInsight é fornecido com os mesmos componentes do ecossistema do Hadoop como Azure HDInsight. Para obter detalhes, incluindo informações sobre as versões implantadas, consulte [Qual versão do Hadoop está no Azure HDInsight?][hdinsight-versions].

>[AZURE.NOTE] O Emulador do HDInsight inclui apenas um cluster Hadoop. Ele não inclui HBase.

O Emulador do HDInsight fornece um ambiente de desenvolvimento local para o Azure HDInsight. Se estiver familiarizado com o Hadoop, você poderá começar a usar o Emulador com o HDFS. No HDInsight, o sistema de arquivo padrão é o armazenamento de Blob do Azure (WASB, também conhecido como Armazenamento do Azure - Blobs). Por isso, em algum momento, você desejará desenvolver seus trabalhos usando o WASB. Para usar o WASB com o Emulador do HDInsight, você deve realizar alterações na configuração do Emulador do HDInsight. 

> [AZURE.NOTE]  O Emulador do HDInsight pode usar somente uma implantação de nó único. 


**Pré-requisitos**	
Antes de começar este tutorial, você deve ter o seguinte:

- O Emulador do HDInsight requer uma versão de 64 bits do Windows. Um dos requisitos a seguir deve ser atendido:

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack 1
	- Windows 8 
	- Windows Server 2012

- Instalar e configurar o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure]. 

## Neste tutorial

* [Instalar o Emulador do HDInsight](#install)
* [Executar a amostra de contagem de palavras](#runwordcount)
* [Executar as amostras de introdução](#rungetstartedsamples)
* [Conectar-se ao armazenamento de Blob do Azure](#blobstorage)
* [Executar o PowerShell do HDInsight](#powershell)
* [Remover o emulador do HDInsight](#remove)
* [Próximas etapas](#nextsteps)

##<a name="install"></a>Instale o Emulador do HDInsight

O Emulador do Microsoft HDInsight é instalável através do Microsoft Web Platform Installer.  

> [AZURE.NOTE] No momento, o Emulador do HDInsight só oferece suporte ao sistema operacional em inglês. Se você tiver a versão anterior do Emulador instalada, você deve desinstalar estes dois componentes no Painel de Controle/Programas e Recursos antes de instalar a versão mais recente do Emulador.
><ul>
<li>Emulador do Microsoft HDInsight para Microsoft Azure ou Visualização do Desenvolvedor do HDInsight, o que estiver instalado.</li>
<li>Hortonworks Data Platform</li>
</ul>


**Para instalar o Emulador do HDInsight**

1. Abra o Internet Explorer e navegue até a [página de instalação do Emulador do Microsoft HDInsight para Azure][hdinsight-emulator-install].
2. Clique em **Instalar Agora**. 
3. Clique em **Executar** quando for solicitada a instalação do HDINSIGHT.exe na parte inferior da página. 
4. Clique no botão **Sim** na janela **Controle de Conta de Usuário** exibida para concluir a instalação. Você deve ver a caixa de diálogo do Web Platform Installer.
6. Clique em **Instalar** na parte inferior da página.
7. Clique em **Aceito** para concordar com os termos de licenciamento.
8. Verifique se o Web Platform Installer mostra o status **Os seguintes produtos foram instalados com sucesso** e clique em **Concluir**.
9. Clique em **Sair** para fechar a janela do Web Platform Installer.

**Para verificar a instalação do Emulador do HDInsight**
	
A instalação deve ter inserido três ícones na área de trabalho. Os três ícones estão vinculados a estes itens: 
	
- **Linha de comando do Hadoop**: o prompt de comando do Hadoop do qual os trabalhos do MapReduce, Pig e Hive são executados no Emulador do HDInsight.

- **Status do NameNode do Hadoop**: o NameNode mantém um diretório em forma de árvore de todos os arquivos no HDFS. Ele também mantém registros de onde os dados de todos os arquivos são mantidos em um cluster Hadoop. Os clientes se comunicam com o NameNode para saber onde os nós de dados de todos os arquivos estão armazenados.
	
- **Status do Yarn do Hadoop**: o controlador de trabalho que aloca tarefas do MapReduce para os nós de um cluster.

A instalação também deve ter instalado vários serviços locais. Esta é uma captura de tela da janela Serviços:

![HDI.Emulator.Services][0]

Os serviços relacionados ao Emulador do HDInsight não são iniciados por padrão. Para iniciar os serviços, na linha de comando do Hadoop, execute **start\_local\_hdp_services.cmd** em <system drive\>\hdp. Para iniciar automaticamente os serviços após o reinício do computador, execute **set-onebox-autostart.cmd**.  

Para obter informações sobre problemas conhecidos relacionados à instalação e à execução do HDInsight Server, consulte as [notas de versão do Emulador do HDInsight][hdinsight-emulator-release-notes]. O log de instalação está localizado em **C:\HadoopFeaturePackSetup\HadoopFeaturePackSetupTools\gettingStarted.winpkg.install.log**.


##<a name="runwordcount"></a>Executar um trabalho do MapReduce de contagem de palavras

Agora que você tem o emulador do HDInsight configurado na estação de trabalho,  você pode executar um trabalho MapReduce para testar a instalação. Primeiro, você carregará alguns arquivos de dados no HDFS e, em seguida, executará um trabalho de contagem de palavras do MapReduce para contar a frequência de palavras específicas desses arquivos. 

O programa de contagem de palavras MapReduce foi compactado em *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*.  O arquivo jar está localizado na pasta *C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce*.

O trabalho do MapReduce para contar palavras usa dois argumentos:

- Uma pasta de entrada. Você usará *hdfs://localhost/user/HDIUser* como a pasta de entrada.
- Uma pasta de saída. Você usará *hdfs://localhost/user/HDIUser/WordCount_Output* como o diretório de saída. A pasta de saída não poderá ser uma pasta existente, caso contrário o trabalho do MapReduce falhará. Se você quiser executar o trabalho do MapReduce pela segunda vez, especifique uma pasta de saída diferente ou exclua a pasta de saída existente. 

**Para executar o trabalho de contagem de palavras do MapReduce**

1. Na área de trabalho, clique duas vezes na **Linha de comando do Hadoop** para abrir a janela de linha de comando do Hadoop.  A pasta atual deve ser:

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	Caso contrário, execute o comando a seguir:

		cd %hadoop_home%

2. Execute os comandos do Hadoop a seguir para criar uma pasta do HDFS para armazenar os arquivos de entrada e saída:

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser
	
3. Execute o comando do Hadoop a seguir para copiar alguns arquivos de texto locais para o HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /user/HDIUser

4. Execute o comando a seguir para listar os arquivos da pasta /user/HDIUser:

		hadoop fs -ls /user/HDIUser

	Os arquivos a seguir devem ser exibidos:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Execute o comando a seguir para processar o trabalho de contagem de palavras do MapReduce:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981> hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Execute o comando a seguir para listar o número de palavras com "windows" do arquivo de saída:

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	A saída deve ser:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Para obter mais informações sobre comandos do Hadoop, consulte o [Manual de comandos do Hadoop][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Executar as amostras de introdução

A instalação do Emulador do HDInsight fornece algumas amostras para que os usuários comecem a aprender sobre os serviços no Windows com base no Apache Hadoop. Essas amostras abordam algumas tarefas que geralmente são necessárias no processamento de um conjunto de big data. Examine as amostras para se familiarizar com os conceitos associados ao modelo de programação do MapReduce e ao seu ecossistema.

As amostras são organizadas com base no processamento dos cenários de dados de log do IIS W3C. Uma ferramenta de geração de dados é fornecida para criar e importar os conjuntos de dados de vários tamanhos para o HDFS ou WASB (armazenamento de Blob do Azure). Consulte [Usar o armazenamento de Blob do Azure para HDInsight][hdinsight-storage] para obter mais informações). Em seguida, trabalhos do MapReduce, do Pig ou do Hive poderão ser executados nas páginas de dados gerados pelo script de PowerShell. Observe que os scripts do Pig e do Hive são uma camada de abstração no MapReduce e são compilados para os programas MapReduce. Os usuários podem executar uma série de trabalhos para observar os efeitos do uso dessas tecnologias diferentes e os efeitos do tamanho dos dados na execução das tarefas de processamento. 

### Nesta seção

- [Os cenários de dados de log do IIS w3c](#scenarios)
- [Carregar dados de log do w3c de amostra](#loaddata)
- [Executar trabalhos do Java MapReduce](#javamapreduce)
- [Executar trabalhos do Hive](#hive)
- [Executar trabalhos do Pig](#pig)
- [Recompilar as amostras](#rebuild)

###<a name="scenarios"></a>Os cenários de dados de log do IIS w3c

O cenário do w3c gera e importa dados de log do IIS W3C em três tamanhos para o HDFS ou o WASB: 1MB (pequeno), 500MB (médio) e 2GB (grande). Ele fornece três tipos de trabalho e implementa cada um deles em C#, Java, Pig e Hive.

- **totalhits**: calcula o número total de solicitações para uma determinada página 
- **avgtime**: calcula o tempo médio necessário (em segundos) para uma solicitação por página 
- **errors**: calcula o número de erros por página, por hora, para as solicitações cujo status eram 404 ou 500 

Essas amostras e sua documentação não fornecem um estudo detalhado ou uma implementação completa das principais tecnologias do Hadoop. O cluster usado tem apenas um único nó e, portanto, o efeito da adição de mais nós não poderá ser observado com esta versão. 

###<a name="loaddata"></a>Cenários de dados de log do W3c de amostra

A geração e a importação dos dados para o HDFS é feita usando o importdata.ps1 de script do PowerShell.

**Para importar dados de log do w3c de amostra:**

1. Abra a linha de comando do Hadoop na área de trabalho.
2. Altere o diretório para **C:\hdp\GettingStarted**.
3. Execute o comando a seguir para gerar e importar dados para o HDFS:

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

	Em vez disso, se você quiser carregar dados para o WASB, consulte [Conectar-se ao armazenamento de Blob do Azure](#blobstorage).

4. Execute o comando a seguir por meio da linha de comando do Hadoop para listar os arquivos importados no HDFS:

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

###<a name="javamapreduce"></a> Executar trabalhos do Java MapReduce

MapReduce é o mecanismo de computação básica do Hadoop. Por padrão, ele é implementado no Java, mas também há exemplos que aproveitam o .NET e o Hadoop Streaming que usam C#. A sintaxe para executar um trabalho do MapReduce é:

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

O arquivo jar e os arquivos de origem estão localizados na pasta C:\Hadoop\GettingStarted\Java.

**Para executar um trabalho do MapReduce para calcular as visitas à página da Web**

1. Abra a linha de comando do Hadoop.
2. Altere o diretório para **C:\hdp\GettingStarted**.
3. Execute o comando a seguir para remover o diretório de saída caso a pasta exista.  O trabalho do MapReduce falhará se a pasta de saída já existir.

		hadoop fs -rm -r /w3c/output

3. Execute o comando a seguir:

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	A tabela a seguir descreve os elementos do comando:
	<table border="1">
	<tr><td>Parâmetro</td><td>Observação</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>O arquivo .jar está localizado na pasta C:\hdp\GettingStarted\Java.</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>O tipo pode ser substituído por um dos procedimentos a seguir: 
	<ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
	<li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul></td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>O arquivo de entrada pode ser substituído por um dos procedimentos a seguir:
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul></td></tr>
	<tr><td>/w3c/output</td><td>Este é o nome da pasta de saída.</td></tr>
	</table>

4. Execute o comando a seguir para exibir o arquivo de saída:

		hadoop fs -cat /w3c/output/part-00000

	A saída deverá ser semelhante a:

		c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137

	A página Default.aspx tem 3.360 visitas e assim por diante. Experimente executar os comandos novamente substituindo os valores conforme sugerido na tabela acima e observe como a saída muda com base no tipo de trabalho e no tamanho dos dados.

### <a name="hive"></a>Executar trabalhos do Hive
Os analistas com fortes habilidades em SQL poderão se sentir familiarizados com o mecanismo de consulta Hive. Esse mecanismo de consulte fornece uma interface semelhante ao SQL e um modelo de dados relacional para HDFS. O Hive usa uma linguagem denominada HiveQL, que é bastante semelhante à SQL. O Hive oferece uma camada de abstração na estrutura do MapReduce baseada em Java, e as consultas do Hive são compiladas no MapReduce no tempo de execução.

**Para executar um trabalho do Hive**

1. Abra a linha de comando do Hadoop.
2. Altere o diretório para **C:\hdp\GettingStarted**.
3. Execute o comando a seguir para remover a pasta **/w3c/hive/input** caso ela exista.  O trabalho do Hive falhará se a pasta existir.

		hadoop fs -rmr /w3c/hive/input

4. Execute o comando a seguir para criar a pasta **/w3c/hive/input** e copie os arquivos de dados para a pasta /hive/input:

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input
        
		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Execute o comando a seguir para processar o arquivo de script **w3ccreate.hql**.  O script cria uma tabela do Hive e carrega dados para ela:
        
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

6. Execute o comando a seguir para executar o arquivo de script do HiveQL **w3ctotalhitsbypate.hql**.  

        C:\hdp\hive-0.13.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	A tabela a seguir descreve os elementos do comando:
	<table border="1">
	<tr><td>Arquivo</td><td>Descrição</td></tr>
	<tr><td>C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd</td><td>O script de comando do Hive.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Você pode substituir o arquivo de script do Hive por um destes:
	<ul>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>

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
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Observe que, como uma primeira etapa de cada um dos trabalhos, uma tabela será criada e dados serão carregados para ela do arquivo criado anteriormente. Você pode procurar o arquivo criado, examinando o nó /Hive no HDFS usando o comando a seguir:

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Executar trabalhos do Pig

O processamento do Pig usa uma linguagem de fluxo de dados, denominada *Pig Latin*. As abstrações de Pig Latin fornecem estruturas de dados mais sofisticadas que o MapReduce e executam para Hadoop o mesmo executado pelo SQL para sistemas RDBMS. 


**Para executar os trabalhos do Pig:**

1. Abra a linha de comando do Hadoop.
2. Altere o diretório para a pasta **C:\hdp\GettingStarted**.
3. Execute o comando a seguir para enviar um trabalho do Pig:

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	A tabela a seguir mostra os elementos do comando:
	<table border="1">
	<tr><td>Arquivo</td><td>Descrição</td></tr>
	<tr><td>C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd</td><td>O script de comando do Pig.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td> Você pode substituir o arquivo de script de Pig Latin por um destes:
	<ul>
	<li>C:\hdp\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\hdp\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td> Você pode substituir o parâmetro por um arquivo maior:
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

	A saída deverá ser semelhante a esta:

		(/Info.aspx,1156)
		(/UserService,1137)
		(/Default.aspx,3360)
		
Observe que, como os scripts do Pig são compilados para os trabalhos do MapReduce e, possivelmente, para mais de um trabalho desse tipo, você poderá ver vários trabalhos do MapReduce em execução durante o processamento de um trabalho do Pig.


### <a name="rebuild"></a>Recompilar as amostras
No momento, as amostras contêm todos os binários necessários, portanto, a compilação não é necessária. Se quiser fazer alterações nas amostras de Java ou .NET, você poderá recompilá-las usando msbuild ou o script do PowerShell incluído.

**Para recompilar as amostras**

1. Abra a linha de comando do Hadoop.
2. Execute o comando a seguir:

		powershell -F buildsamples.ps1


##<a name="blobstorage"></a>Conectar-se ao armazenamento de Blob do Azure
O Emulador do HDInsight usa o HDFS como o sistema de arquivos padrão. No entanto, o Azure HDInsight usa o armazenamento de Blob do Azure como o sistema de arquivos padrão. É possível configurar o Emulador do HDInsight para usar o armazenamento de Blob do Azure em vez do armazenamento local. Siga as instruções abaixo para criar um contêiner de armazenamento no Azure e conectá-lo ao Emulador do HDInsight.

>[AZURE.NOTE] Para obter mais informações sobre como o HDInsight usa o armazenamento do Blob do Azure, consulte [Usar o Armazenamento do blob do Azure com o HDInsight][hdinsight-storage].

Antes de começar pelas instruções abaixo, você deve ter criado uma conta de armazenamento. Para obter instruções, consulte [Como criar uma conta de armazenamento][azure-create-storage-account].

**Para criar um contêiner**

1. Entre no [Portal de Gerenciamento][azure-management-portal].
2. Clique em **ARMAZENAMENTO** à esquerda. Você verá uma lista de contas de armazenamento sob a sua assinatura.
3. Clique na conta de armazenamento onde deseja criar o contêiner da lista.
4. Clique em **CONTÊINERES** na parte superior da página.
5. Clique em **ADICIONAR** na parte inferior da página.
6. Insira o **NOME** e selecione **ACESSO**. Você pode usar qualquer um dos três níveis de acesso.  O padrão é **Particular**.
7. Clique em **OK** para salvar as alterações. Você verá o novo contêiner listado no portal.

Antes de poder acessar uma conta de Armazenamento do Azure, você deve adicionar o nome e a chave da conta ao arquivo de configuração.

**Para configurar a conexão a uma conta de armazenamento do Azure**

1. Abra **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\etc\hadoop\core-site.xml** no Bloco de Notas.
2. Adicione a marca <property\> ao lado das outras marcas <property\>:

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Você precisa substituir <StorageAccountName\> e <StorageAccountKey\> pelos valores que correspondem às informações de sua conta de armazenamento.

3. Salve a alteração.  Não é necessário reiniciar os serviços do Hadoop.

Use a sintaxe a seguir para acessar a conta de armazenamento:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Por exemplo:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Execute o PowerShell do HDInsight
Alguns dos cmdlets do PowerShell do Azure HDInsight também recebem suporte no Emulador do HDInsight. Esses cmdlets incluem:

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

Um prompt será exibido quando você chamar Get-Credential. Você deve usar **hadoop** como o nome de usuário. A senha pode ser qualquer cadeia de caracteres. O nome do cluster é sempre **http://localhost:50111**.

Para obter mais informações para o envio de trabalhos do Hadoop, consulte [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs]. Para obter mais informações sobre os cmdlets do PowerShell do HDInsight, consulte [Referência a cmdlets do HDInsight][hdinsight-powershell-reference].


##<a name="remove"></a> Remova o emulador do HDInsight
No computador em que o emulador instalado, abra o Painel de Controle e, em **Programas**, clique em **Desinstalar um programa**. Na lista de programas instalados, clique com o botão direito em **Microsoft HDInsight Emulator para o Azure** e, em seguida, clique em **Desinstalar**. 


##<a name="nextsteps"></a> Próximas etapas
Neste tutorial, você instalará um Emulador do HDInsight e executará alguns trabalhos do Hadoop. Para saber mais, consulte os seguintes artigos:

- [Introdução ao uso do Azure HDInsight][hdinsight-get-started]
- [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]
- [Desenvolver programas MapReduce de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-deploy-streaming]
- [Notas de versão do emulador do HDInsight][hdinsight-emulator-release-notes]
- [Fórum do MSDN para discussão do HDInsight](http://social.msdn.microsoft.com/Forums/en-US/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: ../hdinsight-emulator-release-notes/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-deploy-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-versions]: ../hdinsight-component-versioning/

[Powershell-install-configure]: ../install-configure-powershell/

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png 
<!--HONumber=42-->
