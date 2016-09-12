<properties
   pageTitle="Criar clusters HDInsight com o Repositório Azure Data Lake usando o portal | Azure"
   description="Usar o Portal do Azure para criar e usar clusters HDInsight com o Repositório Azure Data Lake"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/29/2016"
   ms.author="nitinme"/>

# Criar um cluster HDInsight com o Repositório Data Lake usando o Portal do Azure

> [AZURE.SELECTOR]
- [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Usando o PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Aprenda a usar o Portal do Azure para criar um cluster HDInsight (Hadoop, HBase, Spark ou Storm) com acesso ao Repositório Azure Data Lake. Algumas considerações importantes para esta versão:

* **Para clusters Spark (Linux) e Hadoop (Windows e Linux)**, o Repositório Data Lake só pode ser usado como uma conta de armazenamento adicional. A conta de armazenamento padrão para esses clusters ainda será o WASB (Blobs de Armazenamento do Azure).

* **Para clusters Storm (Windows e Linux)**, o Repositório Data Lake pode ser usado para gravar dados de uma topologia do Storm. O Repositório Data Lake também pode ser usado para armazenar dados de referência que podem ser lidos por uma topologia do Storm. Para saber mais, confira [Usar o Repositório Data Lake em uma topologia do Storm](#use-data-lake-store-in-a-storm-topology).

* **Para clusters HBase (Windows e Linux)**, o Data Lake Store pode ser usado como um armazenamento padrão, bem como armazenamento adicional. Para saber mais, confira [Usar Repositório Data Lake com clusters HBase](#use-data-lake-store-with-hbase-clusters).

> [AZURE.NOTE] Alguns pontos importantes a serem considerados.
> 
> * A opção de criar clusters HDInsight com acesso ao Repositório Data Lake está disponível apenas para o HDInsight versões 3.2 e 3.4 (para clusters Hadoop, HBase e Storm no Windows e no Linux). Para clusters Spark no Linux, esta opção só está disponível em clusters HDInsight 3.4.
>
> * Conforme mencionado acima, o Repositório Data Lake está disponível como armazenamento padrão para alguns tipos de cluster (HBase) e como um armazenamento adicional para outros tipos de cluster (Hadoop, Spark, Storm). O uso do Repositório Data Lake como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de leitura/gravação no armazenamento do cluster. Em um cenário no qual o Repositório Data Lake é usado como um armazenamento adicional, os arquivos relacionados ao cluster (como logs etc.) são gravados no armazenamento padrão (Blobs do Azure), enquanto os dados que você deseja processar podem ser armazenados em uma conta do Repositório Data Lake.


## Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a Public Preview do Data Lake Store. Veja [instruções](data-lake-store-get-started-portal.md#signup).
- **Conta do Repositório Azure Data Lake**. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md). Depois de criar a conta, execute as seguintes tarefas para carregar alguns dados de exemplo. Você precisará desses dados posteriormente no tutorial para executar trabalhos de um cluster HDInsight que acessa dados no repositório Data Lake.

	* [Criar uma pasta no seu Repositório Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Carregar um arquivo em seu Repositório Data Lake](data-lake-store-get-started-portal.md#uploaddata). Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## Você aprende mais rapidamente com vídeos?

Assista aos vídeos a seguir para entender como provisionar clusters HDInsight com acesso ao Repositório Data Lake.

* [Criar um cluster HDInsight com acesso ao Repositório Data Lake](https://mix.office.com/watch/l93xri2yhtp2)
* Depois que o cluster for configurado, [Acesse dados no Repositório Data Lake usando scripts do Hive e Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)

## Criar um cluster HDInsight com acesso ao repositório Azure Data Lake

Nesta seção, você cria um cluster HDInsight Hadoop que usa o Repositório Data Lake como um armazenamento adicional. Nesta versão, para um cluster Hadoop, o Repositório Data Lake pode ser usado apenas como um armazenamento adicional para o cluster. O armazenamento padrão ainda será nos blobs de armazenamento do Azure (WASB). Portanto, vamos criar primeiro a conta de armazenamento e os contêineres de armazenamento exigidos para o cluster.

1. Entre no novo [Portal do Azure](https://portal.azure.com).

2. Siga as etapas em [Criar clusters de Hadoop no HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) para começar a provisionar um cluster HDInsight.

3. Na folha **Configuração opcional**, clique em **Fonte de dados**. Na folha **Fonte de dados**, especifique os detalhes para a conta de armazenamento e o contêiner de armazenamento, especifique **Local** como **Leste dos EUA 2**, e, em seguida, clique em **Identidade do AAD do Cluster**.

	![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Adicionar entidade de serviço no cluster HDInsight")

4. Na folha **Identidade do AAD do Cluster**, é possível selecionar uma Entidade de Serviço existente ou criar uma novo.

	* **Criar uma nova entidade de serviço**

		* Na folha **Identidade AAD do Cluster**, clique em **Criar novo**, clique em **Entidade de Serviço** e, em seguida, na folha **Criar uma Entidade de Serviço**, forneça valores para criar uma nova entidade de serviço. Como parte disso, um certificado e um aplicativo do Active Directory do Azure também é criado. Clique em **Criar**.

			![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Adicionar entidade de serviço no cluster HDInsight")

		* Na folha **Identidade AAD do Cluster**, clique em **Gerenciar Acesso de ADLS**. O painel mostra as contas do Data Lake Store associadas à assinatura. No entanto, você pode definir as permissões somente para a conta que você criou. Selecione as permissões READ/WRITE/EXECUTE para a conta que você deseja associar ao cluster HDInsight e clique em **Salvar Permissões**.

			![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Adicionar entidade de serviço no cluster HDInsight")

		* Na folha **Identidade AAD do Cluster**, clique em **Baixar Certificado** para baixar o certificado associado à entidade de serviço que você criou. Isso é útil se você quiser usar a mesma entidade de serviço no futuro, ao criar clusters HDInsight adicionais. Clique em **Selecionar**.

			![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Adicionar entidade de serviço no cluster HDInsight")


	* **Escolha uma Entidade de Serviço existente**

		* Na folha **Identidade AAD do Cluster**, clique em **Usar existente**, clique em **Entidade de Serviço** e, na folha **Selecionar uma Entidade de Serviço**, procure uma entidade de serviço existente. Clique em um nome de entidade de serviço e, em seguida, clique em **Selecionar**.

			![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Adicionar entidade de serviço no cluster HDInsight")

		* Na folha **Identidade AAD do Cluster**, carregue o certificado (.pfx) associado à entidade de serviço selecionada e forneça a senha do certificado.

5. Clique em **Gerenciar Acesso de ADLS** e em **Selecione as permissões de arquivo**.

	![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "Adicionar entidade de serviço no cluster HDInsight")

6. Na folha **Selecione as permissões de arquivo**, na lista suspensa **Conta**, escolha a conta do Data Lake Store que você quer associada ao cluster HDInsight. A folha lista os arquivos e as pastas disponíveis na conta selecionada do Data Lake Store.
 
	![Fornecer acesso ao Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-1.png "Fornecer acesso ao Data Lake Store")

	Depois disso, determine as permissões a serem fornecidas para os arquivos e as pastas selecionados. Para pastas, especifique também se as permissões se aplicam somente à pasta ou à pasta e a todos os itens filho na pasta. Você pode fazer essa seleção escolhendo o valor apropriado na lista suspensa **Aplica-se a**. Para remover uma permissão, clique no ícone **Excluir**

	![Fornecer acesso ao Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-2.png "Fornecer acesso ao Data Lake Store")

	Repita essas etapas para arquivos e pastas associados de outras contas do Data Lake Store também. Ao concluir a atribuição de permissões, clique em **Selecionar** na parte inferior da folha.

7. Na folha **Atribuir permissões selecionadas**, revise as permissões que você forneceu e clique em **Executar** para concedê-las.

	![Fornecer acesso ao Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-3.png "Fornecer acesso ao Data Lake Store")

	A coluna de status exibe o progresso. Depois que todas as permissões tiverem sido atribuídas com êxito, clique em **Concluído**.

6. Clique em **Selecionar** nas folhas **Identidade AAD do Cluster** e **Fonte de Dados** e continue com a criação de cluster, conforme descrito em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

7. Depois que o cluster for provisionado, você pode verificar que a Entidade de Serviço está associada com o cluster HDInsight. Para fazer isso, na folha do cluster, clique em **Identidade AAD do Cluster** para ver a Entidade de Serviço associada.

	![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Adicionar entidade de serviço no cluster HDInsight")

## Executar trabalhos de teste no cluster HDInsight para usar o Repositório Data Lake do Azure

Após a configuração de um cluster HDInsight, você poderá executar trabalhos de teste no cluster a fim de testar se o cluster HDInsight pode acessar dados no Repositório Data Lake do Azure. Para fazer isso, vamos executar algumas consultas de hive que visam o Repositório Data Lake.

### Para um cluster do Linux

1. Abra a folha de cluster para o cluster que você acabou de provisionar e, em seguida, clique em **Painel**. Isso abre o Ambari para o cluster do Linux. Ao acessar o Ambari, você receberá uma solicitação para se autenticar no site. Insira o nome da conta e a senha de administrador (o administrador padrão) que você usou ao criar o cluster.

	![Iniciar painel do cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Iniciar painel do cluster")

	Também é possível navegar diretamente até o Ambari indo para https://CLUSTERNAME.azurehdinsight.net em um navegador da Web (em que **CLUSTERNAME** é o nome do cluster HDInsight).

2. Abra o modo de exibição do Hive Selecione o conjunto de quadrados no menu da página (ao lado do link e do botão **Administrador** à direita da página) para listar os modos de exibição disponíveis. Escolha a exibição **Hive**.

	![Escolhendo modos de exibição do Ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Você verá uma página semelhante à seguinte:

	![Imagem da página de modo de exibição do Hive, que contém uma seção de editor de consultas](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. Na seção **Editor de Consultas** da página, cole a seguinte instrução HiveQL na planilha:

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Clique no botão **Executar** na parte inferior do **Editor de Consultas** para iniciar a consulta. Uma seção **Resultados do Processo de Consulta** deve aparecer abaixo do **Editor de Consultas** e exibir informações sobre o trabalho.

6. Depois que a consulta for concluída, a seção **Resultados do Processo de Consulta** exibirá os resultados da operação. A guia **Resultados** deve conter as seguintes informações:

7. Execute a consulta a seguir para verificar se a tabela foi criada.

		SHOW TABLES;

	A guia **Resultados** deve mostrar o seguinte:

		hivesampletable
		vehicles

	**veículos** é a tabela que você criou anteriormente. **hivesampletable** é uma tabela de exemplo disponível em todos os clusters de HDInsight por padrão.

8. Também é possível executar uma consulta para recuperar dados da tabela **veículos**.

		SELECT * FROM vehicles LIMIT 5;

### Para um cluster do Windows

1. Abra a folha de cluster para o cluster que você acabou de provisionar e, em seguida, clique em **Painel**.

	![Iniciar painel do cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Iniciar painel do cluster")

	Quando solicitado, insira as credenciais de administrador para o cluster.

2. Isso abre o Console de consulta do Microsoft Azure HDInsight. Clique em **Editor Hive**.

	![Abrir o editor Hive](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Abrir o editor Hive")

3. No Editor de Hive, digite a seguinte consulta e, em seguida, clique em **Enviar**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	Nesta consulta de Hive, criamos uma tabela de dados armazenados no Repositório Data Lake em `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Esse local tem um arquivo de dados de exemplo que você deve ter carregado anteriormente.

	A tabela **Sessão de trabalho** na parte inferior mostra o status do trabalho sendo alterado de **Inicializando**, para **Executando**, para **Concluído**. Você também pode clicar em **Exibir detalhes** para obter mais informações sobre o trabalho concluído.

	![Criar tabela](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Criar tabela")

4. Execute a consulta a seguir para verificar se a tabela foi criada.

		SHOW TABLES;

	Clique na opção **Exibir Detalhes** correspondente a essa consulta, e a saída deverá mostrar o seguinte:

		hivesampletable
		vehicles

	**veículos** é a tabela que você criou anteriormente. **hivesampletable** é uma tabela de exemplo disponível em todos os clusters de HDInsight por padrão.

5. Também é possível executar uma consulta para recuperar dados da tabela **veículos**.

		SELECT * FROM vehicles LIMIT 5;


## Repositório Data Lake usando comandos do HDFS

Após a configuração do cluster HDInsight para usar o Repositório Data Lake, você poderá usar os comandos do shell HDFS para acessar o armazenamento.

### Para um cluster do Linux

Nesta seção, você acessará o cluster por SSH e executará os comandos HDFS. O Windows não fornece um cliente SSH integrado. É recomendável usar o **PuTTY**, que pode ser baixado de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre o uso de PuTTY, confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Uma vez conectado, use o comando do sistema de arquivos HDFS a seguir para listar os arquivos no Repositório Data Lake.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Isso deve listar o arquivo carregado anteriormente no Repositório Data Lake.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Você também pode usar o comando `hdfs dfs -put` para carregar alguns arquivos no Repositório Data Lake e usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.


### Para um cluster do Windows

1. Entre no novo [Portal do Azure](https://portal.azure.com).

2. Clique em **Procurar**, em **Clusters HDInsight** e clique no cluster HDInsight que você criou.

3. Na folha do cluster, clique em **Área de Trabalho Remota** e na folha **Área de Trabalho Remota**, clique em **Conectar**.

	![Remoto em cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Criar um grupo de recursos do Azure")

	Quando receber a solicitação, insira as credenciais fornecidas para o usuário da área de trabalho remota.

4. Na sessão remota, inicie o Windows PowerShell e use os comandos do sistema de arquivos HDFS para listar os arquivos no Repositório Data Lake do Azure.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Isso deve listar o arquivo carregado anteriormente no Repositório Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	Você também pode usar o comando `hdfs dfs -put` para carregar alguns arquivos no Repositório Data Lake e usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## Usar o Repositório Data Lake com o cluster Spark

Nesta seção, você usar o notebook Jupyter disponível com clusters Spark HDInsight para executar um trabalho de leitura de dados de uma conta do Repositório Data Lake associada a um cluster Spark HDInsight, em vez da conta de Armazenamento de Blob do Azure padrão.

1. Copie alguns dados de exemplo da conta de armazenamento padrão (WASB) associada ao cluster Spark para a conta do Repositório Data Lake do Azure associada ao cluster. Você pode usar a [ferramenta ADLCopy](http://aka.ms/downloadadlcopy) para fazer isso. Baixe e instale a ferramenta a partir do link.

2. Abra um prompt de comando e navegue até o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

3. Execute o seguinte comando para copiar um blob específico do contêiner de origem para um Repositório Data Lake:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Para este tutorial, copie o arquivo de exemplo de dados **HVAC.csv** em **/HdiSamples/HdiSamples/SensorSampleData/hvac/** para a conta do Azure Data Lake Store. O trecho de código deve parecer com:

		AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	>[AZURE.WARNING] Verifique se os nomes de arquivo e caminho estão com a capitalização apropriada.

4. Você precisará inserir as credenciais da assinatura do Azure vinculadas à conta do Data Lake Store. Você verá uma saída semelhante ao seguinte:

		Initializing Copy.
		Copy Started.
		100% data copied.
		Copy Completed. 1 file copied.

	O arquivo de dados (**HVAC.csv**) será copiado em uma pasta **/hvac** na conta do Data Lake Store.

4. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.

2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha **Painel do Cluster**, clique em **Notebook do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE] Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações. Clique em **Novo** e em **PySpark**.

	![Criar um novo bloco de anotações do Jupyter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações do Jupyter")

3. Um novo notebook é criado e aberto com o nome **Untitled.pynb**.

4. Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive serão criados automaticamente para você ao executar a primeira célula do código. Você pode começar importando os tipos obrigatórios para este cenário. Para fazer isso, cole o trecho de código a seguir em uma célula vazia e pressione **SHIFT + ENTER**.

		from pyspark.sql.types import *
		
	Toda vez que você executar um trabalho no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito. Depois que o trabalho for concluído, isso será alterado para um círculo vazio.

	 ![Status de um trabalho do bloco de anotações do Jupyter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.jupyter.job.status.png "Status de um trabalho do bloco de anotações do Jupyter")

4. Carregue os dados de exemplo em uma tabela temporária usando o arquivo **HVAC.csv** copiado para a conta do Data Lake Store. Você pode acessar os dados na conta do Repositório Data Lake usando o seguinte padrão de URL.

		adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

	Em uma célula vazia, cole o seguinte exemplo de código, substitua **MYDATALAKESTORE** pelo nome de sua conta do Data Lake Store e pressione **SHIFT + ENTER**. Esse exemplo de código registra os dados em uma tabela temporária chamada **hvac**.

		# Load the data
		hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. Como está usando um kernel PySpark, agora você pode executar diretamente uma consulta SQL na tabela temporária **hvac** que acabou de criar usando a mágica de `%%sql`. Para obter mais informações sobre a mágica do `%%sql`, bem como outras mágicas disponíveis com o kernel PySpark, confira [Kernels disponíveis para notebooks Jupyter com clusters do Spark no HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"

5. Depois que o trabalho for concluído com êxito, a saída tabular a seguir será exibida por padrão.

 	![Saída de tabela do resultado da consulta](./media/data-lake-store-hdinsight-hadoop-use-portal/tabular.output.png "Saída de tabela do resultado da consulta")

	Você também pode ver os resultados em outras visualizações. Por exemplo, um gráfico de área para a mesma saída seria semelhante ao seguinte.

	![Gráfico de área de resultado da consulta](./media/data-lake-store-hdinsight-hadoop-use-portal/area.output.png "Gráfico de área de resultado da consulta")


6. Depois de concluir a execução do aplicativo, você deve encerrar o notebook para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**. Isso desligará e fechará o bloco de anotações.

## Usar o Repositório Data Lake em uma topologia do Storm

Você pode usar o Repositório Data Lake para gravar os dados de uma topologia do Storm. Para obter instruções sobre como reproduzir esse cenário, confira [Usar o Azure Data Lake Store com o Apache Storm com HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## Usar Repositório Data Lake com clusters HBase

Com clusters HBase, você pode usar o Repositório Data Lake como um armazenamento padrão, bem como um armazenamento adicional. Para fazer isso:

1.  Na folha **Fonte de Dados**, para **Local dos Dados do HBase**, escolha **Data Lake Store**.
2.  Escolha o nome do Repositório Data Lake que deseja usar ou crie um novo.
3.  Por fim, especifique a **Pasta Raiz do HBase** no Data Lake Store. Se a conta do Repositório Data Lake não tiver uma pasta raiz, crie uma.

	![HBase com Repositório Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Criar um grupo de recursos do Azure")

### Considerações ao usar o Repositório Data Lake como armazenamento padrão para clusters HBase

* Você pode usar a mesma conta do Repositório Data Lake para obter mais de um cluster HBase. No entanto, a **Pasta Raiz do HBase** fornecida para o cluster (etapa nº 4 na captura de tela acima) deve ser exclusiva. Você **não deve** usar a mesma pasta raiz em dois clusters HBase diferentes.
* Mesmo que você use a conta do Repositório Data Lake como armazenamento padrão, os arquivos de log do cluster HBase ainda são armazenados no WASB (Blobs de Armazenamento do Azure) associado ao cluster. Isso é destacado na caixa azul na captura de tela cima.



## Confira também

* [PowerShell: Criar um cluster HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0831_2016-->