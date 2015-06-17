<properties 
   pageTitle="Tutorial do Hadoop: Introdução ao Hadoop no Windows | Microsoft Azure" 
   description="Introdução ao Hadoop no HDInsight. Saiba como provisionar clusters Hadoop no Windows, executar uma consulta de Hive nos dados e analisar a saída no Excel." 
   keywords="hadoop tutorial,hadoop on windows,hadoop cluster,learn hadoop, hive query"
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/20/2015"
   ms.author="nitinme"/>


# Tutorial do Hadoop: Introdução ao Hadoop e uma consulta de Hive no HDInsight no Windows

> [AZURE.SELECTOR]
- [Windows](hdinsight-get-started.md)
- [Linux](hdinsight-hadoop-linux-get-started.md)

Para ajudá-lo a conhecer o Hadoop no Windows e começar a usar o HDInsight, este tutorial mostra como executar uma consulta de Hive em dados não estruturados em um cluster Hadoop e, em seguida, analisar os resultados no Microsoft Excel.

## O que este tutorial Hadoop faz? 

Suponha que você tenha um grande conjunto de dados não estruturado e deseje executar uma consulta de Hive nele para extrair algumas informações importantes. É exatamente isso que vamos fazer neste tutorial. Veja como faremos isso:

   !["Tutorial do Hadoop: Criar uma conta; provisionar um cluster Hadoop; enviar uma consulta de Hive; analisar os dados no Excel.][image-hdi-getstarted-flow]

Assista a um vídeo de demonstração deste tutorial para aprender sobre o Hadoop no HDInsight:

![Vídeo de um primeiro tutorial do Hadoop: Enviar uma consulta de Hive em um cluster Hadoop e analisar os resultados no Excel.][img-hdi-getstarted-video]

**[Assista ao tutorial do Hadoop para HDInsight no YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**


Em conjunto com a disponibilidade geral do Azure HDInsight, a Microsoft também oferece o Emulador do HDInsight para o Azure, anteriormente conhecido como *Microsoft HDInsight Developer Preview*. O Emulador tem como alvo os cenários de desenvolvedor e só dá suporte a implantações de um único nó. Para obter informações sobre como usar o Emulador do HDInsight, consulte [Introdução ao Emulador do HDInsight][hdinsight-emulator].

> [AZURE.NOTE]Para obter instruções sobre como provisionar um cluster HBase, consulte [Provision HBase cluster in HDInsight (Provisionar um cluster HBase no HDInsight)][hdinsight-hbase-custom-provision]. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Qual é a diferença entre Hadoop e HBase?</a> para entender por que optar por um ou outro banco de dados.

## Pré-requisitos

Antes de começar este tutorial para Hadoop no Windows, você deve ter o seguinte:


- Uma assinatura do Azure. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].
- Um computador com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

**Tempo estimado para concluir este tutorial:** 30 minutos



##<a name="storage"></a>Criar uma conta do Armazenamento do Azure

Ao provisionar um cluster Hadoop no HDInsight, você especifica uma conta de armazenamento do Azure. Um contêiner de armazenamento de blob específico dessa conta é designado como sistema de arquivos padrão, como no sistema de arquivos distribuído Hadoop (HDFS). Por padrão, o cluster HDInsight é provisionado no mesmo datacenter da conta de armazenamento especificada por você. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

>[AZURE.NOTE]Não compartilhe um contêiner de armazenamento de blob padrão com vários clusters Hadoop.

Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento ao configurar um cluster de modo personalizado. Essa conta de armazenamento adicional pode ser da mesma assinatura ou de assinaturas diferentes do Azure. Para obter instruções, consulte [Provisionar clusters HDInsight usando opções personalizadas][hdinsight-provision].

Este tutorial usa apenas o blob padrão e a conta de armazenamento padrão.

**Para criar uma conta do Armazenamento do Azure**

1. Entre no [Portal do Azure][azure-management-portal].
2. Clique em **NOVO** no canto inferior esquerdo e insira os valores conforme mostrado na imagem.

	![Portal do Azure, em que você pode usar a criação rápida para configurar uma nova conta de armazenamento.][image-hdi-storageaccount-quickcreate]

>[AZURE.NOTE]Certifique-se de criar sua conta de armazenamento em um local compatível com o cluster. São eles: **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Europa Ocidental**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA**, **Centro-Sul dos EUA**.

Selecione a nova conta de armazenamento na lista e clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página. Anote a **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA** – qualquer uma das chaves funciona). Você precisará delas mais tarde no tutorial. Para obter mais informações, consulte [Como criar contas de armazenamento][azure-create-storageaccount].
	
