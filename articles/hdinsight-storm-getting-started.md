<properties 
	pageTitle="Introdução ao uso do Apache Storm com o Microsoft Azure HDInsight (Hadoop)" 
	description="Aprenda a usar  o Apache Storm para processar dados em tempo real com o HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="larryfr"/>

# Introdução ao uso do Storm com o HDInsight (Hadoop)

O Apache Storm é um sistema de computação escalável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Azure HDInsight, você pode criar um cluster Hadoop baseado em nuvem que realiza uma análise de dados em tempo real com o Storm.

## Neste tutorial, você aprenderá...

* [Como provisionar um cluster Storm no HDInsight](#provision)

* [Como conectar-se ao cluster](#connect)

* [Como executar uma topologia do Storm](#run)

* [Como monitorar uma topologia do Storm](#monitor)

* [Como parar uma topologia do Storm](#stop)

* [Próximas etapas](#next)

## Antes de começar

Você deve ter o seguinte para concluir com sucesso este tutorial.

* Uma assinatura do Azure

* Microsoft Azure PowerShell

* Se não estiver familiarizado com o Apache Storm, você deve ler primeiro o artigo [Visão geral do HDInsight Storm](/pt-br/documentation/articles/hdinsight-storm-overview) article.

## <a id="provision"></a>Provisionar um cluster Storm no portal do Azure

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. Entre no [Portal de Gerenciamento do Azure][azureportal]

2. Clique em **HDInsight** à esquerda e depois em **+NOVO** no canto inferior esquerdo da página.

3. Clique no ícone HDInsight na segunda coluna e selecione **Personalizar**.

4. Na página **Detalhes do Cluster**, insira o nome do novo cluster e selecione **Storm** como o **Tipo de Cluster**. Clique na seta para continuar.

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. Insira o número de **Nós de dados** a serem utilizados para esse cluster e a **Região/Rede virtual** na qual esse cluster será criado. Clique na seta para continuar.

	> [AZURE.NOTE] Para minimizar o custo do cluster usado neste artigo, reduza o **Tamanho do Cluster** para 1 e exclua o cluster após ter terminado de usá-lo.

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. Insira o **Nome de Usuário** e a **Senha** do administrador e clique na seta para continuar.

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

7. Em **Conta de Armazenamento**, selecione **Criar Novo Armazenamento** ou selecione uma conta de armazenamento existente. Selecione ou insira o **Nome da Conta** e o **Contêiner padrão** a utilizar.

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

8. Na página **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre o script personalizado que você deseja executar para personalizar um cluster, enquanto o cluster está sendo criado. Por exemplo, você pode usar a Ação de Script para personalizar um cluster para instalar o <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script](/pt-br/documentation/articles/hdinsight-hadoop-customize-cluster/). 
	
	![Configure Script Action to customize an HDInsight HBase cluster](./media/hdinsight-storm-getting-started/wizard5.png "Use Script Action to customize an HDInsight cluster") 

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nome</td>
			<td>Especifique um nome para a ação de script.</td></tr>
		<tr><td>URI do script</td>
			<td>Especifique o URI para o script que é chamado para personalizar o cluster.</td></tr>
		<tr><td>Tipo de nó</td>
			<td>Especifica os nós em que o script de personalização é executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós headnode</b> ou <b>Somente nós de trabalho</b>.
		<tr><td>Parâmetros</td>
			<td>Especifique os parâmetros, se exigido pelo script.</td></tr>
	</table>

	Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

## Usando o Storm no HDInsight

Para a versão de teste do Storm no HDInsight, você deve se conectar ao cluster usando a Área de Trabalho Remota para trabalhar com o Storm. Use as etapas a seguir para se conectar ao cluster do HDInsight e usar o comando Storm.

###<a id="connect"></a>Conectar-se ao cluster

1. Habilite a conectividade com a área de trabalho remota para seu cluster do HDInsight usando o [Portal de Gerenciamento do Azure][gerenciamento]. No portal, selecione seu cluster HDInsight e selecione __Habilitar Remoto__ na parte inferior da página de __Configuração__.

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    Quando solicitado, insira um nome de usuário e uma senha para uso em sessões de acesso remoto. Você também deve selecionar uma data de vencimento para o acesso remoto.

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. Após o acesso remoto ser habilitado, selecione __Conectar__ para iniciar a conexão. Isso baixará um arquivo __.rdp__, que poderá ser usado para iniciar uma sessão de área de trabalho remota.

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [AZURE.NOTE] Você pode receber vários prompts para que confie em certificados ao se conectar ao computador remoto.

3. Uma vez conectado, use o ícone __Linha de comando do Hadoop__ na área de trabalho para iniciar a linha de comando do Hadoop.

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

4. Na Linha de comando do Hadoop, use o comando a seguir para alterar diretórios para o diretório que contém o comando Storm.

	cd %storm_home%\bin

5. Para ver uma lista de comandos disponíveis, digite `storm` sem nenhum parâmetro.

O cluster HDInsight é fornecido com várias topologias Storm de amostra. O exemplo **WordcountTopology** é usado nas etapas a seguir. Para obter mais informações sobre os exemplos fornecidos com o Storm no HDInsight, consulte [Próximas etapas](#next).

### <a id="run"></a>Para executar uma topologia do Storm

Para executar o **WordCountTopology**, use os comandos a seguir.

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

Isso diz ao Storm que queremos executar a topologia de **wordcount** na classe **storm.starter.WordCountTopology**, contida no arquivo **storm-starter-&lt;version>-jar-with-dependencies.jar**. Esse arquivo está localizado na pasta contrib no diretório %storm_home%, juntamente com os outros exemplos do Storm.

> [AZURE.NOTE] A versão do arquivo JAR que contém os exemplos pode ser alterada periodicamente. Especifique a versão do arquivo fornecido com o cluster ao executar esse comando.

Observe que nada é retornado ao inserir o comando. **Uma topologia Storm, uma vez iniciada, é executada até que você a interrompa.** O WordCountTopology gerará sentenças aleatórias e manterá uma contagem de quantas vezes cada palavra for encontrada até que seja interrompido.

### <a id="monitor"></a>Para monitorar o status de uma topologia Storm

A amostra WordCountTopology não grava saídas em um diretório, mas podemos usar páginas da Web da interface do cliente do Storm para exibir o status da topologia, bem como a saída.

1. Usando a área de trabalho remota, conecte-se ao cluster do HDInsight.

2. Abra o link **Interface do usuário do Storm** na área de trabalho. Isso exibirá a página da Web da interface do usuário do Storm.  Em **Resumo da topologia**, selecione a entrada **wordcount**.

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	Isso exibirá as estatísticas para a topologia, incluindo os componentes que formam a topologia, os **spouts** e **bolts**.

3. Selecione o link do**spout** e selecione o número da **Porta** para uma das entradas na lista **Executores (o tempo todo)** que tenha um número superior a 0 nas colunas**Emitido** e**Transferido**.

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [AZURE.NOTE] Dependendo do número de nós no cluster e da topologia que está sendo executada, pode levar alguns minutos até que a topologia comece a ser processada. Atualize a página periodicamente até que os valores**Emitido** e **Transferido** comece a aumentar.

4. Você deverá ver uma página contendo informações semelhantes às seguintes.

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	A partir desse trecho, você pode ver que o spout emite a sentença 'snow white and the seven dwarfs', que foi dividida em palavras individuais. Além disso, uma contagem é mantida para quantas vezes cada palavra foi processada pela topologia desde que ela foi iniciada. Por exemplo, o termo 'dwarfs' foi emitido 8360 vezes pelo spout quando a saída acima foi visualizada.

### <a id="stop"></a>Para interromper uma topologia do Storm

A **WordCountTopology** continuará a ser executada até que seja interrompida. Para interrompê-la, use o comando a seguir.

	storm kill wordcount

Caso veja a página da Web da interface do usuário do Storm na Web imediatamente após esse comando, notará que o status de **wordcount** em **Resumo da topologia** terá mudado para KILLED. Após alguns segundos, a topologia não será mais listada na seção **Resumo da topologia**.

## <a id="next"></a>Próximas etapas

* **Arquivos de exemplo** - O cluster do HDInsight Storm oferece vários exemplos no diretório **%storm_home%\contrib**. Cada exemplo deve conter o seguinte.

	* O código-fonte - por exemplo, storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Os documentos Java - por exemplo, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* O exemplo - por exemplo, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Use o comando 'jar' para extrair o código-fonte ou os documentos Java. Por exemplo, 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [AZURE.NOTE] Os documentos Java consistem em páginas da Web. Após a extração, use um navegador para exibir o arquivo **index.html**.

* [Analisando dados de sensor com Storm e HDInsight](/ pt-br/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Desenvolver aplicativos de processamento de dados de streaming com SCP.NET e C# em Storm no HDInsight](/pt-br/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/

<!--HONumber=42-->
