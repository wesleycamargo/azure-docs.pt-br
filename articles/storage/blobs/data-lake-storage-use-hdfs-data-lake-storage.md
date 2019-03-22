---
title: Usando a CLI do HDFS com o Azure Data Lake Storage Gen2
description: Introdução à CLI do HDFS para o Data Lake Storage Gen2
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: c77981574ff2e507af7012f26f742dda62d952fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57991959"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Usando a CLI do HDFS com o Data Lake Storage Gen2

O Azure Data Lake Storage Gen2 permite gerenciar e acessar os dados como seria feito com um [Sistema de Arquivos Distribuído Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Se você tiver um cluster do HDInsight conectado ou executar um trabalho do Apache Spark usando as Chaves de dados do Azure para executar análises em dados armazenados em uma conta do Armazenamento do Azure, poderá usar a interface da linha de comando (CLI) para recuperar e manipular os dados carregados.

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI com HDInsight

O HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o shell que interage diretamente com o HDFS e outros sistemas de arquivos suportados pelo Hadoop. Abaixo estão os comandos mais usados e os links para recursos úteis.

>[!IMPORTANT]
>O faturamento de cluster do HDInsight é iniciado depois que um cluster é criado e pára quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado. Para saber como excluir um cluster, consulte o nosso artigo [sobre o tópico](../../hdinsight/hdinsight-delete-cluster.md). No entanto, os dados armazenados em uma conta de armazenamento com o Data Lake Storage Gen2 ativado persistem mesmo depois que um cluster do HDInsight é excluído.

### <a name="create-a-file-system"></a>Criar um sistema de arquivos

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Substitua o espaço reservado `<file-system-name>` pelo nome que você deseja dar ao sistema de arquivos.

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

### <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

    hdfs dfs -ls <path>

Substitua o espaço reservado `<path>` pelo URI do sistema de arquivos ou pela pasta do sistema de arquivos.

Por exemplo: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

### <a name="create-a-directory"></a>Criar um diretório

    hdfs dfs -mkdir [-p] <path>

Substitua o espaço reservado `<path>` pelo nome do sistema de arquivos raiz ou por uma pasta do sistema de arquivos.

Por exemplo: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

### <a name="delete-a-file-or-directory"></a>Excluir um arquivo ou diretório

    hdfs dfs -rm <path>

Substitua o espaço reservado `<path>` pelo URI do arquivo ou pasta que você quer excluir.

Por exemplo: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Usar a CLI do HDFS com um cluster HDInsight Hadoop no Linux

Primeiro, estabeleça o [acesso remoto aos serviços](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Se você escolher [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) o código de exemplo do PowerShell seria semelhante ao seguinte:

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A cadeia de caracteres de conexão pode ser encontrada no "SSH + Cluster logon" seção da folha de cluster HDInsight no portal do Azure. As credenciais SSH foram especificadas no momento da criação do cluster.

Para obter mais informações sobre HDFS CLI, consulte o [documentação oficial](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e [HDFS permissões guia](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html). Para saber mais sobre as ACLs no Databricks, consulte a [CLI de Segredos](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli).

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI com Databricks do Azure

O Databricks fornece um CLI fácil de usar criado sobre a API de REST Databricks. O projeto de código-fonte aberto está hospedado em [GitHub](https://github.com/databricks/databricks-cli). Abaixo estão os comandos usados com frequência.

### <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

    dbfs ls [-l]

### <a name="create-a-directory"></a>Criar um diretório

    dbfs mkdirs

### <a name="delete-a-file"></a>Excluir um arquivo

    dbfs rm [-r]

Outra maneira de interagir com Databricks são blocos de anotações. Enquanto um bloco de anotações tem um idioma principal, você pode misturar idiomas especificando o comando magic % de idiomas no início de uma célula. Especificamente, % sh permite que você execute o código de shell no bloco de anotações muito parecida com o exemplo de HDInsight neste artigo.

### <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

    %sh ls <args>

### <a name="create-a-directory"></a>Criar um diretório

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Excluir um arquivo ou diretório

    %sh rm [-skipTrash] URI [URI ...]

Depois de iniciar o cluster Spark no Azure Databricks, você criará um novo bloco de anotações. O exemplo de script do bloco de anotações será semelhante ao seguinte:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Para obter mais informações sobre Databricks CLI, consulte o [documentação oficial](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Para obter mais informações sobre anotações, consulte o [notebooks](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) seção da documentação.

## <a name="set-file-and-directory-level-permissions"></a>Definir permissões de nível de diretório e arquivo

Você define e obtém permissões de acesso no nível de diretório e arquivo. Aqui, são apresentados alguns comandos para você começar. 

Para saber mais sobre permissões de nível de diretório e arquivo para o sistema de arquivos do Azure Data Lake Gen2, consulte [Controle de acesso no Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Exibir as ACLs (Lista de Controle de Acesso) de arquivos e diretórios

    hdfs dfs -getfacl [-R] <path>

Exemplo:

`hdfs dfs -getfacl -R /dir`

Consulte [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>Definir ACLs de arquivos e diretórios

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exemplo:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Consulte [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Alterar o proprietário dos arquivos

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Consulte [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Alterar associação de grupos de arquivos

    hdfs dfs -chgrp [-R] <group> <URI>

Consulte [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>Alterar as permissões dos arquivos

    hdfs dfs -chmod [-R] <mode> <URI>

Consulte [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

É possível exibir a lista completa de comandos no site [Guia do shell do sistema de arquivos do Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="next-steps"></a>Próximas etapas

[ Use uma conta compatível com o Azure Data Lake Gen2 em bancos de dados do Azure ](./data-lake-storage-quickstart-create-databricks-account.md) 
