---
title: "Usar o Sqoop do Hadoop com Curl no HDInsight – Azure | Microsoft Docs"
description: Saiba como enviar remotamente trabalhos do Sqoop para o HDInsight usando o Curl.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 0975aedf58c6e110726dd3308eae5f9ad3907cc7
ms.contentlocale: pt-br
ms.lasthandoff: 07/28/2017

---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Executar trabalhos do Sqoop com Hadoop no HDInsight com Curl
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Curl para executar trabalhos do Sqoop em um cluster Hadoop no HDInsight.

Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar, monitorar e recuperar os resultados de trabalhos do Sqoop. Isso funciona usando a API REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster HDInsight.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas não conhece o HDInsight, confira [Informações sobre o uso do HDInsight no Linux](hdinsight-hadoop-linux-information.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop no cluster HDInsight (baseado em Linux ou Windows)
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a name="submit-sqoop-jobs-by-using-curl"></a>Enviar trabalhos do Sqoop usando o Curl
> [!NOTE]
> Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. Você também deve usar o nome do cluster como parte do URI (Uniform Resource Identifier) usado para enviar as solicitações ao servidor.
> 
> Para os comandos nesta seção, substitua **USERNAME** pelo usuário para autenticar o cluster e substitua **PASSWORD** pela senha da conta de usuário. Substitua **CLUSTERNAME** pelo nome do cluster.
> 
> A API REST é protegida por meio de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS (HTTP seguro) para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor.
> 
> 

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    Você deve receber uma resposta com esta aparência:
   
        {"status":"ok","version":"v1"}
   
    Os parâmetros usados nesse comando são os seguintes:
   
   * **-u** - o nome de usuário e a senha usada para autenticar a solicitação.
   * **-G** - indica que se trata de uma solicitação GET.
     
     O início da URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho, **/status**, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) ao servidor. 
2. Use o seguinte para enviar um trabalho do sqoop:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Os parâmetros usados nesse comando são os seguintes:

    * **-d** – uma vez que `-G` não é usado; a solicitação padrão é o método POST. `-d` especifica os valores de dados que são enviados com a solicitação.

        * **user.name** - o usuário que está executando o comando.

        * **command** - o comando Sqoop a ser executado.

        * **statusdir** - o diretório no qual o status deste trabalho será gravado.

    Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho.

        {"id":"job_1415651640909_0026"}

1. Para verificar o status do trabalho, use o comando a seguir. Substitua **JOBID** pelo valor retornado na etapa anterior. Por exemplo, se o valor retornado for **, `{"id":"job_1415651640909_0026"}`JOBID** será `job_1415651640909_0026`.
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    Se o trabalho foi concluído, o estado será **SUCCEEDED**.
   
   > [!NOTE]
   > Essa solicitação de Curl retorna um documento JSON (JavaScript Object Notation) com informações sobre o trabalho; jq é usado para recuperar o valor de estado.
   > 
   > 
2. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho no Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém o local do arquivo de saída; nesse caso, **wasb:///example/curl**. Esse endereço armazena a saída do trabalho no diretório **example/curl** do contêiner de armazenamento padrão usado pelo cluster HDInsight.
   
    Você pode listar e baixar esses arquivos usando a [CLI do Azure](../cli-install-nodejs.md). Por exemplo, para listar arquivos em **example/curl**, use o seguinte comando:
   
        azure storage blob list <container-name> example/curl
   
    Para baixar um arquivo, use o seguinte:
   
        azure storage blob download <container-name> <blob-name> <destination-file>
   
   > [!NOTE]
   > Você deve especificar o nome da conta de armazenamento que contém o blob usando os parâmetros `-a` e `-k` ou definir as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**. Consulte <a href="hdinsight-upload-data.md" target="_blank"para obter mais informações.
   > 
   > 

## <a name="limitations"></a>Limitações
* Exportação em massa — com HDInsight baseado em Linux, o conector Sqoop usado para exportar dados no Microsoft SQL Server ou no Banco de Dados SQL do Azure, atualmente, não permite inserções em massa.
* Envio em lote — com HDInsight baseado em Linux, ao usar o comutador `-batch` na execução de inserções, Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="summary"></a>Resumo
Conforme demonstrado neste documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos do Sqoop no cluster HDInsight.

Para saber mais sobre a interface REST usada neste artigo, confira o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Guia da API REST do Sqoop</a>.

## <a name="next-steps"></a>Próximas etapas
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




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx



