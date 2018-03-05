---
title: "Usar o Hive do Hadoop com Curl no HDInsight – Azure | Microsoft Docs"
description: Saiba como enviar remotamente trabalhos do Pig para o HDInsight usando o Curl.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: dfe9efdb57a0ce2506abd251267f39020568d081
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Executar consultas Hive com Hadoop no HDInsight usando REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como usar a API REST do WebHCat para executar consultas Hive com o Hadoop no cluster HDInsight do Azure.

## <a name="prerequisites"></a>pré-requisitos

* Um Hadoop baseado em Linux no cluster HDInsight versão 3.4 ou maior.

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Um cliente REST. Este documento usa exemplos de Windows PowerShell e [Curl](http://curl.haxx.se/).

    > [!NOTE]
    > O Azure PowerShell fornece cmdlets dedicados para trabalhar com o Hive no HDInsight. Para obter mais informações, consulte o documento [Usar Hive com o Azure PowerShell](apache-hadoop-use-hive-powershell.md).

Este documento também usa o Windows PowerShell e [Jq](http://stedolan.github.io/jq/) para processar os dados JSON retornados de solicitações REST.

## <a id="curl"></a>Executar um trabalho do Hive

> [!NOTE]
> Ao usar o cURL ou qualquer outra comunicação REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight.
>
> A API REST é protegida por meio de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor, sempre faça solicitações usando HTTPS (HTTP seguro).

1. Para definir o logon de cluster que é usado pelos scripts neste documento, use um dos seguintes comandos:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Para definir o nome do cluster, use um dos seguintes comandos:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Para verificar se você pode se conectar ao cluster HDInsight, use os seguintes comandos:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Você deve receber uma resposta semelhante ao texto a seguir:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Os parâmetros usados nesse comando são os seguintes:

    * `-u` - O nome de usuário e a senha usados para autenticar a solicitação.
    * `-G` - Indica que essa solicitação é uma operação GET.

   O início da URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todas as solicitações. O caminho, `/status`, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) ao servidor. Você também pode solicitar a versão do Hive usando o comando a seguir:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Essa solicitação retorna uma resposta semelhante ao seguinte texto:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Use o seguinte para criar uma tabela chamada **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Essa solicitação usa o método POST, que envia dados como parte da solicitação para a API REST. Os valores de dados a seguir são enviados com a solicitação:

     * `user.name` - O usuário que está executando o comando.
     * `execute` - As instruções do HiveQL a executar.
     * `statusdir` - O diretório no qual o status deste trabalho é gravado.

   Essas instruções executam as seguintes ações:
   
   * `DROP TABLE` - Se a tabela já existir, ela será excluída.
   * `CREATE EXTERNAL TABLE` - Cria uma nova tabela ‘externa’ no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive. Os dados são mantidos no local original.

     > [!NOTE]
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizados ou outra operação MapReduce.
     >
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

   * `ROW FORMAT` – o modo como os dados são formatados. Os campos em cada log são separados por um espaço.
   * `STORED AS TEXTFILE LOCATION` - O local em que os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
   * `SELECT` – Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Essa instrução retorna um valor de **3**, visto que há três linhas que contêm esse valor.

     > [!NOTE]
     > Observe que os espaços entre as instruções HiveQL são substituídos pelo caractere `+` quando usados com o Curl. Os valores entre aspas que contêm um espaço, como o delimitador, não devem ser substituídos por `+`.

      Esse comando retorna uma ID de trabalho que pode ser usada para verificar o status do trabalho.

5. Para verificar o status do trabalho, use o comando a seguir:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Se o trabalho foi concluído, o estado será **SUCCEEDED**.

6. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho no Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém a localização do arquivo de saída; nesse caso, `/example/rest`. Esse endereço armazena a saída do diretório `example/curl` no armazenamento padrão de clusters.

    Você pode listar e baixar esses arquivos usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como usar a CLI do Azure com o Armazenamento do Azure, consulte o armazenamento [Usar a CLI 2.0 do Azure com o Armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

## <a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Hive com HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se você estiver usando o Tez com o Hive, consulte os seguintes documentos para as informações de depuração:

* [Usar a exibição de Ambari Tez no HDInsight baseado em Linux](../hdinsight-debug-ambari-tez-view.md)

Para obter mais informações sobre a API REST usada nesse documento, consulte o documento [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

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
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


