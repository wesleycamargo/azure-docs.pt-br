<properties 
	pageTitle="Usar blocos de anotações do Zeppelin com cluster Spark no HDInsight Linux | Azure" 
	description="Instruções passo a passo sobre como usar blocos de anotações do Zeppelin com clusters Spark no HDInsight Linux." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Usar blocos de anotações do Zeppelin com cluster Spark no HDInsight (Linux)

Saiba como instalar blocos de anotações do Zeppelin em clusters Spark e como usar esses blocos de anotações.

> [AZURE.IMPORTANT] O bloco de anotações do Zeppelin para o cluster HDInsight Spark é uma oferta para demonstrar como usar o Zeppelin em um ambiente do Azure HDInsight Spark. Se você quiser usar blocos de anotações para trabalhar com o HDInsight Spark, recomendamos que você use os blocos de anotações do Jupyter. Os blocos de anotações do Jupyter também oferecem opções diferentes de kernel, como o Scala, e continuarão a ter melhorias de recursos. Para obter instruções sobre como usar os blocos de anotações do Jupyter com o HDInsight Spark, veja [Executar consultas SQL do Spark usando um bloco de anotações do Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md#jupyter).

**Pré-requisitos:**

* Antes de começar este tutorial, você deve ter uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark. Para obter instruções, confira [Create Apache Spark clusters in Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
* Um cliente SSH. Para distribuições Linux e Unix ou o Macintosh OS X, o comando `ssh` é fornecido com o sistema operacional. Para sistemas Windows, é recomendável [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

	> [AZURE.NOTE] Se você quiser usar um cliente SSH diferente de `ssh` ou PuTTY, consulte a documentação de seu cliente sobre como estabelecer um túnel SSH.

* Um navegador da Web que pode ser configurado para usar um proxy SOCKS

* __(opcional)__: um plug-in como o [FoxyProxy](http://getfoxyproxy.org/,) que pode aplicar regras que só roteiam solicitações específicas pelo túnel.

	> [AZURE.WARNING] Sem um plug-in como o FoxyProxy, todas as solicitações feitas por meio do navegador poderão ser roteadas pelo túnel. Isso pode resultar em um carregamento mais lento de páginas da Web em seu navegador.

## Instalar o Zeppelin como parte da criação do cluster

Você pode instalar o Zeppelin em um cluster Spark usando ação de script. A ação de script usa scripts personalizados para instalar componentes no cluster que não estão disponíveis por padrão. O script personalizado para instalar o Zeppelin em um cluster do Spark está disponível em ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

### Usando o Portal do Azure

Para obter instruções sobre como usar o SDK do .NET do HDInsight para executar a ação de script a fim de instalar o Zeppelin, confira [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal). Você deve fazer algumas alterações nas instruções deste artigo.

* Você deve usar o script para instalar o Zeppelin. O script a ser usado é ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

* Você deve executar a ação de script somente no nó principal.

* O script não precisa de parâmetros.

### Usando o SDK do .NET do HDInsight

Para obter instruções sobre como usar o SDK do .NET do HDInsight para executar a ação de script a fim de instalar o Zeppelin, confira [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk). Você deve fazer algumas alterações nas instruções deste artigo.

* Você deve usar o script para instalar o Zeppelin. O script a ser usado é ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

* O script não precisa de parâmetros.

* Defina o tipo de cluster que você está criando como Spark.

### Usando o PowerShell do Azure

Use o trecho do PowerShell a seguir para criar um cluster Spark no HDInsight Linux com o Zeppelin instalado. Verifique se você tem o PowerShell instalado antes de continuar. Confira [Instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter instruções.

	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## Configurar o túnel SSH para acessar um bloco de anotações do Zeppelin

Você usará os túneis SSH para acessar os blocos de anotações do Zeppelin em execução no cluster Spark no HDInsight Linux. As etapas abaixo demonstram como criar um túnel SSH usando a linha de comando ssh (Linux) e PuTTY (Windows).

### Criar um túnel usando o comando SSH (Linux)

Use o comando a seguir para criar um túnel SSH usando o comando `ssh`. Substitua __USERNAME__ por um usuário SSH para seu cluster HDInsight e substitua __CLUSTERNAME__ pelo nome do seu cluster HDInsight

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Isso cria uma conexão que encaminha o tráfego para a porta local 9876 do cluster via SSH. As opções são:

* **D 9876**: a porta local que roteará o tráfego pelo túnel.

* **C**: compactar todos os dados, porque o tráfego da Web é texto, em sua maioria.

* **2**: forçar o SSH para tentar somente a versão 2 do protocolo.

* **q**: modo silencioso.

* **T**: desabilitar alocação pseudo-tty, já que estamos apenas encaminhando uma porta.

* **n**: impede a leitura de STDIN, já que estamos apenas encaminhando uma porta.

* **N**: não executar um comando remoto, pois estamos apenas encaminhando uma porta.

* **f**: executar em segundo plano.

Se você tiver configurado o cluster com uma chave SSH, talvez seja necessário usar o parâmetro `-i` e especificar o caminho para a chave privada de SSH.

Quando o comando terminar, o tráfego enviado para a porta 9876 no computador local será roteado pelo protocolo SSL para o nó de cabeçalho do cluster e parecerá originar-se de lá .

### Criar um túnel usando PuTTY (Windows)

Use as etapas a seguir para criar um túnel SSH usando o PuTTY.

1. Abra o PuTTY e insira as informações da sua conexão. Se você não estiver familiarizado com o PuTTY, confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como usá-lo com o HDInsight.

2. Na seção **Categoria** à esquerda da caixa de diálogo, expanda **Conexão**, expanda **SSH** e selecione **Túneis**.

3. Forneça as seguintes informações no formulário **Opções de controle do encaminhamento de porta SSH**:

	* **Porta de Origem**: a porta no cliente que você deseja encaminhar. Por exemplo, **9876**.

	* **Destino**: o endereço SSH para o cluster HDInsight baseado em Linux. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

	* **Dinâmico**: habilita roteamento de proxy SOCKS dinâmico.

	![imagem de opções de túnel](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. Clique em **Adicionar** para adicionar as configurações e clique em **Abrir** para abrir uma conexão SSH.

5. Quando solicitado, faça logon no servidor. Isso estabelecerá uma sessão SSH e habilitará o túnel.

### Usar o túnel de seu navegador

> [AZURE.NOTE] As etapas desta seção usam o navegador Firefox, pois ele está disponível gratuitamente para os sistemas Linux, Unix, Macintosh OS X e Windows. Outros navegadores modernos, como o Google Chrome, o Microsoft Edge ou o Apple Safari também devem funcionar; no entanto, é possível que o plug-in FoxyProxy usado em algumas etapas não esteja disponível para todos os navegadores.

1. Configure o navegador para usar **localhost:9876** como um proxy **SOCKS v5**. Aqui está a aparência das configurações do Firefox. Se você tiver usado uma porta diferente da 9876, altere a porta que usou:

	![imagem das configurações do Firefox](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE] Selecionar **DNS Remoto** resolverá as solicitações de DNS (Sistema de Nomes de Domínio) usando o cluster HDInsight. Se essa opção estiver desmarcada, o DNS será resolvido localmente.

2. Verifique se o tráfego está sendo roteado pelo túnel visitando um site como [http://www.whatismyip.com/](http://www.whatismyip.com/) com as configurações de proxy habilitadas e desabilitadas no Firefox. Embora as configurações estejam habilitadas, o endereço IP será de um computador no datacenter do Microsoft Azure.

### Extensões do navegador

Embora configurar o navegador para usar o túnel funcione, geralmente não deseja encaminhar todo o tráfego através do túnel. As extensões de navegador, como o [FoxyProxy](http://getfoxyproxy.org/), aceitam a correspondência de padrão para solicitações de URL (somente o FoxyProxy Standard ou Plus) e, portanto, apenas as solicitações de URLs específicas serão enviadas pelo túnel.

Se você tiver instalado o FoxyProxy Standard, use as seguintes etapas para configurá-lo para encaminhar o tráfego apenas para HDInsight pelo túnel.

1. Abra a extensão FoxyProxy no seu navegador. Por exemplo, no Firefox, selecione o ícone FoxyProxy ao lado do campo de endereço.

	![ícone do foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. Selecione **Adicionar Novo Proxy**, clique na guia **Geral** e insira um nome de proxy de **HDInsightProxy**.

	![foxyproxy geral](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. Selecione a guia **Detalhes de Proxy** e preencha os campos a seguir:

	* **Host ou Endereço IP**: é localhost, já que estamos usando um túnel SSH no computador local.

	* **Porta**: é a porta usada para o túnel SSH.

	* **Proxy SOCKS**: selecione esta opção para habilitar o navegador a usar o túnel como proxy.

	* **SOCKS v5**: selecione esta opção para definir a versão necessária do proxy.

	![proxy do foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. Clique na guia **Padrões de URL** e selecione **Adicionar Novo Padrão**. Use o seguinte para definir o padrão e clique em **OK**:

	* **Nome padrão** – **zeppelinnotebook** – apenas um nome amigável para o padrão.

	* **Padrão de URL** – ***hn0*** – isso define um padrão que corresponde ao nome de domínio totalmente qualificado interno do ponto de extremidade em que os blocos de anotações do Zeppelin estão hospedados. Como os blocos de anotações do Zeppelin estão disponíveis somente em headnode0 do cluster e o ponto de extremidade normalmente é `http://hn0-<string>.internal.cloudapp.net`, usar o padrão **hn0** garante que a solicitação seja redirecionada para o ponto de extremidade do Zeppelin.

		![padrão do foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. Clique em **OK** para adicionar o proxy e fechar **Configurações de Proxy**.

5. Na parte superior da caixa de diálogo FoxyProxy, altere **Modo de Seleção** para **Usar proxies com base em seus padrões e prioridades predefinidos** e clique em **Fechar**.

	![modo de seleção do foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

Após a execução destas etapas, somente solicitações de URLs que contêm a cadeia de caracteres __internal.cloudapp.net__ serão roteadas pelo túnel SSL.

## Acessar o bloco de anotações do Zeppelin

Depois de configurar o túnel SSH, você poderá acessar o bloco de anotações do Zeppelin no cluster Spark seguindo as etapas abaixo.

1. No navegador da Web, abra o seguinte ponto de extremidade:

		http://hn0-myspar:9995

	* **hn0**: denota headnode0
	* **myspar**: são as seis primeiras letras do nome do cluster Spark.
	* **9995**: é a porta onde o bloco de anotações do Zeppelin pode ser acessado.

2. Crie um novo bloco de anotações. No painel de cabeçalho, clique em **Notebook** e em **Criar Nova Anotação**.

	![Criar um novo bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.createnewnote.png "Criar um novo bloco de anotações do Zeppelin")

	Na mesma página, sob o título **Notebook**, você verá um novo bloco de anotações com o nome começando por **Note XXXXXXXXX**. Clique no novo bloco de anotações.

3. Na página da Web para o novo bloco de anotações, clique no título e altere o nome do bloco de anotações, se desejar. Pressione ENTER para salvar a alteração do nome. Além disso, verifique se o cabeçalho do bloco de anotações mostra um status **Conectado** no canto superior direito.

	![Status do bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.newnote.connected.png "Status do bloco de anotações do Zeppelin")

4. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\\HdiSamples\\SensorSampleData\\hvac**.

	No parágrafo vazio criado por padrão no novo bloco de anotações, cole o trecho a seguir.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Pressione **SHIFT+ENTER** ou clique no botão **Reproduzir** para o parágrafo executar o trecho. O status no canto direito do parágrafo deve progredir de PRONTO, PENDENTE, EM EXCECUÇÃO para CONCLUÍDO. A saída é exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:

	![Criar uma tabela temporária por meio de dados brutos](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.loaddataintotable.png "Criar uma tabela temporária por meio de dados brutos")

	Você também pode fornecer um título para cada parágrafo. No canto direito, clique no ícone **Configurações** e em **Mostrar título**.

5. Agora você pode executar instruções do Spark SQL na tabela **hvac**. Cole a seguinte consulta em um novo parágrafo. A consulta recupera a ID do prédio e a diferença entre as temperaturas almejada e real para cada prédio em uma determinada data. Pressione **SHIFT+ENTER**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	A instrução **%sql** no início informa ao bloco de anotações para usar o interpretador Spark SQL. Você pode examinar os interpretadores definidos na guia **Interpretador** no cabeçalho do bloco de anotações.

	A captura de tela a seguir mostra o resultado.

	![Executar uma instrução do Spark SQL usando o bloco de anotações](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery1.png "Executar uma instrução do Spark SQL usando o bloco de anotações")

	 Clique nas opções de exibição (realçadas no retângulo) para alternar entre diferentes representações para o mesmo resultado. Clique em **Configurações** para escolher o que constitui a chave e os valores na saída. A captura de tela acima usa **buildingID** como a chave e a média de **temp\_diff** como o valor.

	
6. Você também pode executar instruções Spark SQL usando variáveis na consulta. O seguinte trecho mostra como definir uma variável, **Temp**, na consulta com os possíveis valores com os quais você deseja consultar. Quando você executa a consulta pela primeira vez, uma lista suspensa é preenchida automaticamente com os valores especificados para a variável.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Cole esse trecho em um novo parágrafo e pressione **SHIFT+ENTER**. A captura de tela a seguir mostra o resultado.

	![Executar uma instrução do Spark SQL usando o bloco de anotações](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery2.png "Executar uma instrução do Spark SQL usando o bloco de anotações")

	Em consultas subsequentes, você pode selecionar um novo valor na lista suspensa e executar a consulta novamente. Clique em **Configurações** para escolher o que constitui a chave e os valores no resultado. A captura de tela acima usa o **buildingID** como chave, a média de **temp\_diff** como valor e a **targettemp** como grupo.

7. Reinicie o interpretador do SQL Sparks para sair do aplicativo. Clique na guia **Interpretador** na parte superior e, para o interpretador do Spark, clique em **Reiniciar**.

	![Reiniciar o interpretador do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.zeppelin.restart.interpreter.png "Reiniciar o interpretador do Zeppelin")


## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### Cenários

* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Ferramentas e extensões

* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Kernels disponíveis para o bloco de anotações do Jupyter no cluster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->