##<a name="provision"></a>Provisionar um cluster Haddop

Ao provisionar um cluster, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Nesta seção, você provisiona um cluster HDInsight versão 3.1, que se baseia no Hadoop versão 2.4. Você também pode criar clusters Hadoop para outras versões usando o portal do Azure, cmdlets do PowerShell do HDInsight ou o HDInsight .NET SDK. Para obter instruções, consulte [Provisionar clusters HDInsight usando opções personalizadas][hdinsight-provision]. Para obter informações sobre as versões do HDInsight e seus SLAs, consulte [Controle de versão de componentes do HDInsight](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**Provisionar um cluster Hadoop**

1. Entre no [Portal do Azure][azure-management-portal]. 

2. Clique em **NOVO** no canto inferior esquerdo e insira os valores conforme mostrado na imagem.

	![Criação de um cluster Hadoop no HDInsight.][image-hdi-quickcreatecluster]

<!-- COMMENTED OUT TEXT BEGINS --

4. Enter or select the following values:

	
	<table border="1">
	<tr><th>Name</th><th>Value</th></tr>
	<tr><td>Cluster Name</td><td>Name of the cluster.</td></tr>
	<tr><td>Cluster Size</td><td>Number of data nodes you want to deploy. The default value is 4. But the option to use 1 or 2 data nodes is also available from the drop-down list. Any number of cluster nodes can be specified by using the <strong>Custom Create</strong> option. Pricing details about the billing rates for various cluster sizes are available. Click the <strong>?</strong> symbol above the drop-down list and follow the link that appears.</td></tr>
	<tr><td>Password</td><td>The password for the <i>admin</i> account. The cluster user name "admin" is specified when you are not using the <strong>Custom Create</strong> option. Note that this is NOT the Windows Administrator account for the VMs on which the clusters are provisioned. The account name can be changed by using the <strong>Custom Create</strong> wizard.</td></tr>
	<tr><td>Storage Account</td><td>Click the drop-down list, and select the storage account that you created. <br/>

	When a storage account is chosen, it cannot be changed. If the storage account is removed, the cluster will no longer be available for use.

	The HDInsight cluster is located in the same datacenter as the storage account. 
	</td></tr>
	</table>
	
	Keep a copy of the cluster name. You will need it later in the tutorial.

	
5. Click **Create HDInsight Cluster**. When the provisioning completes, the  status column shows **Running**.

-- COMMENTED OUT TEXT ENDS -->

>[AZURE.NOTE]Essas etapas provisionam um cluster HDInsight com a versão 3.1. Para criar um cluster com outras versões, use o método **Criação Personalizada** do portal ou use o PowerShell do Azure. Para obter informações sobre as diferenças entre cada versão, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?][hdinsight-versions] Para obter informações sobre como usar a opção **CRIAÇÃO PERSONALIZADA**, consulte [Provisionar clusters HDInsight usando opções personalizadas][hdinsight-provision].


##<a name="sample"></a>Executar dados de exemplos do portal

Um cluster de HDInsight provisionado com êxito fornece um console de consulta que inclui uma Galeria de Introdução para executar os exemplos diretamente do portal. Você pode usar os exemplos para saber como trabalhar com o HDInsight analisando alguns cenários básicos. Esses exemplos vêm com todos os componentes necessários, como os dados para analisar e as consultas para executar nos dados. Para saber mais sobre os exemplos na Galeria de Introdução, consulte [Saiba mais sobre Hadoop no HDInsight usando a Galeria de Introdução do HDInsight](hdinsight-learn-hadoop-use-sample-gallery.md).

