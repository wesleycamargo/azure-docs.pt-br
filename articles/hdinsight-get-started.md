<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Introdução ao uso do Hadoop com o Hive no HDInsight | Azure" metaKeywords="" description="Comece a usar o Hadoop no HDInsight, uma solução de big data na nuvem. Saiba como provisionar clusters, dados de consulta com o Hive e saída para o Excel para análise." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop with Hive in HDInsight to analyze mobile handset use" authors="nitinme" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="nitinme" />


# Introdução ao uso do Hadoop com o Hive no HDInsight para analisar o uso de fone de celular

<!--div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div-->

Como introdução rápida ao uso do HDInsight, este tutorial mostra como executar uma consulta do Hive para extrair informações significativas de dados não estruturados em um cluster Hadoop. Em seguida, você analisará os resultados no Microsoft Excel.


> [WACOM.NOTE] Se você é iniciante no Hadoop e em Big Data, leia mais sobre os termos [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [HDFS][apache-hdfs] e [Hive][apache-hive]. Para entender como o HDInsight habilita o Hadoop no Azure, consulte [Introdução ao Hadoop no HDInsight][hadoop-hdinsight-intro].

Em conjunto com a disponibilidade geral do Azure HDInsight, a Microsoft também oferece o Emulador do HDInsight para o Azure, anteriormente conhecido como *Microsoft HDInsight Developer Preview*. O Emulador tem como alvo os cenários de desenvolvedor e só dá suporte a implantações de um único nó. Para usar o Emulador do HDInsight, consulte [Introdução ao Emulador do HDInsight][hdinsight-emulator].

> [WACOM.NOTE] Para obter instruções sobre como provisionar um cluster HBase, consulte [Provisionar um cluster HBase no HDInsight][hdinsight-hbase-custom-provision]. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Qual é a diferença entre o Hadoop e o HBase?</a> para entender por que escolher um ou outro.   

## O que este tutorial aborda? ##

Suponha que você tenha um grande conjunto de dados não estruturado e deseje executar consultas nele para extrair algumas informações importantes. É exatamente isso que vamos fazer neste tutorial. Veja como faremos isso:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

Você também pode assistir a um vídeo de demonstração deste tutorial:

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:


- Uma assinatura do Azure. Para obter mais informações sobre como obter uma assinatura, consulte [Opções de Compra, ][azure-purchase-options][Ofertas para Membros][azure-member-offers] ou [Avaliação Gratuita][azure-free-trial].
- Um computador com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

**Tempo estimado para conclusão:**30 minutos

##Neste tutorial

* [Criar uma conta de armazenamento do Azure](#storage)
* [Provisionar um cluster HDInsight](#provision)
* [Executar exemplos do portal](#sample)
* [Executar um trabalho do HIVE](#hivequery)
* [Próximas etapas](#nextsteps)

##<a name="storage"></a>Criar uma conta de Armazenamento do Azure

O HDInsight usa o Armazenamento de Blob do Azure para armazenar dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

Ao provisionar um cluster HDInsight, você especifica uma conta de Armazenamento do Azure. Um contêiner de armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão, exatamente como no HDFS. O cluster HDInsight, por padrão, é provisionado no mesmo data center que a conta de armazenamento especificada.

Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento ao configurar um cluster HDInsight de modo personalizado. Essa conta de armazenamento adicional pode ser da mesma assinatura do Azure ou de assinaturas diferentes do Azure. Para obter instruções, consulte [Provisionar clusters HDInsight usando opções personalizadas][hdinsight-provision]. 

Para simplificar este tutorial, somente o contêiner de blob padrão e a conta de armazenamento padrão são usados. Na prática, os arquivos de dados geralmente são armazenados em uma conta de armazenamento designada.

**Para criar uma conta de Armazenamento do Azure**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e clique em **CRIAÇÃO RÁPIDA**.

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. Digite a **URL, ****LOCAL** e **REPLICAÇÃO** e clique em **CRIAR CONTA DE ARMAZENAMENTO**. Não há suporte para grupos de afinidade.Você verá a nova conta de armazenamento na lista de armazenamento.

	>[WACOM.NOTE]  A opção de criação rápida para provisionar um cluster HDInsight, como a que usamos neste tutorial, não solicita um local durante o provisionamento do cluster. Em vez disso, por padrão, ela colocaliza o cluster no mesmo data center que a conta de armazenamento. Desse modo, verifique se você criou a conta de armazenamento nos locais com suporte para o cluster, que são:  **Ásia Oriental**, **Sudeste Asiático**, **Norte da Europa**, **Europa Ocidental**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA**, **Centro-Sul dos EUA**.

4. Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.
5. Selecione a nova conta de armazenamento na lista e clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
7. Anote o **NOME DA CONTA DE ARMAZENAMENTO** e a **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA**.  As duas chaves funcionam).Você precisará deles mais tarde no tutorial.


Para obter mais informações, consulte
[Como criar uma conta de armazenamento][azure-create-storageaccount] e [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].
	
##<a name="provision"></a>Provisionar um cluster HDInsight

Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Nesta seção, você provisiona um cluster HDInsight versão 3.1, baseado no Hadoop versão 2.4. Também é possível criar clusters Hadoop para outras versões usando o portal do Azure, os cmdlets do PowerShell do HDInsight ou o SDK do .NET do HDInsight. Para obter instruções, consulte [Provisionar clusters HDInsight usando opções personalizadas][hdinsight-provision]. Para obter informações sobre versões diferentes do HDInsight e o SLA (contrato de nível de serviço), consulte a página [Controle de versão do componente do HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/) .

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**Para provisionar um cluster HDInsight** 

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal]. 

2. Clique em **HDInsight** à esquerda para listar o status dos clusters na conta.Na captura de tela a seguir, não há nenhum cluster HDInsight existente.

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. Clique em **NOVO** no canto inferior esquerdo, em **Serviços de Dados**, em **HDInsight** e em **Hadoop**.

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. Digite ou selecione os valores a seguir:

	<table border="1">
	<tr><th>Nome</th><th>Valor</th></tr>
	<tr><td>Nome do cluster</td><td>Nome do cluster</td></tr>
	<tr><td>Tamanho do cluster</td><td>Número de nós de dados que você deseja implantar. O valor padrão é 4. No entanto, a opção de usar 1 ou 2 nós de dados também está disponível no menu suspenso. Qualquer número de nós do cluster pode ser especificado ao utilizar a opção <strong>Criação Personalizada</strong> . Os detalhes de preço referente às taxas de cobrança para diversos tamanhos de cluster estão disponíveis. Clique no símbolo <strong>?</strong> logo acima da lista suspensa e siga o link no pop-up.</td></tr>
	<tr><td>Senha</td><td>A senha da conta <i>admin</i> . O nome de usuário do cluster "admin" é especificado quando você não está usando a opção <strong>Criação Personalizada</strong> . Observe que essa NÃO é a conta de Administrador do Windows para as VMs nas quais os clusters são provisionados. O nome da conta pode ser alterado usando o assistente <strong>Criação Personalizada</strong> .</td></tr>
	<tr><td>Conta de Armazenamento</td><td>Selecione a conta de armazenamento criada na caixa suspensa. <br/>

	Assim que for escolhida, a conta de armazenamento não poderá ser alterada. Se a conta de armazenamento for removida, o cluster não estará disponível para uso.

	O cluster HDInsight está colocalizado no mesmo data center que a conta de armazenamento. 
	</td></tr>
	</table>

	Mantenha uma cópia do nome do cluster.  Você precisará dela mais tarde no tutorial.

	
5. Clique em **Criar Cluster HDInsight**. Quando o provisionamento for concluído, a coluna de status mostrará **Em execução**.

	>[WACOM.NOTE] O procedimento acima cria clusters com o cluster HDInsight versão 3.1. Para criar outras versões de cluster, use o método de criação personalizada no portal de gerenciamento ou use o PowerShell do Azure. Para obter informações sobre o que é diferente entre cada versão de cluster, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?][hdinsight-versions] Para obter informações sobre como usar a opção **CRIAÇÃO PERSONALIZADA**, consulte [Provisionar clusters HDInsight usando opções personalizadas][hdinsight-provision].


##<a name="sample"></a>Executar exemplos do portal

Um cluster do HDInsight provisionado com sucesso oferece um console de consulta para executar amostras diretamente do portal. Você pode usar os exemplos para saber como trabalhar com o HDInsight analisando alguns cenários básicos. Esses exemplos vêm com todos os componentes necessários, como os dados para analisar e as consultas para executar nos dados. 

**Para executar o exemplo**, no Portal de Gerenciamento do Azure, clique no nome do cluster no qual deseja executar o exemplo e clique em **Console de Consulta** na parte inferior da página.  Na página da Web que é aberta, clique na guia **Galeria de introdução** e na categoria **Amostras**; clique na amostra que deseja executar.Siga as instruções na página da Web para concluir a amostra.Para saber mais sobre o que cada amostra faz, clique nos links abaixo.

Amostra | O que ela faz?
------ | ---------------
[Análise de dados de sensor][hdinsight-sensor-data-sample] | Saiba como usar o HDInsight para processar dados históricos produzidos por sistemas de aquecimento, ventilação e ar condicionado (HVAC) para identificar sistemas que não conseguem manter uma temperatura determinada de maneira confiável
[Análise de log de site][hdinsight-weblogs-sample] | Saiba como usar o HDInsight para analisar arquivos de log de sites para obter informações sobre a frequência de visitas em um dia por meio de sites externos e um resumo dos erros do site enfrentados pelos usuários


##<a name="hivequery"></a>Executar uma consulta do HIVE do portal
Agora que você provisionou um cluster HDInsight, a próxima etapa é executar um trabalho do Hive para consultar uma tabela Hive de exemplo, *hivesampletable*, que é fornecida com clusters HDInsight. A tabela contém dados sobre o fabricante de dispositivos móveis, plataformas e modelos. Consultamos essa tabela para recuperar dados para dispositivos móveis de um fabricante específico.

> [WACOM.NOTE] As Ferramentas HDInsight para Visual Studio são fornecidas com o SDK do Azure para .NET versão 2.5 ou posterior.  Usando as ferramentas do Visual Studio, você pode se conectar ao cluster HDInsight, criar tabelas Hive e executar consultas Hive.  Para obter mais informações, consulte [Introdução ao uso das ferramentas do HDInsight Hadoop para Visual Studio][1].

**Para executar um trabalho do Hive em um painel do cluster**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal]. 
2. Clique em **HDINSIGHT** no painel esquerdo.Você verá uma lista dos clusters criados, incluindo aquele criado na última seção.
3. Clique no nome do cluster em que deseja executar o trabalho do Hive e em **CONSOLE DE CONSULTA** na parte inferior da página. 
4. Uma página da Web será aberta em uma guia diferente do navegador. Insira a conta e a senha do usuário do Hadoop.  O nome de usuário padrão é **admin**; a senha é a que você digitou durante o provisionamento do cluster.O painel fica assim:

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	Há várias guias na parte superior.  A guia padrão é **Editor do Hive**, enquanto as outras guias são **Histórico de Trabalhos** e **Navegador de Arquivos**.Usando o painel, você pode enviar consultas do Hive, verificar logs de trabalho do Hadoop e procurar arquivos do WASB.

	> [WACOM.NOTE] Observe que a URL da página da Web é *<Nome_Cluster>.azurehdinsight.net* . Em vez de abrir o painel por meio do Portal de gerenciamento, você também pode abrir o painel de um navegador da Web usando a URL.

6. Na guia **Editor do Hive** de **Nome da Consulta**, insira **HTC20**.O nome da consulta é o cargo.

7. No painel de consulta, insira a seguinte consulta: 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. Clique em **Enviar**. Demora alguns minutos para obter os resultados. A tela é atualizada a cada 30 segundos.Você também pode clicar em **Atualizar** para atualizar a tela.

    Depois de concluído, a tela fica assim:

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. Clique no nome da consulta na tela para ver a saída.  Anote a **Hora de Início do Trabalho (UTC)**.Você precisará dela mais tarde. 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    A página também mostra a **Saída do Trabalho** e o **Log do Trabalho**.Além disso, você tem a opção de baixar o arquivo de saída (\_stdout) e o arquivo de log (\_stderr).


	> [WACOM.NOTE] A tabela **Sessão do Trabalho** na guia **Editor do Hive** lista os trabalhos concluídos ou em execução desde que você permaneça nessa guia. A tabela não listará nenhum trabalho se você sair da página.A guia **Histórico de Trabalhos** mantém uma lista de todos os trabalhos concluídos ou em execução.
 

**Para navegar até o arquivo de saída**

1. No painel do cluster, clique em **Navegador de Arquivos** na parte superior. 
2. Clique no nome da conta de armazenamento, no nome do contêiner (que é o mesmo que o nome do cluster) e em **usuário**.
3. Clique no **administrador** e no GUID com a última hora de modificação, um pouco depois da hora de início do trabalho anotada anteriormente. Anote este GUID.Você precisará dele na próxima seção.


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Conectar-se às ferramentas do Microsoft Business Intelligence 

Você pode usar o suplemento Power Query para Microsoft Excel para importar a saída de trabalho do HDInsight para o Excel, onde as ferramentas do Microsoft BI (Business Intelligence) podem ser usadas para analisar ainda mais os resultados. 

Você deve ter o Excel 2010 ou 2013 instalado para concluir essa parte do tutorial. 

**Para baixar o Microsoft Power Query para Excel**

- Baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft](http://www.microsoft.com/pt-br/download/details.aspx?id=39379) e instale-o.

**Para importar dados no HDInsight**

1. Abra o Excel e crie uma nova planilha em branco.
3. Clique no menu **Power Query**, em **De Outras Fontes** e em **Do Azure HDInsight**.

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Digite o **Nome da Conta** da conta de Armazenamento de Blob do Azure associada ao cluster e clique em **OK**.Esta é a conta de armazenamento criada anteriormente no tutorial.
4. Digite a **Chave de Conta** da Conta de Armazenamento de Blob do Azure e clique em **Salvar**. 
5. No painel de navegação à direita, clique duas vezes no nome do contêiner de armazenamento de Blob. Por padrão, o nome do contêiner é igual ao nome do cluster. 

6. Localize **stdout** na coluna **Nome**.  Verifique se o GUID na coluna **Caminho da Pasta** correspondente coincide com o GUID anotado anteriormente.Valores correspondentes sugerem que os dados de saída correspondem ao trabalho enviado.Clique em **Binário** à esquerda de **stdout**.

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. Clique em **Fechar e Carregar** no canto superior esquerdo para importar a saída de trabalho do Hive no Excel.


##<a name="nextsteps"></a>Próximas etapas
Neste tutorial, você aprendeu a provisionar um cluster com o HDInsight, executar um trabalho de MapReduce e importar os resultados para o Excel, onde eles podem ser processados ainda mais e exibidos graficamente usando ferramentas de BI. Para saber mais, consulte os seguintes artigos:

- [Introdução ao uso das ferramentas do HDInsight Hadoop para Visual Studio][1]
- [Introdução ao Emulador do HDInsight][hdinsight-emulator]
- [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]
- [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce]
- [Usar o Hive com o HDInsight][hdinsight-use-hive]
- [Usar o Pig com o HDInsight][hdinsight-use-pig]
- [Usar o Oozie com o HDInsight][hdinsight-use-oozie]
- [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming]
- [Desenvolver programas Java MapReduce para o HDInsight][hdinsight-develop-mapreduce]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/pt-br/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pt-br/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pt-br/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png

<!--HONumber=35.1-->
