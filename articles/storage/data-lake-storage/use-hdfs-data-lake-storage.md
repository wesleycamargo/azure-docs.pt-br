---
title: Usando a CLI do HDFS com a Visualização do Azure Data Lake Storage Gen2
description: Introdução ao HDFS CLI para o Data Lake Storage Gen2 Preview
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c59331c772e140fccfefb89eef086a35837171e1
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576968"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Usando a CLI do HDFS com o Data Lake Storage Gen2

A Visualização do Gen2 do Data Storage do Azure Data permite que você gerencie e acesse os dados da mesma forma que faria com um [ Sistema de Arquivos Distribuídos Hadoop (HDFS) ](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Se você tiver um cluster do HDInsight conectado ou executar uma tarefa do Apache Spark usando Azure Databricks para executar análises em dados armazenados no Azure Data Lake Storage Gen2, poderá usar a interface da linha de comandos (CLI) para recuperar e manipular os dados carregados. O restante do artigo descreve as opções que você tem enquanto a equipe do [Azure Storage está trabalhando para adicionar suporte ao Azure Storage Explorer e ao portal do Azure](https://azure.microsoft.com/roadmap/).

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI com HDInsight

O HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o shell que interage diretamente com o HDFS e outros sistemas de arquivos suportados pelo Hadoop. Abaixo estão os comandos mais usados e os links para recursos úteis.

>[!IMPORTANT]
>A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. Cobrança é proporcional por minuto, portanto, você sempre deve excluir o cluster quando ele não estiver mais em uso (Saiba como [excluir um cluster](../../hdinsight/hdinsight-delete-cluster.md)). No entanto, os dados armazenados no Azure Data Lake armazenamento Gen2 persiste mesmo depois de um cluster HDInsight é excluído.

Para obter uma lista de arquivos ou diretórios:

    hdfs dfs -ls <args>
Para criar um diretório:

    hdfs dfs -mkdir [-p] <paths>
Para excluir um arquivo ou diretório:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Agora, vamos cluster HDInsight Hadoop no Linux como um exemplo. Para usar a CLI do HDFS, primeiro você precisa estabelecer [acesso remoto aos serviços](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Se você escolher [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) o código de exemplo do PowerShell seria semelhante ao seguinte:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

A cadeia de caracteres de conexão pode ser encontrada no "SSH + Cluster logon" seção da folha de cluster HDInsight no portal do Azure. As credenciais SSH foram especificadas no momento da criação do cluster.

Para obter mais informações sobre HDFS CLI, consulte o [documentação oficial](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e [HDFS permissões guia](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI com Databricks do Azure

O Databricks fornece um CLI fácil de usar criado sobre a API de REST Databricks. O projeto de código-fonte aberto está hospedado em [GitHub](https://github.com/databricks/databricks-cli). Abaixo estão os comandos usados com frequência.

Para obter uma lista de arquivos ou diretórios:

    dbfs ls [-l]
Para criar um diretório:

    dbfs mkdirs
Para excluir um arquivo:

    dbfs rm [-r]

Outra maneira de interagir com Databricks são blocos de anotações. Enquanto um bloco de anotações tem um idioma principal, você pode misturar idiomas especificando o comando magic % de idiomas no início de uma célula. Especificamente, % sh permite que você execute o código de shell no bloco de anotações muito parecida com o exemplo de HDInsight neste artigo.

Para obter uma lista de arquivos ou diretórios:

    %sh ls <args>
Para criar um diretório:

    %sh mkdir [-p] <paths>
Para excluir um arquivo ou diretório:

    %sh rm [-skipTrash] URI [URI ...]

Depois de iniciar o cluster Spark no Azure Databricks, você criará um novo bloco de anotações. O exemplo de script do bloco de anotações será semelhante ao seguinte:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Para obter mais informações sobre Databricks CLI, consulte o [documentação oficial](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Para obter mais informações sobre anotações, consulte o [notebooks](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) seção da documentação.

## <a name="next-steps"></a>Próximas etapas

- [ Crie um cluster do HDInsight com o Armazenamento de Dados do Azure Data Lake Gen2 ](./quickstart-create-connect-hdi-cluster.md)
- [ Use uma conta compatível com o Azure Data Lake Gen2 em bancos de dados do Azure ](./quickstart-create-databricks-account.md) 