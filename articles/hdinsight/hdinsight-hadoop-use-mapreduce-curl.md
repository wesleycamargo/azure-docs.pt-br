---
title: Usar o MapReduce e Curl com o Hadoop no HDInsight | Microsoft Docs
description: Saiba como executar trabalhos MapReduce remotamente com Hadoop no HDInsight usando o Curl.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dc0e49f96bc80045eed888ff5354e5b6902a841c


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Executar trabalhos MapReduce com Hadoop no HDInsight usando o Curl
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Neste documento, você aprenderá como usar o Curl para executar trabalhos MapReduce em um Hadoop no cluster HDInsight.

O Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar trabalhos MapReduce. Isso funciona usando a API REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster HDInsight.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for iniciante no HDInsight, consulte [O que você precisa saber sobre Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-information.md).
> 
> 

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Pré-requisitos
Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop no cluster HDInsight (baseado em Linux ou Windows)
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a name="a-idcurlarun-mapreduce-jobs-using-curl"></a><a id="curl"></a>Executar trabalhos MapReduce usando o Curl
> [!NOTE]
> Ao usar o Curl ou quaisquer outras comunicações do REST com WebHCat, deve autenticar as solicitações fornecendo o nome de usuário de administrador de cluster HDInsight e a senha. Você também deve usar o nome do cluster como parte do URI usado para enviar as solicitações para o servidor.
> 
> Para os comandos nesta seção, substitua **USERNAME** pelo usuário para autenticar o cluster, e **PASSWORD** pela senha da conta de usuário. Substitua **CLUSTERNAME** pelo nome do cluster.
> 
> A API REST é protegida usando [autenticação básica de acesso](http://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS para garantir que suas credenciais sejam enviadas com segurança para o servidor.
> 
> 

1. De uma linha de comando, use o seguinte comando para verificar se você pode conectar-se ao cluster HDInsight:
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    Você deve receber uma resposta com esta aparência:
   
        {"status":"ok","version":"v1"}
   
    Os parâmetros usados nesse comando são os seguintes:
   
   * **-u**: o nome de usuário e a senha usada para autenticar a solicitação
   * **-G**: indica que se trata de uma solicitação GET
     
     O início do URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações.
2. Para enviar um trabalho MapReduce, use o seguinte comando:
   
        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
   
    O final do URI (/mapreduce/jar) informa o WebHCat que essa solicitação iniciará um trabalho MapReduce de uma classe em um arquivo jar. Os parâmetros usados nesse comando são os seguintes:
   
   * **-d** já que `-G` não é usado; a solicitação padrão será o método POST. `-d` especifica os valores de dados que são enviados com a solicitação.
     
     * **user.name**: o usuário que está executando o comando
     * **jar**: o local do arquivo jar que contém a classe para ser executada
     * **class**: a classe que contém a lógica do MapReduce
     * **arg**: os argumentos a serem passados para o trabalho MapReduce; nesse caso, o arquivo de texto de entrada e o diretório usados para a saída
     
     Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho:
     
       {"id":"job_1415651640909_0026"}
3. Para verificar o status do trabalho, use o comando a seguir. Substitua o **JOBID** pelo valor retornado na etapa anterior. Por exemplo, se o valor retornado foi `{"id":"job_1415651640909_0026"}`, JOBID será `job_1415651640909_0026`.
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    Se o trabalho for concluído, o estado será “SUCCEEDED".
   
   > [!NOTE]
   > Essa solicitação de Curl retorna um documento JSON com informações sobre o trabalho; jq é usado para recuperar o valor de estado.
   > 
   > 
4. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho do Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém o local do arquivo de saída; nesse caso, **wasb:///example/curl**. Esse endereço armazena a saída do trabalho no diretório **example/curl** do contêiner de armazenamento padrão usado pelo cluster HDInsight.

Você pode listar e baixar esses arquivos usando a [CLI do Azure](../xplat-cli-install.md). Por exemplo, para listar arquivos em **example/curl**, use o seguinte comando:

    azure storage blob list <container-name> example/curl

Para baixar um arquivo, use o seguinte:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [!NOTE]
> Você deve especificar o nome da conta de armazenamento que contém o blob usando os parâmetros `-a` e `-k` ou definir as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**. Consulte [como carregar dados no HDInsight](hdinsight-upload-data.md) para obter mais informações.
> 
> 

## <a name="a-idsummaryasummary"></a><a id="summary"></a>Resumo
Conforme demonstrado nesse documento, você pode usar a solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos do Hive no cluster do HDInsight.

Para obter mais informações sobre a interface REST usada nesse artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Próximas etapas
Para obter informações gerais sobre trabalhos de MapReduce no HDInsight:

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)




<!--HONumber=Nov16_HO3-->