**Para executar o exemplo**, no portal do Azure, clique no nome do cluster em que você deseja executar o exemplo e, depois, em **Console de Consulta**, na parte inferior da página. Na página da Web que é aberta, clique na **Galeria de Introdução** e, na categoria **Exemplos**, clique no exemplo que deseja executar. Siga as instruções na página da Web para concluir o exemplo. A tabela a seguir lista alguns exemplos e fornece mais informações sobre o que cada exemplo faz.

Amostra | O que ela faz?
------ | ---------------
[Análise de dados de sensor][hdinsight-sensor-data-sample] | Aprenda a usar o HDInsight para processar dados históricos produzidos por sistemas de aquecimento, ventilação e ar-condicionado a fim de identificar sistemas que não estão conseguindo manter uma temperatura definida.
[Análise de log do site][hdinsight-weblogs-sample] | Aprenda a usar o HDInsight para analisar arquivos de log de um site para obter informações sobre a frequência de visitas provenientes de sites externos e um resumo dos erros que os usuários enfrentam no site.
[Análise de tendências do Twitter](hdinsight-analyze-twitter-data.md) | Aprenda a usar o HDInsight para analisar tendências no Twitter.



##<a name="hivequery"></a>Executar uma consulta de Hive do portal
Agora que você provisionou um cluster HDInsight, a próxima etapa é executar um trabalho do Hive para consultar uma tabela Hive de exemplo. Vamos usar *hivesampletable*, que vem com o HDInsight. A tabela contém dados sobre fabricantes, plataformas e modelos de dispositivos móveis. Uma consulta de Hive nessa tabela recupera dados para dispositivos móveis de um fabricante específico.

> [AZURE.NOTE]As Ferramentas do HDInsight para Visual Studio vem com o SDK do Azure para .NET versão 2.5 ou posterior. Usando as ferramentas no Visual Studio, você pode se conectar ao cluster HDInsight, criar tabelas Hive e executar consultas Hive. Para obter mais informações, consulte [Introdução ao uso das ferramentas do HDInsight Hadoop para Visual Studio][1].

**Para executar um trabalho do Hive pelo painel do cluster**

1. Entre no [Portal do Azure][azure-management-portal]. 
2. Clique em **HDINSIGHT** a partir do painel esquerdo. Você verá uma lista de clusters, incluindo o cluster que você criou na seção anterior.
3. Clique no nome do cluster que deseja usar para executar o trabalho do Hive e clique em **CONSOLE DE CONSULTA**, na parte inferior da página. 
4. Uma página da Web é aberta em uma guia diferente do navegador. Insira a conta e a senha do usuário do Hadoop. O nome de usuário padrão é **admin**; a senha é a que você digitou durante o provisionamento do cluster. O painel tem esta aparência:

	![Guia Editor do Hive no painel do cluster HDInsight.][img-hdi-dashboard]

	Há várias guias na parte superior da página. A guia padrão é **Editor do Hive** e as outras guias são **Histórico de Trabalhos** e **Navegador de Arquivos**. Usando o painel, você pode enviar consultas Hive, conferir os logs de trabalho do Hadoop e procurar arquivos no armazenamento.

	> [AZURE.NOTE]Observe que a URL da página da Web é *&lt;NomeDoCluster&gt;.azurehdinsight.net*. Então, em vez de abrir o painel pelo portal, você pode usar a URL para abrir o painel em um navegador da Web.

6. Na guia **Editor do Hive**, de **Nome da Consulta**, insira **HTC20**. O nome da consulta é o cargo. No painel de consulta, insira a consulta de Hive, conforme mostrado na imagem:
		
	![Consulta de Hive inserida no painel de consulta do Editor do Hive.][img-hdi-dashboard-query-select]

4. Clique em **Enviar**. Demora alguns minutos para obter os resultados. A tela é atualizada a cada 30 segundos. Você também pode clicar em **Atualizar** para atualizar a tela.

    ![Resultados de uma consulta Hive listadas na parte inferior do painel do cluster.][img-hdi-dashboard-query-select-result]

