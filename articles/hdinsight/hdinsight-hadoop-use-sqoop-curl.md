<properties
   pageTitle="Usar o Sqoop do Hadoop com Curl no HDInsight | Microsoft Azure"
   description="Saiba como enviar remotamente trabalhos do Sqoop para o HDInsight usando o Curl."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/27/2016"
   ms.author="jgao"/>

#Executar trabalhos do Sqoop com Hadoop no HDInsight com Curl

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Neste documento, você aprenderá como usar o Curl para executar trabalhos do Sqoop em um Hadoop no cluster HDInsight do Azure.

Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar, monitorar e recuperar os resultados de trabalhos do Sqoop. Isso funciona usando a API REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster HDInsight.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for iniciante no HDInsight, consulte [O que você precisa saber sobre o Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

##Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop no cluster HDInsight (baseado em Linux ou Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##Enviar trabalhos do Sqoop usando o Curl

> [AZURE.NOTE] Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. Você também deve usar o nome do cluster como parte do URI (Uniform Resource Identifier) usado para enviar as solicitações ao servidor.
>
> Para os comandos nesta seção, substitua **USERNAME** pelo usuário para autenticar o cluster e substitua **PASSWORD** pela senha da conta de usuário. Substitua **CLUSTERNAME** pelo nome do cluster.
>
> A API REST é protegida por meio de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS (HTTP seguro) para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Você deve receber uma resposta com esta aparência:

        {"status":"ok","version":"v1"}

    Os parâmetros usados nesse comando são os seguintes:

    * **-u** - o nome de usuário e a senha usada para autenticar a solicitação.
    * **-G** - indica que se trata de uma solicitação GET.

    O início da URL, ****https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho **/status**, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) ao servidor.

2. Use o seguinte para enviar um trabalho do sqoop:


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Os parâmetros usados nesse comando são os seguintes:

    * **-d** - como `-G` não é usado, a solicitação resulta no método POST por padrão. `-d` especifica os valores de dados que são enviados com a solicitação.

        * **user.name** - o usuário que está executando o comando.

        * **command** - o comando Sqoop a ser executado.

        * **statusdir** - o diretório no qual o status deste trabalho será gravado.

    Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho.

        {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir. Substitua **JOBID** pelo valor retornado na etapa anterior. Por exemplo, se o valor retornado for `{"id":"job_1415651640909_0026"}`, **JOBID** será `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Se o trabalho foi concluído, o estado será **SUCCEEDED**.

    > [AZURE.NOTE] Essa solicitação de Curl retorna um documento JSON (JavaScript Object Notation) com informações sobre o trabalho; jq é usado para recuperar o valor de estado.

4. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho do Armazenamento de Blobs do Azure. O parâmetro `statusdir` passado com a consulta contém o local do arquivo de saída; nesse caso, ****wasb:///example/curl**. Esse endereço armazena a saída do trabalho no diretório **example/curl** do contêiner de armazenamento padrão usado pelo cluster HDInsight.

    Você pode listar e baixar esses arquivos usando a [CLI do Azure](../xplat-cli-install.md). Por exemplo, para listar arquivos em **example/curl**, use o seguinte comando:

		azure storage blob list <container-name> example/curl

	Para baixar um arquivo, use o seguinte:

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] Você deve especificar o nome da conta de armazenamento que contém o blob usando os parâmetros `-a` e `-k` ou definir as variáveis de ambiente **AZURE\\_STORAGE\\_ACCOUNT** e **AZURE\\_STORAGE\\_ACCESS\\_KEY**. Consulte <a href="hdinsight-upload-data.md" target="\_blank"para obter mais informações.


##Resumo

Conforme demonstrado neste documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos do Sqoop no cluster HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, confira o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guia da API REST do Sqoop</a>.

##Próximas etapas

Para obter informações gerais sobre o Hive com HDInsight:

* [Usar o Sqoop com o Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

<!---HONumber=AcomDC_0601_2016-->