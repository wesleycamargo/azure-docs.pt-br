---
title: Consultar dados do armazenamento do Azure compatível com o HDFS - Azure HDInsight
description: Aprenda a consultar dados do Armazenamento do Azure e do Azure Data Lake Storage para armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 24e0b61dfd9950a5c5990f8341e32d048453c5d6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689575"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Usar o Armazenamento do Azure com clusters HDInsight

Para analisar dados em um cluster HDInsight, você pode armazenar os dados ou no [armazenamento do Azure](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[armazenamento do Azure Data Lake Gen 2](../storage/blobs/data-lake-storage-introduction.md), ou uma combinação. Essas opções de armazenamento permitem que você excluir com segurança os clusters de HDInsight que são usados para cálculo sem perder dados do usuário.

O Apache Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica esquema e autoridade padrões. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão ou, com o HDInsight 3.6, você pode selecionar o Armazenamento do Azure ou o Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 como o sistema de arquivos padrão com algumas exceções. Para o suporte do uso do Data Lake Storage Gen 1 como o armazenamento padrão e o vinculado, veja [Disponibilidade para o cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Neste artigo, você aprenderá como funciona o Armazenamento do Azure com clusters HDInsight. Para saber como o Data Lake Storage Gen 1 funciona com clusters HDInsight, veja [Usar o Azure Data Lake Storage com clusters Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Para saber mais sobre a criação de um cluster HDInsight, consulte [Criar clusters do Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

O Armazenamento do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. O HDInsight pode usar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão para o cluster. Através de uma interface HDFS (Sistema de Arquivos Distribuído Hadoop), o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados estruturados ou não estruturados armazenados como blobs.

> [!WARNING]  
> Tipo de conta de armazenamento **BlobStorage** só pode ser usado como armazenamento secundário para clusters do HDInsight.

| Tipo de conta de armazenamento | Serviços com suporte | Níveis de desempenho compatíveis | Camadas de acesso compatíveis |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (uso geral v2)  | Blob     | Standard                    | Frequente, esporádico, arquivo morto\*   |
| Armazenamento (uso geral v1)   | Blob     | Standard                    | N/D                    |
| BlobStorage                    | Blob     | Standard                    | Frequente, esporádico, arquivo morto\*   |

Não recomendamos o contêiner de blobs padrão para armazenar dados corporativos. É uma prática recomendada excluir o contêiner de blobs padrão após cada uso para reduzir o custo de armazenamento. O contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

Não há suporte para o compartilhamento de um contêiner de blobs como o sistema de arquivos padrão para vários clusters.

> [!NOTE]  
> A camada de acesso aos arquivos é um tipo offline que tem uma latência de recuperação de várias horas e não é recomendada para uso com o HDInsight. Para saber mais, consulte [Camada de acesso aos arquivos](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Se você optar por proteger a conta de armazenamento com o **Firewalls e redes virtuais** restrições de **redes selecionadas**, não se esqueça de habilitar a exceção **permitir que os Microsoft confiáveis serviços...**  para que o HDInsight pode acessar sua conta de armazenamento.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight
O diagrama a seguir fornece uma exibição abstrata da arquitetura de armazenamento do HDInsight de uso do Armazenamento do Azure:

![Clusters Hadoop usam a API HDFS para acessar e armazenar dados estruturados e não estruturados no armazenamento de blobs.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Arquitetura de Armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Além disso, o HDInsight permite que você acesse os dados armazenados no Armazenamento do Azure. A sintaxe do é:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Veja algumas considerações ao usar a conta de armazenamento do Azure com clusters HDInsight.

* **Contêineres nas contas de armazenamento conectadas a um cluster:** Como o nome e a chave da conta estão associados ao cluster durante a criação, você tem acesso total aos blobs nesses contêineres.

* **Contêineres públicos ou blobs públicos em contas de armazenamento que NÃO estão conectadas a um cluster:** Você tem permissão somente leitura para os blobs nos contêineres.
  
> [!NOTE]  
> Um contêiner público permite obter uma lista de todos os blobs disponíveis nesse contêiner e obter metadados do contêiner. Um blob público somente permite acessar os blobs se você souber a URL exata. Para obter mais informações, veja [Gerenciar o acesso a contêineres e blobs](../storage/blobs/storage-manage-access-to-resources.md).

* **Contêineres privados em contas de armazenamento que NÃO estão conectadas a um cluster:** Não é possível acessar os blobs nos contêineres, a menos que você defina a conta de armazenamento ao enviar trabalhos do WebHCat. Isso será explicado mais adiante neste artigo.

As contas de armazenamento que são definidas no processo de criação e suas chaves são armazenadas em `%HADOOP_HOME%/conf/core-site.xml` em nós de cluster. O comportamento padrão do HDInsight é usar as contas de armazenamento definidas no arquivo core-site.xml. Você pode modificar essa configuração usando o [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos do WebHCat, incluindo o Apache Hive, MapReduce, streaming do Apache Hadoop e o Apache Pig, podem conter uma descrição de contas de armazenamento e metadados. (Isso funciona atualmente com o Pig para contas de armazenamento, mas não para metadados.) Para obter mais informações, consulte [Usando um Cluster HDInsight com metastores e contas de armazenamento alternativas](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser usados para dados estruturados e não estruturados. Os contêineres de blob armazenam dados como pares de chave/valor, e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de *entrada* real, mas, devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo.

## <a id="benefits"></a>Benefícios do Armazenamento do Azure
O custo de desempenho implícito de não colocação de clusters de cálculo e recursos de armazenamento é reduzido pela maneira como os clusters de cálculo são criados próximos aos recursos de conta de armazenamento na região do Azure, no qual a rede de alta velocidade torna eficiente para o nós para acessar os dados no armazenamento do Azure de computação.

Há vários benefícios associados ao armazenamento de dados no Armazenamento do Azure em vez de no HDFS:

* **Reutilização e compartilhamento de dados:** Os dados no HDFS estão localizados dentro do cluster de computação. Apenas os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Os dados no armazenamento do Azure podem ser acessados por meio de APIs HDFS ou por meio de [APIs de REST do armazenamento de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Assim, um conjunto maior de aplicativos (incluindo outros clusters HDInsight) e ferramentas podem ser usados para produzir e consumir os dados.

* **Arquivamento de dados:** Armazenar dados no armazenamento do Azure permite que os clusters HDInsight usados para computação sejam excluídos com segurança sem perder dados do usuário.

* **Custo de armazenamento de dados:** Armazenar dados no DFS a longo prazo é mais caro do que armazenar os dados no armazenamento do Azure porque o custo de um cluster de computação é maior que o custo do armazenamento do Azure. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de computação, você está economizando em custos de carregamento de dados.

* **Expansão elástica:** Embora o HDFS forneça um sistema de arquivos dimensionado, a escala é determinada pelo número de nós que você cria para o cluster. A alteração da escala pode se tornar um processo mais complicado do que depender dos recursos de dimensionamento elástico do Armazenamento do Azure que você obtém automaticamente.

* **Replicação geográfica:** O armazenamento do Azure pode ser replicado geograficamente. Embora isso forneça redundância de dados e recuperação geográfica, um failover para o local replicado geograficamente afetará seriamente o desempenho e poderá incorrer em custos adicionais. Portanto, nossa recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados compensar o custo adicional.

Determinados trabalhos e pacotes do MapReduce podem criar resultados intermediários que você não deseja realmente armazenar no contêiner de Armazenamento do Azure. Nesse caso, você ainda pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight usa o DFS para vários desses resultados intermediários em trabalhos Hive e outros processos.

> [!NOTE]  
> A maioria dos comandos HDFS (por exemplo, `ls`, `copyFromLocal` e `mkdir`) ainda funciona conforme o esperado. Apenas os comandos específicos à implementação nativa do HDFS (que é conhecida como DFS), como `fschk` e `dfsadmin` mostram um comportamento diferente no Armazenamento do Azure.

## <a name="address-files-in-azure-storage"></a>Arquivos de endereços no armazenamento do Azure
O esquema de URI para acessar arquivos no Armazenamento do Azure pelo HDInsight é:

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

O esquema de URI fornece acesso sem criptografia (com o prefixo *wasb:*) e acesso criptografado SSL (com *wasbs*). Recomendamos usar *wasbs* sempre que possível, mesmo ao acessar dados que residem dentro da mesma região do Azure.

O `<BlobStorageContainerName>` identifica o nome do contêiner de BLOBs no armazenamento do Azure.
O `<StorageAccountName>` identifica o nome da conta de armazenamento do Azure. Um FQDN (nome de domínio totalmente qualificado) é necessário.

Se nem `<BlobStorageContainerName>` nem `<StorageAccountName>` tiver sido especificado, o sistema de arquivos padrão é usado. Para os arquivos no sistema de arquivos padrão, você pode usar um caminho absoluto ou um caminho relativo. Por exemplo, o arquivo *hadoop-mapreduce-examples.jar* fornecido com clusters HDInsight pode ser referenciado para usar um dos seguintes procedimentos:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> O nome do arquivo é `hadoop-examples.jar` nos clusters HDInsight versões 2.1 e 1.6.

O caminho é o nome de caminho HDFS do arquivo ou diretório. Como os contêineres no Armazenamento do Azure são um repositório de chave-valor, não há nenhum sistema de arquivos hierárquico verdadeiro. Um caractere "/" dentro de uma chave de blob é interpretado como um separador de diretório. Por exemplo, o nome do blob para *hadoop-mapreduce-examples.jar* é:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhecem o formato WASB e, em vez disso, esperam um formato de caminho básico, como `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Contêineres de blob
Para usar blobs, primeiro você cria um [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Como parte desse processo, especifique uma região do Azure na qual a conta de armazenamento será criada. O cluster e a conta de armazenamento devem ser hospedados na mesma região. O banco de dados SQL Server do metastore do Hive e o banco de dados SQL Server do metastore do Oozie do Apache também devem estar localizados na mesma região.

Independentemente de onde estiverem, cada blob que você criar pertencerá a um contêiner na sua conta de armazenamento do Azure. Esse contêiner pode ser um contêiner de armazenamento de blob existente criado fora do HDInsight, ou pode ser um contêiner criado para um cluster HDInsight.

O contêiner de blob padrão armazena informações específicas do cluster como logs e o histórico do trabalho. Não compartilhe um contêiner de armazenamento de blob padrão com vários clusters HDInsight. Isso pode corromper o histórico de trabalhos. É recomendável usar um contêiner diferente para cada cluster e colocar os dados compartilhados em uma conta de armazenamento vinculada especificada na implantação de todos os clusters relevantes, e não na conta de armazenamento padrão. Para obter mais informações sobre como configurar contas de armazenamento vinculadas, consulte [clusters do HDInsight criar](hdinsight-hadoop-provision-linux-clusters.md). No entanto, você pode reutilizar um contêiner de armazenamento padrão depois que o cluster HDInsight original for excluído. Para clusters HBase, você pode realmente manter os dados e o esquema de tabela do HBase criando um novo cluster HBase com o contêiner de blobs padrão usado por um cluster HBase que foi excluído.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagindo com o armazenamento do Azure

A Microsoft fornece as seguintes ferramentas para trabalhar com o armazenamento do Azure:

| Ferramenta | Linux | OS X |  Windows |
| --- |:---:|:---:|:---:|
| [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [PowerShell do Azure](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Usar contas de armazenamento adicionais

Ao criar um cluster HDInsight, você especifica a conta de armazenamento do Azure que deseja associar a ele. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de criação ou após a criação de um cluster. Para obter instruções sobre como adicionar mais contas de armazenamento, veja [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o armazenamento do Azure compatível com HDFS com o HDInsight. Isso permite que você crie soluções de aquisição de dados para arquivamento de longo prazo escalonáveis e use o HDInsight para desbloquear as informações nos dados armazenados estruturados e não estruturados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução ao Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar assinaturas de acesso compartilhado do Azure Storage para restringir o acesso a dados com o HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md)