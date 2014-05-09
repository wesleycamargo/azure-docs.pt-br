<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="SDK do HDInsight" pageTitle="Como usar as bibliotecas do .NET no HDInsight | Azure" metaKeywords="" description="Saiba como obter os pacotes NuGet do HDInsight NuGet e usá-los em seu aplicativo .NET." metaCanonical="" services="hdinsight" documentationCenter="" title="Usar o SDK do .NET Hadoop com o HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />





#Usar o SDK do .NET do Hadoop com o HDInsight#

O SDK do .NET Hadoop do HDInsight fornece bibliotecas de cliente do .NET que facilitam o trabalho com o Hadoop no .NET. Neste tutorial, você aprenderá como obter o SDK do .NET do Hadoop e usá-lo para criar um aplicativo simples baseado em .NET que executa consultas usando o Serviço Azure HDInsight. Com um determinado arquivo actors.txt, você irá escrever um aplicativo para localizar a ator/atriz que recebe mais prêmios. 

Para habilitar o HDInsight, clique [aqui](https://account.windowsazure.com/PreviewFeatures).

## Neste artigo

* [Baixe e instale o SDK do .NET do Hadoop](#install)
* [Preparar-se para o tutorial](#prepare)
* [Criar o aplicativo](#create)
* [Executar o aplicativo](#run)
* [Próximas etapas](#nextsteps)

##<a id="install"></a> Baixar e instalar o SDK do .NET do Hadoop##

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). O SDK inclui os seguintes componentes:

* **Biblioteca MapReduce** - simplifica a criação de trabalhos MapReduce em linguagens .NET usando a interface de streaming do Hadoop.

* **LINQ para a biblioteca de cliente Hive** - converte consultas LINQ de C# ou F# em consultas e as executa no cluster Hadoop. Essa biblioteca também pode executar consultas HiveQL arbitrárias em um aplicativo .NET.

* **Biblioteca WebClient** - contém bibliotecas de cliente para *WebHDFS* e *WebHCat*.

	* **Biblioteca de cliente WebHDFS** - funciona com arquivos no HDFS e no Armazenamento de Blog do Azure.

	* **Biblioteca de cliente WebHCat** - gerencia a programação e a execução de trabalhos em cluster HDInsight.
	
A sintaxe do NuGet para instalar as bibliotecas:
	
	install-package Microsoft.Hadoop.MapReduce
	install-package Microsoft.Hadoop.Hive 
	install-package Microsoft.Hadoop.WebClient 
			
Esses comandos adicionam as bibliotecas e referências ao projeto atual do Visual Studio.

##<a id="prepare"></a> Preparar-se para o tutorial

Você deve ter uma [assinatura do Azure][free-trial]e uma [Conta de Armazenamento do Azure][create-storage-account] para poder continuar. Você também deve conhecer o nome e a chave da conta de Armazenamento do Azure. Para obter instruções sobre como obter essas informações, consulte a seção *Como exibir, copiar e regenerar chaves de acesso de armazenamento* de [Como gerenciar contas de armazenamento](/pt-br/manage/services/storage/how-to-manage-a-storage-account/).


Você também deve baixar o arquivo de Actors.txt usado neste tutorial. Execute as seguintes etapas para baixar esse arquivo em seu ambiente de desenvolvimento:

1. Crie uma pasta C:\Tutorials no computador local.

2. Baixe o [Actors.txt](http://www.microsoft.com/pt-br/download/details.aspx?id=37003) e salve o arquivo na pasta C:\Tutorials.

##<a id="create"></a>Criar o aplicativo

Nesta seção, você aprenderá como carregar arquivos no cluster Hadoop de maneira programática e como executar trabalhos Hive usando o LINQ para Hive.

1. Abra o Visual Studio 2012.

2. No menu Arquivo, clique em **Novo** e, em seguida, em **Projeto**.

3. No Novo Projeto, digite ou selecione os seguintes valores:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriedade</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modelos/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modelo</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicativo de console</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SimpleHiveJob</td></tr>
	</table>

4. Clique em **OK** para criar o projeto.


5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.

6. Execute os seguintes comandos no console para instalar os pacotes.

		install-package Microsoft.Hadoop.Hive 
		install-package Microsoft.Hadoop.WebClient 

	Esses comandos adicionam bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo.

8. Adicione as seguintes instruções using na parte superior do arquivo :

		using Microsoft.Hadoop.WebHDFS.Adapters;
		using Microsoft.Hadoop.WebHDFS;
		using Microsoft.Hadoop.Hive;
	
9. Na função Main(), copie e cole o código a seguir:
		
		// Upload actors.txt to Blob Storage
		var asvAccount = [Storage-account-name.blob.core.windows.net];
		var asvKey = [Storage account key];
		var asvContainer = [Container name];
		var localFile = "C:/Tutorials/Actors.txt";
		var hadoopUser = [Hadoop user name]; // The HDInsight cluster user
		var hadoopUserPassword = [Hadoop user password]; // The HDInsight cluster user password
		var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
		
		var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
		var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
		
		Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
		HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
		HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
		
		// Create Hive connection
		var hiveConnection = new HiveConnection(
		  new System.Uri(clusterURI),
		  hadoopUser, 
		  hadoopUserPassword,
		  asvAccount, 
		  asvKey);
		
		// Drop any existing tables called Actors
		// Only needed if you wish to rerun this application
		// and drop a previous Actors table.
		//hiveConnection.GetTable<HiveRow>("Actors").Drop();

		Console.WriteLine("Creating a Hive table named 'Actors'...");
		string command =
		  @"CREATE TABLE Actors(
		            MovieId string, 
		            ActorId string,
		            Name string, 
		            AwardsCount int) 
		            row format delimited fields 
		        terminated by ',';";
		hiveConnection.ExecuteHiveQuery(command).Wait();
		
		Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
		command =
		  @"LOAD DATA INPATH 
		        '/user/hadoop/MovieData/Actors.txt'
		    OVERWRITE INTO TABLE Actors;";
		hiveConnection.ExecuteHiveQuery(command).Wait();
		
		Console.WriteLine("Performing Hive query...");
		var result = hiveConnection.ExecuteQuery(@"select name, awardscount
		          from actors sort by awardscount desc
		          limit 1");
		result.Wait();

		Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
        Console.WriteLine("\nPress any key to continue.");
        Console.ReadKey();

10. Atualize as constantes no aplicativo. O Serviço Azure HDInsight usa o armazenamento de Blob do Azure como o sistema de arquivos padrão. Durante o processo de provisionamento do HDInsight, um blob é designado como o sistema de arquivos padrão. Você tem opções para usar o contêiner do sistema de arquivos padrão ou um contêiner em um armazenamento de Blob diferente. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](/pt-br/manage/services/hdinsight/howto-blob-store/).

	Se você optar por usar o contêiner do sistema de arquivos padrão, poderá localizar o nome da conta de armazenamento, a chave do armazenamento e o nome do contêiner no arquivo de configuração *c:\apps\dist\hadoop-1.1.0-SNAPSHOT\conf>core-site.xml* por meio de comunicação remota com o cluster. O contêiner utilizado como o sistema de arquivos padrão pode ser localizado pela pesquisa de *fs.default.name*. O nome da conta de armazenamento e a chave da conta podem ser localizados pesquisando *fs.azure.account.key*.
	
##<a id="run"></a>Executar o aplicativo

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Deve ser aberta uma janela de console que exibe as etapas executadas pelo aplicativo à medida que os dados são carregados, armazenados em uma tabela Hive e finalmente consultados. Quando o aplicativo for concluído e os resultados da consulta forem retornados, pressione qualquer tecla para encerrá-lo.

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "Console Application")

<div class="dev-callout">
<strong>Observação</strong>
<p>Cada etapa realizada pelo aplicativo pode demorar segundos ou até mesmo minutos para ser concluída. O tempo de carregamento do arquivo Actors.txt irá variar com base em sua conexão de Internet com o data center do Azure, enquanto as outras etapas são dependentes do tamanho do cluster.</p>
</div>

<div class="dev-callout">
<strong>Observação</strong>
<p>A operação LOAD DATA INPATH é uma operação de mudança que move os dados do Actors.txt para o namespace do sistema de arquivos controlado pelo Hive. Isso remove efetivamente o arquivo Actors.txt do local de carregamento. Por esse motivo, o arquivo Actors.txt deve ser carregado cada vez que você executa o aplicativo.</p>
<p>Para obter mais informações sobre como carregar dados no Hive, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations">Introdução ao Hive (a página pode estar em inglês)</a>.</p>
</div>

##<a id="nextsteps"></a>Próximas etapas
Agora você sabe como criar um aplicativo .NET usando o SDK do .NET do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](/pt-br/manage/services/hdinsight/get-started-hdinsight/)
* [Usar o Pig com o HDInsight][hdinsight-pig] 
* [Usar o MapReduce com HDInsight][hdinsight-mapreduce]
* [Usar o Hive com o HDInsight](/pt-br/manage/services/hdinsight/using-hive-with-hdinsight/)

[hdinsight-pig]: /pt-br/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-mapreduce]: /pt-br/manage/services/hdinsight/using-mapreduce-with-hdinsight/



[free-trial]: http://www.windowsazure.com/pt-br/pricing/free-trial/
[create-storage-account]: http://www.windowsazure.com/pt-br/manage/services/storage/how-to-create-a-storage-account/



