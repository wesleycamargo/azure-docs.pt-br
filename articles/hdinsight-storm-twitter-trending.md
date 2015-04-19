<properties
   pageTitle="Trending topics do Twitter com Apache Storm no HDInsight | Azure"
   description="Saiba como usar o Trident para criar uma topologia Storm que determina os trending topics com base em hashtags."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Trending topics do Twitter com Apache Storm no HDInsight

Saiba como usar Trident para criar uma topologia Storm que determina trending topics (hashtags) no Twitter. 

Trident é uma abstração de alto nível que fornece ferramentas, como junções, agregações, agrupamento, funções e filtros. Além disso, o Trident adiciona primitivos para fazer o processamento incremental com monitoração de estado. Esse exemplo demonstra como você pode criar uma topologia usando um spout personalizado, função e várias funções internas fornecidas pelo trident.

> [AZURE.NOTE] Baseia-se muito no exemplo [trident-storm](https://github.com/jalonsoramos/trident-storm) de Juan Alonso.

##Requisitos

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java e JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Uma conta de desenvolvedor do Twitter

##Baixe o projeto

Use o seguinte para clonar o projeto localmente.

	git clone https://github.com/Blackmist/TwitterTrending

##Topologia

A topologia para esse exemplo é o seguinte:

![topology](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Essa é uma exibição simplificada da topologia - várias instâncias dos componentes serão distribuídas entre os nós no cluster.

O código do Trident que implementa a topologia é o seguinte:

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

Isso faz o seguinte:

1. Cria um novo fluxo do spout. O spout recupera tweets do Twitter, filtrados por palavras-chave específicas - amor, músicas e café neste exemplo.

2. HashtagExtractor, uma função personalizada, é usado para extrair hashtags de cada tweet. Elas são emitidas para o fluxo.

3. O fluxo é agrupado por hashtag e transmitido para um agregador. Esse agregador cria uma contagem de quantas vezes cada hashtag ocorreu, que é mantida na memória. Por fim, é emitido um novo fluxo que contém a hashtag e a contagem.

4. Como estamos interessados apenas nas hashtags mais populares para um determinado lote de tweets, o assembly FirstN é aplicado para retornar somente os 10 principais valores com base no campo de contagem.

> [AZURE.NOTE] Diferentes do spout e HashtagExtractor, estamos usando a funcionalidade interna do Trident.
> 
> Para obter informações sobre operações internas, consulte <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin</a>.
> 
> Para implementações de estado do Trident que não seja MemoryMapState, consulte o seguinte:
> 
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">https://github.com/fhussonnois/storm-trident-elasticsearch</a>
> 
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">https://github.com/kstyrc/trident-redis</a>

###O spout

O spout **TwitterSpout** usa o <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> para recuperar tweets do Twitter. Um filtro é criado (amor, música e café) e tweets de entrada (status) que correspondem ao filtro são armazenados em um <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue</a>. Por fim, os itens são retirados da fila e emitidos na topologia.

###O HashtagExtractor

Para extrair hashtags, <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> é usado para recuperar todas as hashtags contidas no tweet. Eles são emitidos para o fluxo.

##Habilitar Twitter

Use as etapas a seguir para registrar um novo aplicativo do Twitter e obter as informações do token de acesso e do consumidor necessárias para ler no Twitter.

1. Vá para <a href="" target="_blank">https://apps.twitter.com/</a> e use o botão **Criar novo aplicativo**. Durante o preenchimento do formulário, deixe **URL de Retorno** vazio.

2. Quando o aplicativo tiver sido criado, selecione a guia **Chaves e Tokens de Acesso**.

3. Copie as informações **Chave do Consumidor** e **Segredo do Consumidor**.

4. Na parte inferior da página, selecione **Criar meu token de acesso** se nenhum token existir. Assim que os tokens forem criados, copie a informação **Token de Acesso** e **Segredo do Token de Acesso**.

5. No projeto **TwitterSpoutTopology** anteriormente clonado, abra o arquivo **resources/twitter4j.properties**, adicione as informações coletadas nas etapas anteriores e salve o arquivo.

##Compilar a topologia

Use a seguinte compilação do projeto.

		cd [directoryname]
		mvn compile

##Testar a topologia

Use o comando a seguir para testar a topologia localmente.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Depois que a topologia é iniciada, você verá informações de depuração que contém hashtags e contagens emitidas pela topologia. A saída deve ter aparência similar à exibida a seguir.

	DEPURAR: [Quicktellervalentine, 7]
	DEPURAR: [GRAMMYs, 7]
	DEPURAR: [AskSam, 7]
	DEPURAR: [poppunk, 1]
	DEPURAR: [rock, 1]
	DEPURAR: [punkrock, 1]
	DEPURAR: [band, 1]
	DEPURAR: [punk, 1]
	DEPURAR: [indonesiapunkrock, 1]

##Próximas etapas

Agora que você testou a topologia localmente, descubra como [implantar essa topologia para Storm no HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Você também pode estar interessado nos tópicos do Storm a seguir:

* [Desenvolver topologias de Java para Storm no HDInsight usando o Maven](hdinsight-storm-develop-java-topology.md)

* [Desenvolver topologias C# para Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Para obter mais exemplos de Storm para HDInsight:

* [Exemplos de Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples)

* [Analisar dados de sensor do hub de eventos com Storm no HDInsight](hdinsight-storm-sensor-data-analysis.md)
<!--HONumber=47-->
