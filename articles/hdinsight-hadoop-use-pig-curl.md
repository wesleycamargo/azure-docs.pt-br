<properties
   pageTitle="Usar o Pig do Hadoop no HDInsight | Azure"
   description="Aprenda a usar o Pig com Hadoop no HDInsight usando o SSH."
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
 
#Execute trabalhos do Pig com Hadoop no HDInsight usando o Curl

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

Neste documento, você aprenderá como usar o Curl para executar trabalhos do Pig Latin em um cluster HDInsight. A linguagem de programação Pig Latin permite descrever as transformações que são aplicadas aos dados de entrada para produzir a saída desejada.

Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar, monitorar e recuperar os resultados de trabalhos do Pig. Isso funciona usando a API REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster HDInsight.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas é novo no HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">O que você precisa saber sobre Hadoop baseado em Linux no HDInsight</a>.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster Azure HDInsight (Hadoop no HDInsight) (baseado em Linux ou Windows)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

##<a id="curl"></a>Executar trabalhos do Pig usando Curl

> [AZURE.NOTE] Ao usar o Curl ou quaisquer outras comunicações do REST com WebHCat, deve autenticar as solicitações fornecendo o nome de usuário de administrador de cluster HDInsight e a senha. Você também deve usar o nome do cluster como parte do URI usado para enviar as solicitações para o servidor.
> 
> Para os comandos nesta seção, substitua **USERNAME** com o usuário para autenticar o cluster e **PASSWORD** com a senha da conta de usuário. Substitua **CLUSTERNAME** com o nome do cluster.
> 
> A API REST é protegida usando <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">autenticação básica</a>. Você deve sempre fazer solicitações usando HTTPS para garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight. 

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Você deve receber uma resposta com esta aparência.

        {"status":"ok","version":"v1"}

    Os parâmetros usados nesse comando são os seguintes.

    * **-u** - o nome de usuário e a senha usada para autenticar a solicitação
    * **-G** - indica que se trata de uma solicitação GET

    O início da url, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho **/status**, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) para o servidor.

2. Use o seguinte para enviar um trabalho de Pig Latin para o cluster.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Os parâmetros usados nesse comando são os seguintes.

    * **-d** - como `- G` não é usado, a solicitação padrão é o método POST. `-d` especifica os valores de dados que são enviados com a solicitação

        * **user.name** - o usuário que está executando o comando
        * **execute** -as instruções de Pig Latin para executar
        * **statusdir** - o diretório no qual o status para esse trabalho será gravado

    > [AZURE.NOTE] Observe que os espaços em instruções do Pig Latin são substituídos pelo caractere `+` quando usado com o Curl.

    Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho.

        {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir. Substitua o **JOBID** com o valor retornado na etapa anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, o JOBID será `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Se o trabalho for concluído, o estado será "SUCCEEDED".

    > [AZURE.NOTE] Essa solicitação de Curl retorna um documento JSON com informações sobre o trabalho; jq é usado para recuperar o valor de estado. 

##<a id="results"></a>Exibir resultados

Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho do Armazenamento de BLOBs do Azure. O parâmetro  `statusdir` transmitido com a consulta contém o local do arquivo de saída; nesse caso, **wasb:///example/pigcurl**. Esse endereço armazena a saída do trabalho no diretório **example/pigcurl** no contêiner de armazenamento padrão usado pelo seu cluster HDInsight.

Você pode listar e baixar esses arquivos usando a <a href="../xplat-cli/" target="_blank">Interface de Linha de Comando de Plataforma Cruzada do Azure (xplat-cli)</a>. Por exemplo, para listar arquivos no **exemplo/pigcurl**, use o seguinte comando.

	azure storage blob list <container-name> example/pigcurl

Para baixar um arquivo, use o seguinte.

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Você deve especificar o nome da conta de armazenamento que contém o blob usando os parâmetros `-a` e `-k` ou definir as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**. Consulte <a href="../hdinsight-upload-data/" target="_blank" for more information.


##<a id="summary"></a>Resumo

Conforme demonstrado nesse documento, você pode usar a solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos Pig no seu cluster do HDInsight.

Para obter mais informações sobre a interface REST usada nesse artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Pig no HDInsight.

* [Usar o Pig com Hadoop no HDInsight](../hdinsight-use-pig/)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight.

* [Usar o Hive com Hadoop no HDInsight](../hdinsight-use-hive/)

* [Usar o MapReduce com Hadoop no HDInsight](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
