---
title: "Tópicos mais populares do Twitter com Apache Storm no HDInsight | Microsoft Docs"
description: "Saiba como usar o Trident para criar uma topologia Apache Storm que determina os tópicos mais populares do Twitter com base em hashtags."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 63b280ea-5c07-4dc8-a35f-dccf5a96ba93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: d588221586f151319436525c5098b0bb2694e5f9
ms.lasthandoff: 04/17/2017


---
# <a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Determine os tópicos mais populares do Twitter com o Apache Storm no HDInsight

Saiba como usar o Trident para criar uma topologia Storm que determina os tópicos mais populares (hashtags) no Twitter.

O Trident é uma abstração de alto nível que fornece ferramentas como junções, agregações, agrupamento, funções e filtros. Além disso, o Trident adiciona primitivos para fazer o processamento incremental com monitoração de estado. O exemplo usado neste documento é uma topologia Trident com um spout e uma função personalizados. Ele também usa várias funções internas fornecidas pelo Trident.

## <a name="requirements"></a>Requisitos

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java e o JDK 1.8</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Uma conta de desenvolvedor do Twitter

## <a name="download-the-project"></a>Baixe o projeto

Use o código a seguir para clonar o projeto localmente.

    git clone https://github.com/Blackmist/TwitterTrending

## <a name="understanding-the-topology"></a>Noções básicas sobre a topologia

O diagrama a seguir mostra de como os dados fluem por essa topologia:

![Topologia](./media/hdinsight-storm-twitter-trending/trident.png)

> [!NOTE]
> Esse diagrama é uma visão simplificada da topologia. Várias instâncias dos componentes são distribuídas entre os nós no cluster.


O código do Trident que implementa a topologia é o seguinte:

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Esse código executa as ações a seguir:

1. Cria um fluxo do spout. O spout recupera tweets do Twitter e os filtra por palavras-chave específicas (amor, música e café neste exemplo).

2. O HashtagExtractor, uma função personalizada, é usado para extrair hashtags de cada tweet. As hashtags são emitidas para o fluxo.

3. O fluxo é agrupado por hashtag e transmitido para um agregador. Esse agregador cria uma contagem de quantas vezes cada hashtag ocorreu. Esses dados persistem na memória. Por fim, é emitido um novo fluxo que contém a hashtag e a contagem.

4. O assembly **FirstN** é aplicado para retornar somente os 10 primeiros valores com base no campo de contagem.

> [!NOTE]
> Para obter mais informações sobre como trabalhar com o Trident, consulte o documento [Visão geral da API do Trident](http://storm.apache.org/releases/current/Trident-API-Overview.html).

### <a name="the-spout"></a>O spout

O spout, **TwitterSpout** usa [Twitter4j](http://twitter4j.org/en/) para recuperar tweets do Twitter. Um filtro é criado para as palavras __love__ (amar), **music** (música) e **coffee** (café). Tweets de entrada (status) que correspondem ao filtro são armazenados em uma fila de bloqueio vinculada. Por fim, os itens são retirados da fila e emitidos na topologia.

### <a name="the-hashtagextractor"></a>O HashtagExtractor

Para extrair hashtags, [getHashtagEntities](http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--) é usado para recuperar todas as hashtags contidas no tweet. Eles são emitidos para o fluxo.

## <a name="configure-twitter"></a>Configurar o Twitter

Use as etapas a seguir para registrar um novo aplicativo do Twitter e obter as informações do token de acesso e do consumidor necessárias para ler do Twitter:

1. Vá para [Aplicativos do Twitter](https://apps.twitter.com) e clique no botão **Criar novo aplicativo**. Durante o preenchimento do formulário, deixe a **URL de retorno** vazia.

2. Quando o aplicativo for criado, clique na guia **Chaves e Tokens de acesso** .

3. Copie as informações **Chave do consumidor** e **Segredo do consumidor**.

4. Na parte inferior da página, selecione **Criar meu token de acesso** se nenhum token existir. Assim que os tokens forem criados, copie as informações **Token de Acesso** e **Segredo do Token de Acesso**.

5. No projeto **TwitterSpoutTopology** anteriormente clonado, abra o arquivo **resources/twitter4j.properties**, adicione as informações coletadas nas etapas anteriores e salve o arquivo.

## <a name="build-the-topology"></a>Compilar a topologia

Use o seguinte código para criar o projeto:

        cd [directoryname]
        mvn compile

## <a name="test-the-topology"></a>Testar a topologia

Use o seguinte comando para testar a topologia localmente:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Depois que a topologia é iniciada, você verá informações de depuração que contêm hashtags e contagens emitidas pela topologia. A saída deve ter aparência similar ao seguinte texto:

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

## <a name="next-steps"></a>Próximas etapas

Agora que você testou a topologia localmente, descubra como implantar essa topologia: [Implantar e gerenciar topologias Apache Storm no HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Você também pode estar interessado nos tópicos do Storm a seguir:

* [Desenvolver topologias Java para Storm no HDInsight usando o Maven](hdinsight-storm-develop-java-topology.md)
* [Desenvolver topologias C# para Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Para ver mais exemplos do Storm para HDInsight:

* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)


