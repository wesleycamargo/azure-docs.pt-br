<properties
   pageTitle="Usar o Hive do Hadoop com Curl no HDInsight | Microsoft Azure"
   description="Saiba como enviar remotamente trabalhos do Pig para o HDInsight usando o Curl."
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
   ms.date="10/09/2015"
   ms.author="larryfr"/>

#Executar consultas Hive com Hadoop no HDInsight com Curl

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste documento, você aprenderá como usar o Curl para executar consultas Hive em um Hadoop no cluster HDInsight do Azure.

O Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar, monitorar e recuperar os resultados de consultas do Pig. Isso funciona usando a API REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster HDInsight.

> [AZURE.NOTE]Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for iniciante no HDInsight, consulte [O que você precisa saber sobre o Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop no cluster HDInsight (baseado em Linux ou Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Executar consultas Hive usando o Curl

> [AZURE.NOTE]Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. Você também deve usar o nome do cluster como parte do URI (Uniform Resource Identifier) usado para enviar as solicitações ao servidor.
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

    O início da URL, ****https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho **/status**, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) ao servidor. Você também pode solicitar a versão do Hive usando o comando a seguir:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Isso deve retornar uma resposta semelhante à seguinte:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Use o seguinte para criar uma nova tabela chamada **log4jLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Os parâmetros usados nesse comando são os seguintes:

    * **-d** - como `-G` não é usado, a solicitação resulta no método POST por padrão. `-d` especifica os valores de dados que são enviados com a solicitação.

        * **user.name** - o usuário que está executando o comando.

        * **execute** - as instruções do HiveQL a executar.

        * **statusdir** - o diretório no qual o status deste trabalho será gravado.

    Essas instruções executam as seguintes ações:

    * **DROP TABLE** - exclui a tabela e o arquivo de dados, caso a tabela já exista.

    * **CREATE EXTERNAL TABLE** - cria uma nova tabela “externa" em Hive. As tabelas externas armazenam apenas a definição da tabela no Hive. Os dados são mantidos no local original.

		> [AZURE.NOTE]As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um processo automático de carregamento de dados, ou outra operação MapReduce, mas sempre quer que as consultas Hive utilizem os dados mais recentes.
		>
		> Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

    * **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.

    * **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.

    > [AZURE.NOTE]Observe que os espaços entre as instruções HiveQL são substituídos pelo caractere `+` quando usados com o Curl. Os valores entre aspas que contêm um espaço, como o delimitador, não devem ser substituídos por `+`.

    * **INPUT\_\_FILE\_\_NAME LIKE '%25.log'** - isso limita a pesquisa para usar somente os arquivos que terminam em .log. Se não estiver presente, o Hive tentará pesquisar todos os arquivos nesse diretório e em seus subdiretórios, incluindo os arquivos que não correspondem ao esquema de coluna definido para essa tabela.

    > [AZURE.NOTE]Observe que %25 é o formato codificado de URL de %, então, a condição real é `like '%.log'`. O % deve ser codificado em URL, pois será tratado como um caractere especial em URLs.

    Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho.

        {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir. Substitua **JOBID** pelo valor retornado na etapa anterior. Por exemplo, se o valor retornado for `{"id":"job_1415651640909_0026"}`, **JOBID** será `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Se o trabalho foi concluído, o estado será **SUCCEEDED**.

    > [AZURE.NOTE]Essa solicitação de Curl retorna um documento JSON (JavaScript Object Notation) com informações sobre o trabalho; jq é usado para recuperar o valor de estado.

4. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho do Armazenamento de Blobs do Azure. O parâmetro `statusdir` passado com a consulta contém o local do arquivo de saída; nesse caso, ****wasb:///example/curl**. Esse endereço armazena a saída do trabalho no diretório **example/curl** do contêiner de armazenamento padrão usado pelo cluster HDInsight.

    Você pode listar e baixar esses arquivos usando a [CLI do Azure para Mac, Linux e Windows](xplat-cli-install.md). Por exemplo, para listar arquivos em **example/curl**, use o seguinte comando:

		azure storage blob list <container-name> example/curl

	Para baixar um arquivo, use o seguinte:

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE]Você deve especificar o nome da conta de armazenamento que contém o blob usando os parâmetros `-a` e `-k` ou definir as variáveis de ambiente **AZURE\\_STORAGE\\_ACCOUNT** e **AZURE\\_STORAGE\\_ACCESS\\_KEY**. Consulte <a href="hdinsight-upload-data.md" target="\_blank"para obter mais informações.

6. Use as instruções a seguir para criar uma nova tabela "interna" chamada **errorLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Essas instruções executam as seguintes ações:

    * **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é usada, esta é uma tabela interna, que é armazenada no data warehouse do Hive e totalmente gerenciada pelo Hive.

		> [AZURE.NOTE]Ao contrário das tabelas externas, remover uma tabela interna excluirá também os dados subjacentes.

    * **STORES AS ORC**: armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
    * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.
    * **SELECT** - seleciona todas as linhas da nova tabela **errorLogs**.

7. Use a ID de trabalho retornada para verificar o status do trabalho. Quando a tiver utilizado com sucesso, use a CLI do Azure para Mac, Linux e Windows conforme descrito anteriormente para baixar e exibir os resultados. A saída deve conter três linhas, todos os quais contêm **[ERROR]**.


##<a id="summary"></a>Resumo

Conforme demonstrado nesse documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos do Hive no cluster do HDInsight.

Para obter mais informações sobre a interface REST usada nesse artigo, consulte a <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">Referência de WebHCat</a>.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Hive com HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

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



[hdinsight-storage]: ../hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[Powershell-install-configure]: ../install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=Oct15_HO3-->