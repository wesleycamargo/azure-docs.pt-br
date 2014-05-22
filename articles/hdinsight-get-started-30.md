<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Introdução" pageTitle="Introdução ao uso de clusters Hadoop 2.2 com o HDInsight | Azure" metaKeywords="" description="Introdução aos clusters Hadoop 2.2 com o HDInsight, uma solução de big data. Saiba como configurar clusters, executar trabalhos MapReduce e gravar a saída de dados no Excel para análise." metaCanonical="" services="hdinsight" documentationCenter="" title="Introdução ao uso do Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />




# Introdução ao uso de clusters Hadoop 2.2 com o HDInsight 

O HDInsight torna o [Apache Hadoop][apache-hadoop] disponível como um serviço na nuvem. Disponibiliza a estrutura do software MapReduce em um ambiente mais simples, mais escalonável e econômico do Azure. O HDInsight também fornece uma abordagem econômica para o gerenciamento e o armazenamento de dados usando o armazenamento de Blob do Azure. 

Neste tutorial, você irá provisionar um cluster HDInsight usando o Portal de Gerenciamento do Azure, enviar um trabalho MapReduce do Hadoop usando o PowerShell e, em seguida, importar os dados de saída do trabalho MapReduce para o Excel para análise.

> [WACOM.NOTE] Este tutorial aborda o uso de clusters Hadoop 2.2 no HDInsight. Para obter o tutorial sobre como usar clusters Hadoop 1.2 no HDInsight, consulte [Introdução ao Azure HDInsight][hdinsight-get-started]

> [WACOM.NOTE]	A sintaxe *asv://* não é suportada em clusters HDInsight versão 3.0 e não terá suporte em versões posteriores. Em vez disso, a sintaxe *wasb://* deve ser usada. 

Em conjunto com a disponibilidade geral do Azure HDInsight, a Microsoft também lançou o Emulador do HDInsight para Azure, anteriormente conhecido como Microsoft HDInsight Developer Preview. Esse produto tem como alvo os cenários de desenvolvedor e, como tal, só oferece suporte a implantações de nó único. Para usar o emulador de HDInsight, consulte [Introdução ao emulador de HDInsight][hdinsight-emulator].

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:


- Uma assinatura do Azure. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].
- Um computador que esteja executando o Windows 8, Windows 7, Windows Server 2012 ou Windows Server 2008 R2. Este computador será usado para enviar trabalhos MapReduce.
- Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

**Tempo estimado para concluir:** 30 minutos

##Neste tutorial

