---
title: Use o Apache Hadoop Pig com REST no HDInsight - Azure
description: Aprenda a usar o REST para executar tarefas do Pig Latin em um cluster do Apache Hadoop no Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 855ee1b7396be97c6529480b8fa8200bb8167ee6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433995"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>Execute trabalhos do Apache Pig com o Apache Hadoop no HDInsight usando o REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Saiba como executar tarefas do Apache Pig Latin fazendo solicitações REST para um cluster do Azure HDInsight. O Curl é usado para demonstrar como você pode interagir com o HDInsight usando a API REST do WebHCat.

> [!NOTE]  
> Se você já estiver familiarizado com o uso de servidores Apache Hadoop baseados em Linux, mas for novo no HDInsight, consulte [Dicas do HDInsight baseadas em Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Pré-requisitos

* Um cluster do Azure HDInsight (Hadoop no HDInsight, baseado em Windows ou Linux)

  > [!IMPORTANT]  
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Executar trabalhos do Apache Pig usando Curl


> [!NOTE]
> A API REST é protegida por meio de [autenticação básica de acesso](https://en.wikipedia.org/wiki/Basic_access_authentication). Para assegurar que suas credenciais sejam enviadas com segurança para o servidor, sempre faça solicitações usando HTTPS (HTTP seguro).
>
> Ao usar os comandos nesta seção, substitua `USERNAME` pelo usuário para autenticar o cluster e substitua `PASSWORD` pela senha da conta de usuário. Substitua `CLUSTERNAME` pelo nome do cluster.
>


1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Você deve receber a resposta JSON a seguir:

        {"status":"ok","version":"v1"}

    Os parâmetros usados nesse comando são os seguintes:

    * **-u**: O nome de usuário e a senha usados para autenticar a solicitação
    * **-G**: Indica que essa solicitação é uma solicitação GET

     O início da URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todas as solicitações. O caminho, **/status**, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) para o servidor.

2. Use o seguinte para enviar um trabalho de Pig Latin para o cluster:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Os parâmetros usados nesse comando são os seguintes:

    * **-d**: Como `-G` não é usado; a solicitação padrão é o método POST. `-d` especifica os valores de dados que são enviados com a solicitação.

    * **user.name**: O usuário que está executando o comando
    * **executar**: As instruções de Pig Latin a executar
    * **statusdir**: O diretório no qual o status deste trabalho é gravado

    > [!NOTE]  
    > Observe que os espaços em instruções do Pig Latin são substituídos pelo caractere `+` quando usado com o Curl.

    Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho, por exemplo:

        {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Substitua `JOBID` pelo valor retornado na etapa anterior. Por exemplo, se o valor retornado era `{"id":"job_1415651640909_0026"}`, então `JOBID` é `job_1415651640909_0026`.

    Se o trabalho foi concluído, o estado será **SUCCEEDED**.

    > [!NOTE]  
    > Essa solicitação de Curl retorna um documento JSON (JavaScript Object Notation) com informações sobre o trabalho; jq é usado para recuperar o valor de estado.

## <a id="results"></a>Exibir resultados

Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho. O parâmetro `statusdir` transmitido com a consulta contém a localização do arquivo de saída; nesse caso, `/example/pigcurl`.

O HDInsight pode usar o Armazenamento do Microsoft Azure ou o Azure Data Lake Store como o armazenamento de dados padrão. Há várias maneiras de obter os dados, dependendo de qual deles você usa. Para obter mais informações, consulte a seção armazenamento do documento [Informações do HDInsight baseado em Linux](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store).

## <a id="summary"></a>Resumo

Conforme demonstrado nesse documento, você pode usar a solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos Pig no seu cluster HDInsight.

Para obter mais informações sobre a interface REST usada nesse artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Pig no HDInsight:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar MapReduce com Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
