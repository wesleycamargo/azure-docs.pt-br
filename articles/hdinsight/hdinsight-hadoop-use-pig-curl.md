<properties
   pageTitle="Usar o Pig do Hadoop com Curl no HDInsight | Microsoft Azure"
   description="Aprenda a usar o Curl para executar trabalhos do Pig Latin em um cluster do Hadoop no Azure HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Executar trabalhos do Pig com Hadoop no HDInsight usando o Curl

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Neste documento, você aprenderá a usar o Curl para executar trabalhos do Pig Latin em um cluster Azure HDInsight. A linguagem de programação Pig Latin permite descrever as transformações que são aplicadas aos dados de entrada para produzir a saída desejada.

Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar, monitorar e recuperar os resultados de trabalhos do Pig. Isso funciona usando a API REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster HDInsight.

> [AZURE.NOTE]Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas é novo no HDInsight, consulte [Dicas do HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster do Azure HDInsight (Hadoop no HDInsight, baseado em Windows ou Linux)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Executar trabalhos do Pig usando Curl

> [AZURE.NOTE]Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário do administrador e a senha para o cluster HDInsight. Você também deve usar o nome do cluster como parte do URI (Uniform Resource Identifier) usado para enviar as solicitações ao servidor.
>
> Para os comandos nesta seção, substitua **USERNAME** pelo usuário para autenticar o cluster e substitua **PASSWORD** pela senha da conta de usuário. Substitua **CLUSTERNAME** pelo nome do cluster.
>
> A API REST é protegida por meio de [autenticação básica de acesso](http://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS (HTTP seguro) para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Você deve receber uma resposta com esta aparência:

        {"status":"ok","version":"v1"}

    Os parâmetros usados nesse comando são os seguintes:

    * **-u**: o nome de usuário e a senha usados para autenticar a solicitação
    * **-G**: indica que se trata de uma solicitação GET

    O início da URL, ****https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho, **/status**, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) para o servidor.

2. Use o seguinte para enviar um trabalho de Pig Latin para o cluster:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Os parâmetros usados nesse comando são os seguintes:

    * **-d**: como `-G` não é usado, a solicitação resulta no método POST por padrão. `-d` especifica os valores de dados que são enviados com a solicitação.

        * **user.name**: o usuário que está executando o comando
        * **execute**: as instruções de Pig Latin a executar
        * **statusdir**: diretório no qual o status deste trabalho será gravado

    > [AZURE.NOTE]Observe que os espaços em instruções do Pig Latin são substituídos pelo caractere `+` quando usado com o Curl.

    Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho, por exemplo:

        {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir. Substitua **JOBID** pelo valor retornado na etapa anterior. Por exemplo, se o valor retornado for `{"id":"job_1415651640909_0026"}`, **JOBID** será `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Se o trabalho foi concluído, o estado será **SUCCEEDED**.

    > [AZURE.NOTE]Essa solicitação de Curl retorna um documento JSON (JavaScript Object Notation) com informações sobre o trabalho; jq é usado para recuperar o valor de estado.

##<a id="results"></a>Exibir resultados

Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho do Armazenamento de Blob do Azure. O parâmetro `statusdir` passado com a consulta contém o local do arquivo de saída; nesse caso, ****wasb:///example/pigcurl**. Esse endereço armazena a saída do trabalho no diretório **example/pigcurl** no contêiner de armazenamento padrão usado pelo seu cluster HDInsight.

Você pode listar e baixar esses arquivos usando a [CLI do Azure para Mac, Linux e Windows](../xplat-cli.md). Por exemplo, para listar arquivos em **exemplo/pigcurl**, use o seguinte comando:

	azure storage blob list <container-name> example/pigcurl

Para baixar um arquivo, use o seguinte:

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Você deve especificar o nome da conta de armazenamento que contém o blob usando os parâmetros `-a` e `-k`, ou definir as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**.

##<a id="summary"></a>Resumo

Conforme demonstrado nesse documento, você pode usar a solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos Pig no seu cluster HDInsight.

Para obter mais informações sobre a interface REST usada nesse artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Pig no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO8-->