5. Depois que o status mostrar que o trabalho está concluído, clique no nome de consulta na tela para ver a saída. Anote a **Hora de Início do Trabalho (UTC)**. Você precisará dela mais tarde.

    ![Hora de Início do Trabalho listada na guia Histórico de Trabalhos do painel do cluster HDInsight.][img-hdi-dashboard-query-select-result-output]

    A página também mostra a **Saída do Trabalho** e o **Log do Trabalho**. Além disso, você tem a opção de baixar o arquivo de saída (_stdout) e o arquivo de log (_stderr).


**Para navegar até o arquivo de saída**

1. No painel do cluster, clique em **Navegador de Arquivos**. 
2. Clique no nome da conta de armazenamento, clique no nome do contêiner (que é o mesmo que o nome do cluster) e clique em **usuário**.
3. Clique em **admin** e clique na GUID que apresenta a hora da última modificação (um pouco depois da hora de início do trabalho que você anotou anteriormente). Copie esse GUID. Você precisará dele na próxima seção.


   	![O GUID do arquivo de saída da consulta de Hive listado na guia Navegador de Arquivos.][img-hdi-dashboard-query-browse-output]


##<a name="powerquery"></a>Conectar-se às ferramentas do Microsoft Business Intelligence para Excel 

Você pode usar o suplemento Power Query para importar a saída de trabalho do HDInsight para o Microsoft Excel, onde as ferramentas de business intelligence da Microsoft podem ser usadas para analisar os resultados mais detalhadamente.

Você deve ter o Excel 2013 ou 2010 instalado para concluir esta parte do tutorial.

**Para baixar o Microsoft Power Query para Excel**

- Baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft](http://www.microsoft.com/download/details.aspx?id=39379) e instale-o.

**Para importar dados do HDInsight**

1. Abra o Excel e crie uma nova pasta de trabalho.
3. Clique no menu **Power Query**, clique em **De Outras Fontes** e, em seguida, clique em **Do Azure HDInsight**.

	![Menu Importar do Excel PowerQuery aberto para o Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Insira o **Nome da Conta** da conta de armazenamento de Blob do Azure que está associada ao cluster e clique em **OK**. (É a conta de armazenamento que você criou no tutorial.)
4. Insira a **Chave de Conta** da conta de armazenamento de Blob do Azure e clique em **Salvar**. 
5. No painel direito, clique duas vezes no nome do blob. Por padrão, o nome do blob é idêntico ao nome do cluster. 

6. Localize **stdout** na coluna **Nome**. Verifique se o GUID na coluna **Caminho da Pasta** correspondente coincide com o GUID que você copiou anteriormente. Valores correspondentes sugerem que os dados de saída correspondem ao trabalho enviado. Clique em **Binário** na coluna à esquerda de **stdout**.

	![Localizar a saída de dados pelo GUID na lista de conteúdo.][image-hdi-gettingstarted-powerquery-importdata2]

9. Clique em **Fechar e Carregar**, no canto superior esquerdo, para importar a saída de trabalho para o Excel.


##<a name="nextsteps"></a>Próximas etapas
Neste tutorial do Hadoop, você aprendeu a provisionar um cluster Hadoop no Windows em HDInsight, executar uma consulta de Hive nos dados e importar os resultados para o Excel, onde eles podem ser processados e exibidos graficamente usando ferramentas de business intelligence. Para saber mais, consulte os seguintes tutoriais:

- [Introdução ao uso das Ferramentas do HDInsight Hadoop para Visual Studio][1]
- [Introdução ao Emulador do HDInsight][hdinsight-emulator]
- [Usar o armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]
- [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce]
- [Usar o Hive com o HDInsight][hdinsight-use-hive]
- [Usar o Pig com o HDInsight][hdinsight-use-pig]
- [Usar o Oozie com o HDInsight][hdinsight-use-oozie]
- [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming]
- [Desenvolver programas MapReduce em Java para HDInsight][hdinsight-develop-mapreduce]


[1]: hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage/storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: install-configure-powershell.md
[powershell-open]: install-configure-powershell.md#Install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=58-->