* [Configurar o ambiente local para a execução do PowerShell](#setup)
* [Provisione um cluster HDInsight](#provision)
* [Executar um programa WordCount MapReduce](#sample)
* [Conectar-se as ferramentas do Microsoft Business Intelligence](#powerquery)
* [Próximas etapas](#nextsteps)



##<a id="setup"></a> Configurar o ambiente local para a execução do PowerShell

Há várias maneiras de enviar trabalhos MapReduce para HDInsight. Neste tutorial, você usará o PowerShell do Azure. Para instalar o PowerShell do Azure, execute o [Microsoft Web Platform Installer][powershell-download]. Clique em **Executar** quando solicitado, em **Instalar** e siga as instruções. Para obter mais informações, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Os cmdlets do PowerShell exigem informações da sua assinatura para que possam ser usados para gerenciar seus serviços.

**Para conectar-se à sua assinatura usando o AD do Azure**

1. Abra o console do PowerShell do Azure, conforme instruído em [Como instalar o PowerShell do Azure][powershell-open].
2. Execute o seguinte comando:

		Add-AzureAccount

3. Na janela, digite o endereço de email e a senha associada à sua conta. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

O outro método para se conectar a sua assinatura é usando o método de certificado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
	
##<a name="provision"></a>Provisione um cluster HDInsight

O processo de provisionamento do HDInsight exige que uma conta de Armazenamento do Azure seja usada como o sistema de arquivos padrão. A conta de armazenamento deve estar localizada no mesmo datacenter que os recursos de computação do HDInsight. No momento, você só poderá provisionar clusters do HDInsight nos seguintes datacenters:

- Sudeste Asiático
- Norte da Europa
- Europa Ocidental
- Leste dos EUA
- Oeste dos EUA

Você deve escolher um dos cinco data centers para sua conta de Armazenamento do Azure.

**Para criar uma conta de Armazenamento do Azure**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.

	![HDI.StorageAccount.QuickCreate][image-hdi-storageaccount-quickcreate]

3. Digite **URL**, **LOCAL** e **REPLICAÇÃO** e, em seguida, clique em **CRIAR CONTA DE ARMAZENAMENTO**. Não há suporte para grupos de afinidade. Você verá a nova conta de armazenamento na lista de armazenamento. 
4. Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.
5. Clique na nova conta de armazenamento da lista para selecioná-la.
6. Clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
7. Anote o **NOME DA CONTA DE ARMAZENAMENTO** e a **CHAVE DE ACESSO PRIMÁRIA**.  Você precisará deles mais tarde no tutorial.


Para obter instruções, consulte
[Como criar uma conta de Armazenamento][azure-create-storageaccount] e [Usar o Armazenamento de Blob com o HDInsight][hdinsight-storage].

















Atualmente, o provisionamento de clusters HDInsight 3.0 tem suporte apenas ao usar a opção de criação personalizada.

**Para provisionar um cluster do HDInsight** 

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal]. 

2. Clique em **HDINSIGHT** à esquerda para listar os clusters HDInsight da sua conta. Na seguinte captura de tela, não há nenhum cluster HDInsight existente.

	![HDI.ClusterStatus][image-hdi-clusterstatus]

3. Clique em **NOVO** no canto inferior esquerdo, clique em **SERVIÇOS DE DADOS**, em **HDINSIGHT** e, em seguida, em **CRIAÇÃO RÁPIDA**.

	![HDI.CustomCreateCluster][image-hdi-customcreatecluster]

4. Na guia Detalhes do Cluster, digite ou selecione os valores a seguir:

	<table border="1">
	<tr><th>Nome</th><th>Valor</th></tr>
	<tr><td><strong>Nome do Cluster</strong></td><td>Nome do cluster.</td></tr>
	<tr><td><strong>Nós de Dados</strong></td><td>Número de nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único. <br />O limite de tamanho do cluster varia para as assinaturas do Azure. Entre em contato com o suporte de cobrança do Azure para aumentar o limite.</td></tr>
	<tr><td><strong>Versão do HDInsight</strong></td><td>Escolha <strong>3.0</strong> para criar um cluster Hadoop 2.2 no HDInsight.</td></tr>
	<tr><td><strong>Região</strong></td><td>escolha a mesma região da conta de armazenamento que você criou no último procedimento. O HDInsight requer que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região especificada aqui.
	</td></tr>
	</table>

4. Clique na seta para a direita no canto inferior direito para configurar o usuário do cluster. 
4. Na guia Configurar Usuário de Cluster, digite o **Nome de Usuário** e a **Senha** para a conta do usuário do cluster HDInsight. Além dessa conta, você pode criar uma conta de usuário RDP depois que o cluster for configurado, para que você possa usar a área de trabalho remota no cluster. Para obter instruções, consulte [Administrar o HDInsight usando o Portal de Gerenciamento][hdinsight-admin-portal]
4. Clique na seta para a direita no canto inferior direito para configurar a conta de armazenamento. 
5. Na guia Conta de Armazenamento, digite ou selecione os valores a seguir:

	<table border="1">
	<tr><th>Nome</th><th>Valor</th></tr>
	<tr><td>CONTA DE ARMAZENAMENTO</td><td>Selecione <strong>Usar Armazenamento Existente</strong>. Você também tem a opção de fazer com que o Portal de Gerenciamento crie uma nova conta de armazenamento se ainda não tiver criado uma.</td></tr>
	<tr><td>NOME DA CONTA</td><td>Especifique a conta de armazenamento criada no último procedimento deste tutorial. Observe que somente as contas de armazenamento na mesma região são exibidas na caixa de listagem.</td></tr>
	<tr><td>CONTÊINER PADRÃO</td><td>Selecione <strong>Criar contêiner de padrão</strong>. Quando essas opções forem escolhidas, o nome do contêiner padrão será igual ao nome do cluster.</td></tr>
	<tr><td>CONTAS DE ARMAZENAMENTO ADICIONAIS</td><td>Selecione <strong>0</strong>. Você tem a opção de conectar o cluster a até sete contas de armazenamento adicionais.</td></tr>
	</table>

5. Clique no ícone de seleção no canto inferior direito para criar o cluster. Quando o processo de provisionamento for concluído, a coluna de status mostrará **Em Execução**.








##<a name="sample"></a>Executar um trabalho WordCount MapReduce

Agora você tem um cluster de HDInsight provisionado. A próxima etapa é executar um trabalho MapReduce para contar palavras em um arquivo de texto. 

Executar um trabalho MapReduce requer os seguintes elementos:

* Um programa MapReduce. Neste tutorial, você usará o exemplo WordCount fornecido com a distribuição de cluster HDInsight para que você não precise escrever seus próprios. Ele está localizado em */example/jars/hadoop-mapreduce-examples.jar*. Para obter instruções sobre como escrever o seu próprio trabalho MapReduce, consulte [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-MapReduce].

* Um arquivo de entrada. Você usará */example/data/gutenberg/davinci.txt* como o arquivo de entrada. Para obter informações sobre como carregar arquivos, consulte [Carregar dados para HDInsight][hdinsight-upload-data].
* Uma pasta de arquivo de saída. Você usará */example/data/WordCountOutput* como a pasta do arquivo de saída. O sistema criará a pasta se ela não existir.

O esquema de URI para acessar arquivos no armazenamento de Blob é:

	wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] Por padrão, o contêiner de Blob usado para o sistema de arquivos padrão tem o mesmo nome que o cluster HDInsight.

O esquema de URI fornece acesso sem criptografia com o prefixo *wasb:* e acesso criptografado SSL com wasbs. É recomendável usar wasbs sempre que possível, mesmo ao acessar dados que residem dentro do mesmo datacenter do Azure.

Como HDInsight usa um contêiner de armazenamento de Blob como o sistema de arquivos padrão, você pode se referir aos arquivos e diretórios dentro do sistema de arquivos padrão usando caminhos relativos ou absolutos.

Por exemplo, para acessar o hadoop-mapreduce-examples.jar, você pode usar uma das seguintes opções:

	● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	● wasb:///example/jars/hadoop-mapreduce-examples.jar
	● /example/jars/hadoop-mapreduce-examples.jar
				
O uso do prefixo *wasb: / /* nos caminhos desses arquivos. Isso é necessário para indicar que o armazenamento de Blob do Azure está sendo usado para os arquivos de entrada e saída. Um diretório de saída assume um caminho padrão relativo para a pasta *wasb:///user/&lt;username&gt;*. 

Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].





















**Para executar o exemplo WordCount**

1. Abra o **PowerShell do Azure**. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

3. Execute os comandos a seguir para definir as variáveis.  
		
		$subscriptionName = "<SubscriptionName>" 
		$clusterName = "<HDInsightClusterName>"        
		
5. Execute os seguintes comandos para criar uma definição do trabalho MapReduce:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	O arquivo hadoop-mapreduce-examples.jar é fornecido com a distribuição do cluster HDInsight. Existem dois argumentos para o trabalho de MapReduce. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. O arquivo de origem é fornecido com a distribuição de cluster HDInsight e o caminho do arquivo de saída será criado no tempo de execução.

6. Execute o comando a seguir para enviar o trabalho MapReduce:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
		
	Além da definição de trabalho MapReduce, você também deve fornecer o nome do cluster HDInsight onde você deseja executar o trabalho de MapReduce. 

	O *Start-AzureHDInsightJob* é uma chamada assincronizada.  Para verificar a conclusão do trabalho, use o cmdlet *AzureHDInsightJob de espera*.

6. Execute o seguinte comando para verificar a conclusão do trabalho MapReduce:

		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
		
8. Execute o seguinte comando para verificar qualquer erro na execução do trabalho MapReduce:	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError
		
	A captura de tela a seguir mostra a saída de uma execução bem sucedida. Caso contrário, você verá algumas mensagens de erro.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]
































**Para recuperar os resultados do trabalho de MapReduce**

1. Abra o **PowerShell do Azure**.
2. Execute os seguintes comandos para criar uma pasta C:\Tutorials e alterar o diretório para a pasta:

		mkdir \Tutorials
		cd \Tutorials
	
	O diretório do PowerShell do Azure é *C:\Windows\System32\WindowsPowerShell\v1.0*. Por padrão, você não tem permissão de gravação nessa pasta. Você deve alterar o diretório para uma pasta onde você tem permissão de gravação.
	
2. Defina as três variáveis nos comandos a seguir e, em seguida, execute-as:

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"   
		$containerName = "<ContainerName>"			   

	A Conta de Armazenamento do Azure é aquela que você criou anteriormente no tutorial. A conta de armazenamento é usada para hospedar o contêiner de Blob usado como o sistema de arquivos de cluster HDInsight padrão.  Geralmente, o nome do contâiner de armazenamento de Blob compartilham o mesmo nome que o cluster HDInsight a menos que você especifique um nome diferente ao configurar o cluster.

3. Execute os seguintes comandos para criar um objeto de contexto de armazenamento do Azure:
		
		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	O *Select-AzureSubscription* é usado para definir a assinatura atual caso você tenha várias assinaturas e a assinatura padrão não é aquela para ser usada. 

4. Execute o comando a seguir para baixar a saída do trabalho MapReduce do contêiner de Blob na estação de trabalho:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	A pasta *example/data/WordCountOutput* é a pasta de saída especificada quando você executa o trabalho de MapReduce. *part-r-00000* é o nome de arquivo padrão para a saída do trabalho de MapReduce.  O arquivo será baixado na mesma estrutura de pasta na pasta local. Por exemplo, na seguinte captura de tela, a pasta atual é a pasta raiz de C. O arquivo será baixado para a pasta *C:\example\data\WordCountOutput&#92;*.

5. Execute o comando a seguir para imprimir a saída do trabalho MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	O trabalho de MapReduce produz um arquivo chamado *part-r-00000* com as palavras e as contagens.  O script usa o comando findstr para listar todas as palavras que contém *"there"*.


> [WACOM.NOTE] Se você abrir o <i>./example/data/WordCountOutput/part-r-00000</i>, uma saída de várias linhas de um trabalho MapReduce, no Bloco de Notas, você observará que as quebras de linha não são renderizadas corretamente. Isso é esperado.


	
##<a name="powerquery"></a>Conectando as ferramentas do Microsoft Business Intelligence 

O suplemento Power Query para Excel pode ser usado para exportar a saída do HDInsight no Excel onde as ferramentas do Microsoft Business Intelligence (BI) podem ser usadas para processar ainda mais ou exibir os resultados. Ao criar um cluster de HDInsight, um contêiner padrão com o mesmo nome de cluster foi criado na conta do armazenamento associada a ele, quando ele foi criado. É preenchido automaticamente com um conjunto de arquivos. Um desses arquivos é um exemplo da tabela de Hive. Nesta seção, mostraremos como importar os dados contidos nessa tabela para o Excel para visualização e processamento adicional.

Você deve ter o Excel 2010 ou 2013 instalado para concluir essa parte do tutorial. Aqui iremos importar a tabela Hive padrão enviada no HDInsight.

**Para baixar o Microsoft PowerQuery para Excel**

- Baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft](http://www.microsoft.com/pt-br/download/details.aspx?id=39379) e instale-o.

**Para importar dados no HDInsight**

1. Abra o Excel e crie uma nova planilha em branco.
3. Clique no menu **Power Query**, clique em **De Outras Fontes** e, em seguida, em **Do Azure HDInsight**.

	![HDI.GettingStarted.PowerQuery.ImportData][image-hdi-gettingstarted-powerquery-importdata]

3. Digite o **Nome da Conta** da Conta de Armazenamento de Blob do Azure associada ao cluster e, em seguida, clique em **OK**. Esta é a conta de armazenamento criada anteriormente no tutorial.
4. Digite a **Chave de Conta** da Conta de Armazenamento de Blob do Azure e clique em **Salvar**. 
5. À direita, no painel do Navegador, clique duas vezes no nome do contêiner do Armazenamento de Blob. Por padrão, o nome do contêiner é igual ao nome do cluster. 

6. Localize **part-r-00000** na coluna **Nome** (o caminho é *.../example/data/WordCountOutput*) e, em seguida, clique em **Binário** à esquerda de **part-r-00000**.

	![HDI.GettingStarted.PowerQuery.ImportData2][image-hdi-gettingstarted-powerquery-importdata2]

8. Clique com o botão direito do mouse em **Column1.1** e, em seguida, selecione **Renomear**.
9. Altere o nome para **Word**.
10. Repita o processo para renomear **Column1.2** para **Contagem**.

	![HDI.GettingStarted.PowerQuery.ImportData3][image-hdi-gettingstarted-powerquery-importdata3]

9. Clique em **Aplicar e Fechar** no canto superior esquerdo. A consulta importa a tabela Hive para o Excel.

##<a name="nextsteps"></a>Próximas etapas
Neste tutorial, você aprendeu a provisionar um cluster com o HDInsight, executar um trabalho de MapReduce e importar os resultados para o Excel, onde eles podem ser processados ainda mais e exibidos graficamente usando ferramentas de BI. Para saber mais, consulte os seguintes artigos:

- [Introdução ao HDInsight][hdinsight-get-started]
- [Introdução ao emulador do HDInsight][hdinsight-emulator]
- [Usar o armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]
- [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Use o hive com o HDInsight][hdinsight-hive]
- [Use o Pig com o HDInsight][hdinsight-pig]
- [Usar o Oozie com o HDInsight][hdinsight-oozie]
- [Desenvolver programas MapReduce de streaming Hadoop em C# para HDInsight][hdinsight-develop-streaming]
- [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]


[hdinsight-get-started]: /pt-br/documentation/articles/hdinsight-get-started/
[hdinsight-get-started-3.0]: /pt-br/documentation/articles/hdinsight-get-started-30/
[hdinsight-provision]: /pt-br/documentation/articles/hdinsight-provision-clusters/
[hdinsight-admin-powershell]: /pt-br/documentation/articles/hdinsight-administer-use-powershell/
[hdinsight-upload-data]: /pt-br/documentation/articles/hdinsight-upload-data/
[hdinsight-mapreduce]: /pt-br/documentation/articles/hdinsight-use-mapreduce
[hdinsight-hive]: /pt-br/documentation/articles/hdinsight-use-hive/
[hdinsight-pig]: /pt-br/documentation/articles/hdinsight-use-pig/
[hdinsight-oozie]: /pt-br/documentation/articles/hdinsight-use-oozie/
[hdinsight-storage]: /pt-br/documentation/articles/hdinsight-use-blob-storage/
[hdinsight-emulator]: /pt-br/documentation/articles/hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: /pt-br/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: /pt-br/documentation/articles/hdinsight-develop-deploy-java-mapreduce/
[hdinsight-admin-portal]: /pt-br/documentation/articles/hdinsight-administer-use-management-portal/
[hdinsight-develop-streaming]: /pt-br/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/

[azure-purchase-options]: https://www.windowsazure.com/pt-br/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/pt-br/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/pt-br/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: /pt-br/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: /pt-br/documentation/articles/install-configure-powershell/
[powershell-open]: /pt-br/documentation/articles/install-configure-powershell/#install

[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started-3.0/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started-3.0/HDI.ClusterStatus.png
[image-hdi-customcreatecluster]: ./media/hdinsight-get-started-3.0/HDI.CustomCreateCluster.png
[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started-3.0/HDI.WordCountDiagram.gif
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.MRJobOutput.png
[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData2.png
[image-hdi-gettingstarted-powerquery-importdata3]: ./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData3.png


