---
title: Criar cluster Hadoop, HBase, Kafka, Storm ou Spark no Azure HDInsight | Microsoft Docs
description: Aprenda a criar clusters Hadoop, HBase ou Storm ou Spark no Linux para o HDInsight usando um navegador, a CLI do Azure, o Azure PowerShell, REST ou um SDK.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 9fc96db2b832f1e57813bebd2d46e4b78ed04677
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="create-hadoop-clusters-in-hdinsight"></a>Criar clusters Hadoop no HDInsight
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Um cluster Hadoop é composto por várias máquinas virtuais (nós), usadas para processamento distribuído de tarefas no cluster. O Azure HDInsight simplifica os detalhes de implementação da instalação e configuração de nós individuais, portanto, você precisa fornecer apenas informações de configuração geral. Neste artigo, você aprende sobre essas definições de configuração.

## <a name="basic-configurations"></a>Configurações básicas

No [Portal do Azure](https://portal.azure.com), você pode criar um cluster HDInsight usando *Criação rápida* ou *Personalizada*. Esta seção aborda as configurações básicas usadas na opção Criação rápida. A opção Personalizado inclui as seguintes configurações adicionais:

- [Aplicativos](#hdinsight-applications)
- [Tamanho do cluster](#cluster-size)
- Configurações avançadas

  - [Ações de script](#customize-clusters-using-script-action)
  - [Rede virtual](#use-virtual-network)

### <a name="subscription"></a>Assinatura 
Cada cluster HDInsight é vinculado a uma assinatura do Azure.

### <a name="resource-group-name"></a>Nome do grupo de recursos
O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ajuda você a trabalhar com os recursos de seu aplicativo como um grupo, chamado de grupo de recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada.

### <a name="cluster-name"></a>Nome do cluster
O nome do cluster é usado para identificar um cluster. O nome do cluster deve ser globalmente exclusivo e seguir as seguintes diretrizes de nomenclatura:

* O campo deve ser uma cadeia com três a 63 caracteres.
* O campo pode conter apenas letras, números e hifens.

### <a name="cluster-types"></a> Tipo de cluster
Atualmente, o Azure HDInsight apresenta os seguintes tipos de cluster, cada um com um conjunto de componentes para fornecer determinadas funcionalidades:

| Tipo de cluster | Funcionalidade |
| --- | --- |
| [Hadoop](hdinsight-hadoop-introduction.md) |Consulta e análise (trabalhos em lotes) |
| [HBase](hdinsight-hbase-overview.md) |Armazenamento de dados NoSQL |
| [Storm](hdinsight-storm-overview.md) |Processamento de eventos em tempo real |
| [Spark](hdinsight-apache-spark-overview.md) |Processamento na memória, consultas interativas, processamento de transmissão de microlotes |
| [Kafka (Visualização)](hdinsight-apache-kafka-introduction.md) | Uma plataforma de streaming distribuída que pode ser usada para compilar pipelines e aplicativos de dados de streaming em tempo real |
| [Servidor R](hdinsight-hadoop-r-server-overview.md) |Uma variedade de recursos de estatísticas de Big Data, modelagem preditiva e aprendizado de máquina |
| [Hive Interativo (Visualização)](hdinsight-hadoop-use-interactive-hive.md) |Caching na memória para consultas de Hive interativas e mais rápidas |

Cada tipo de cluster tem seu próprio número de nós, terminologia para nós no cluster, bem como o tamanho da VM padrão para cada tipo de nó. Na tabela a seguir, o número de nós para cada tipo de nó está entre parênteses.

| Tipo | Nós | Diagrama |
| --- | --- | --- |
| O Hadoop |Nó de cabeçalho (2), Nó de dados (1+) |![Nós de cluster Hadoop do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Hadoop-roles.png) |
| HBase |Servidor de cabeçalho (2), Servidor de região (1 +), Nó mestre/do ZooKeeper (3) |![Nós de cluster HBase do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-HBase-roles.png) |
| Storm |Nó do Nimbus (2), Servidor do supervisor (1+), Nó do ZooKeeper (3) |![Nós de cluster Storm do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Storm-roles.png) |
| Spark |Nó de cabeçalho (2), nó de trabalho (1+), nó do ZooKeeper (3) (gratuito para tamanho de VM A1 do ZooKeeper) |![Nós de cluster Spark do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Spark-roles.png) |

As tabelas abaixo listam os tamanhos de VM padrão para o HDInsight:

* Todas as regiões com suporte, exceto Sul do Brasil e Oeste do Japão:

  | Tipo de cluster | O Hadoop | HBase | Storm | Spark | Servidor R |
  | --- | --- | --- | --- | --- | --- |
  | Cabeçalho: tamanho padrão da VM |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | Cabeçalho: tamanhos de VM recomendados |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Trabalho: tamanho de VM padrão |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Trabalho: tamanhos de VM recomendados |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | Zookeeper: tamanho de VM padrão | |A3 |A2 | | |
  | Zookeeper: tamanhos de VM recomendados | |A3, A4, A5 |A2, A3, A4 | | |
  | Borda: tamanho padrão da VM | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Borda: tamanho de VM recomendado | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Apenas Sul do Brasil e Oeste do Japão (sem tamanhos v2 aqui):

  | Tipo de cluster | O Hadoop | HBase | Storm | Spark | Servidor R |
  | --- | --- | --- | --- | --- | --- |
  | Cabeçalho: tamanho padrão da VM |D3 |D3 |A3 |D12 |D12 |
  | Cabeçalho: tamanhos de VM recomendados |D3, D4, D12 |D3, D4, D12 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Trabalho: tamanho de VM padrão |D3 |D3 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Trabalho: tamanhos de VM recomendados |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | Zookeeper: tamanho de VM padrão | |A2 |A2 | | |
  | Zookeeper: tamanhos de VM recomendados | |A2, A3, A4 |A2, A3, A4 | | |
  | Borda: tamanhos padrão da VM | | | | |Windows: D12; Linux: D4 |
  | Borda: tamanhos de VM recomendados | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> O cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm. O trabalho é conhecido como *Região* para o tipo de cluster HBase e como *Supervisor* para o tipo de cluster Storm.

> [!IMPORTANT]
> Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.
>
>

É possível adicionar outros componentes, como o Hue, a esses tipos básicos usando as [ações de script](#customize-clusters-using-script-action).

> [!IMPORTANT]
> Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster.
>
>

Se sua solução exige tecnologias que sejam distribuídas entre vários tipos de cluster HDInsight, você deve criar uma rede virtual do Azure e os tipos de cluster necessários dentro da rede virtual. Essa configuração permite que os clusters e qualquer código que você implantar neles se comuniquem diretamente uns com os outros.

Para obter mais informações sobre como usar uma rede virtual do Azure com HDInsight, confira [Estender o HDInsight com redes virtuais do Azure](hdinsight-extend-hadoop-virtual-network.md).

Para obter um exemplo de como usar dois tipos de cluster em uma rede virtual do Azure, confira [Analisar dados de sensor com Storm e HBase](hdinsight-storm-sensor-data-analysis.md).

### <a name="operating-system"></a>Sistema operacional
Você pode criar clusters HDInsight no Windows ou Linux.  Para saber mais sobre as versões de SO, confira [Versões do HDInsight com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions). O HDInsight 3.4 ou superior usa a distribuição do Ubuntu Linux como o SO subjacente para o cluster. 

### <a name="version"></a>Versão
Essa opção é usada para determinar a versão do HDInsight a ser usada para esse cluster. Para saber mais, confira [Versões do HDInsight com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-tiers"></a>Camada de cluster

O Azure HDInsight fornece ofertas de nuvem de Big Data em duas categorias: Standard e Premium.  Para saber mais, confira [HDInsight Standard e HDInsight Premium]](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).

A captura de tela a seguir mostra as informações do portal do Azure para escolher os tipos de cluster.

![Configuração do HDInsight premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


### <a name="credentials"></a>Credenciais
Com os clusters HDInsight, você pode configurar duas contas de usuário durante a criação de cluster:

* Usuário HTTP. O nome de usuário padrão é *admin*. Ele usa a configuração básica no portal do Azure. Às vezes, ele é chamado "Usuário de cluster".
* Usuário SSH (clusters do Linux). Ele é usado para conectar-se ao cluster por meio de SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="location"></a>Local
O local do cluster não precisa ser explicitamente especificado. O cluster compartilha o mesmo local do armazenamento padrão. Para ver uma lista das regiões com suporte, clique na lista suspensa **Região** em [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


### <a name="storage"></a>Armazenamento

O HDFS (sistema de arquivos distribuído Hadoop) original usa muitos discos locais no cluster. O HDInsight usa blobs no [Armazenamento do Azure](hdinsight-hadoop-provision-linux-clusters.md#azure-storage) ou o [Azure Data Lake Store](hdinsight-hadoop-provision-linux-clusters.md#azure-data-lake-store). Há alguns requisitos específicos sobre como usar Data Lake Stores para HDInsight. Para saber mais, veja a seção de introdução de [Criar clusters HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

![Armazenamento do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)


#### <a name="azure-storage"></a>Armazenamento do Azure
O Azure Storage é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. Ao usar uma interface HDFS, o conjunto completo de componentes em HDInsight pode operar diretamente sobre dados estruturados ou não estruturados armazenados em blobs. Armazenar dados no Azure Storage permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário. O HDInsight só dá suporte a contas do Azure Storage de __Uso geral__. Atualmente ele não dá suporte ao tipo de conta __Armazenamento de blobs__.

Durante a configuração, você especifica uma conta de Armazenamento do Azure e um contêiner de blobs na conta de Armazenamento do Azure. O contêiner blobs é usado como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar contas adicionais do Azure Storage (armazenamento vinculado) que o cluster possa acessar. Além disso, o cluster também pode acessar contêineres de Blob configurados com acesso de leitura público completo ou acesso de leitura público somente para blobs.

Não recomendamos o contêiner de blobs padrão para armazenar dados corporativos. É uma prática recomendada excluir o contêiner de blobs padrão após cada uso para reduzir o custo de armazenamento. Observe que o contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

Não há suporte para o compartilhamento de um contêiner de blobs para vários clusters.

Para saber mais sobre o uso de uma conta de Armazenamento do Azure, confira [Usando Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Além do Azure Storage, você pode usar o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) como a conta de armazenamento padrão do cluster HBase no HDInsight e como um armazenamento vinculado para todos os quatro tipos de cluster HDInsight. Para saber mais, veja [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

#### <a name="use-additional-storage"></a>Armazenamento adicional

Em alguns casos, você pode adicionar mais armazenamento ao cluster. Por exemplo, você pode ter várias contas de armazenamento do Azure para diferentes regiões geográficas ou diferentes serviços, mas desejar analisá-los com o HDInsight.

Você pode adicionar contas de armazenamento ao criar um cluster do HDInsight ou depois de um cluster ter sido criado.  Confira [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

Para saber mais sobre a conta do Azure Storage secundária, confira [Uso do Azure Storage com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para saber mais sobre repositórios Data Lake secundários, veja [Criar clusters HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).


#### <a name="use-hiveoozie-metastore"></a>Metastore do Hive/Oozie
Recomendamos que você use um metastore personalizado se quiser manter suas tabelas Hive depois de excluir o cluster HDInsight. Você poderá anexar esse metastore a outro cluster HDInsight.

> [!IMPORTANT]
> Um metastore do HDInsight criado para uma versão de cluster do HDInsight não pode ser compartilhado entre diferentes versões de cluster do HDInsight. Para obter uma lista das versões do HDInsight, consulte [Versões do HDInsight com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

O metastore contém metadados de Hive e Oozie, como tabelas Hive, partições, esquemas e colunas. O metastore ajuda a manter seus metadados do Hive e do Oozie, para que você não precise recriar tabelas Hive ou trabalhos do Oozie ao criar um novo cluster. Por padrão, o Hive usa um banco de dados SQL do Azure integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído. Quando você cria uma tabela do Hive em um cluster HDInsight com um metastore do Hive configurado, as tabelas serão mantidas ao recriar o cluster usando o mesmo metastore do Hive.

A configuração de Metastore não está disponível para tipos de cluster HBase.

> [!IMPORTANT]
> Ao criar um metastore personalizado, não use um nome de banco de dados que contém a traços ou hifens. Isso pode fazer com que o processo de criação de cluster falhe.
>
>

## <a name="install-hdinsight-applications"></a>Instalar aplicativos do HDInsight

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster HDInsight baseado em Linux. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria. Para saber mais, confira [Instalar aplicativos de terceiros do Hadoop no Azure HDInsight](hdinsight-apps-install-applications.md).

A maioria dos aplicativos HDInsight é instalada em um nó de borda vazia.  Um nó de borda vazio é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas do nó principal. Você pode usar o nó de borda para acessar o cluster, testar e hospedar seus aplicativos clientes. Para saber mais, confira [Usar nós de borda vazia no HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="configure-cluster-size"></a>Configurar tamanho do cluster

Os clientes são cobrados pelo uso desses nós pela duração da vida útil do cluster. A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído. Os clusters não podem ser desalocados ou colocados em espera.

Diferentes tipos de cluster têm diferentes tipos de nó, números de nós e tamanhos de nós. Por exemplo, um tipo de cluster Hadoop tem dois *nós de cabeçalho* e um padrão de quatro *nós de dados*, enquanto um tipo de cluster Storm tem dois *nós Nimbus*, três *nós ZooKeeper* e um padrão de quatro *nós supervisores*. O custo de clusters HDInsight é determinado pelo número de nós e pelos tamanhos de máquinas virtuais para os nós. Por exemplo, se você souber que executará operações que precisam de muita memória, convém selecionar um recurso de computação com mais memória. Para fins de aprendizado, recomendamos usar um nó de dados. Para obter mais informações sobre os preços do HDInsight, confira [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> O limite de tamanho do cluster varia entre as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.
>
> Os nós usados pelo seu cluster não contam como máquinas virtuais, pois as imagens de máquina virtual usadas para os nós são um detalhe de implementação do serviço HDInsight. Os núcleos de computação usados por nós são contados em relação ao número total de núcleos de computação disponíveis para sua assinatura. Ao criar um cluster HDInsight, você poderá ver o número de núcleos disponíveis e os núcleos que serão usados pelo cluster na seção de resumo da folha **Tamanho do cluster**.
>
>

Quando você usar o portal do Azure para configurar o cluster, o tamanho do nó está disponível na folha **Tipos de Preço de Nó** . Você também pode ver o custo associado aos diferentes tamanhos de nós. A captura de tela a seguir mostra as opções para um cluster Hadoop baseado em Linux.

![Tamanhos de nó de VM do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

As tabelas a seguir mostram os tamanhos com suporte dos clusters HDInsight e as capacidades que eles fornecem.

### <a name="standard-tier-a-series"></a>Camada Standard: série A
No modelo de implantação clássica, alguns tamanhos de VM são ligeiramente diferentes no PowerShell e na CLI (interface de linha de comando).

* Standard_A3 é grande
* Standard_A4 é ExtraLarge

| Tamanho | Núcleos de CPU | Memória | NICs (Máx.) | Máx. tamanho do disco | Máx. de discos de dados (1023 GB cada) | Máx. IOPS (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\Grande |4 |7 GB |2 |Temporário = 285 GB |8 |8 x 500 |
| Standard_A4\ExtraLarge |8 |14 GB |4 |Temporário = 605 GB |16 |16 x 500 |
| Standard_A6 |4 |28 GB |2 |Temporário = 285 GB |8 |8 x 500 |
| Standard_A7 |8 |56 GB |4 |Temporário = 605 GB |16 |16 x 500 |

### <a name="standard-tier-d-series"></a>Camada Standard: série D
| Tamanho | Núcleos de CPU | Memória | NICs (Máx.) | Máx. tamanho do disco | Máx. de discos de dados (1023 GB cada) | Máx. IOPS (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3 |4 |14 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D4 |8 |28 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D12 |4 |28 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D13 |8 |56 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D14 |16 |112 GB |8 |Temporário (SSD) =800 GB |32 |32 x 500 |

### <a name="standard-tier-dv2-series"></a>Camada Standard: série Dv2
| Tamanho | Núcleos de CPU | Memória | NICs (Máx.) | Máx. tamanho do disco | Máx. de discos de dados (1023 GB cada) | Máx. IOPS (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D4_v2 |8 |28 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D12_v2 |4 |28 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D13_v2 |8 |56 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D14_v2 |16 |112 GB |8 |Temporário (SSD) =800 GB |32 |32 x 500 |

Para obter as considerações de implantação a serem observadas ao planejar o uso desses recursos, confira [Tamanhos para máquinas virtuais](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para obter informações sobre os preços dos vários tamanhos, confira [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

> [!IMPORTANT]
> Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.
>
>

A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído. Para obter mais informações sobre preços, consulte [Detalhes de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


> [!WARNING]
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.



## <a name="use-virtual-network"></a>Usar rede virtual
Com a [Rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/), você pode criar uma rede segura e persistente que contém os recursos necessários para sua solução. Para saber mais sobre como usar o HDInsight com uma rede virtual, incluindo requisitos de configuração específicos para a rede virtual, confira [Estender as funcionalidades do HDInsight usando uma Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).


## <a name="customize-clusters-using-script-action"></a>Personalizar clusters usando a Ação de Script

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante a criação. Tais scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no portal do Azure, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

Alguns componentes nativos do Java, como Mahout e Cascading, podem ser executados no cluster como arquivos Java Archive (JAR). Esses arquivos JAR podem ser distribuídos para o Azure Storage e enviados aos clusters HDInsight por meio de mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md).

> [!NOTE]
> Se você tiver problemas para implantar arquivos JAR nos clusters do HDInsight ou ao chamar arquivos JAR nesses clusters, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).
>
> A cascata não tem suporte do HDInsight e não está qualificada para o Suporte da Microsoft. Para obter as listas dos componentes suportados, confira [Novidades nas versões de cluster fornecidas pelo HDInsight](hdinsight-component-versioning.md).
>
>

Às vezes, você deseja definir os seguintes arquivos de configuração durante o processo de criação:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Para saber mais, confira [Personalizar clusters HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="cluster-creation-methods"></a>Métodos de criação de cluster
Neste artigo, você aprendeu as informações básicas sobre como criar um cluster HDInsight baseado em Linux. Use a tabela a seguir para obter informações específicas sobre como criar um cluster usando um método que melhor atenda às suas necessidades.

| Clusters criados com | Navegador da Web | Linha de comando | API REST | . | Linux, Mac OS X ou Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [O portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |✔ |✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [PowerShell do Azure](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Modelos do Gerenciador de Recursos do Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [ requisitos de controle de acesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Próximas etapas

- [O que é o HDInsight](hdinsight-hadoop-introduction.md).
- [Tutorial do Hadoop: Introdução ao uso do Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
