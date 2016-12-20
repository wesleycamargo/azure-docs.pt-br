---
title: Criar clusters Hadoop baseados no Windows no HDInsight | Microsoft Docs
description: Aprenda a criar clusters do Azure HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a311f7e0-9333-4886-a726-def79e5db8cb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/21/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: a9d45c12da106f8175a37e6e963b78d50f93f0ad


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight"></a>Crie clusters Hadoop baseados no Windows no HDInsight
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Um cluster Hadoop é composto por várias máquinas virtuais (nós), usadas para processamento distribuído de tarefas no cluster. O Azure abstrai os detalhes da implementação da instalação e configuração de nós individuais. Portanto, você precisa fornecer informações de configuração geral. Neste artigo, você aprenderá a usar essas definições de configuração.

> [!NOTE]
> As informações contidas neste documento são específicas de clusters HDInsight do Azure baseados em Windows. Para saber mais sobre clusters baseados em Linux, veja [Create Linux-based Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
> 
> 

## <a name="access-control-requirements"></a>Requisitos de controle de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="cluster-types"></a>Tipos de cluster
Atualmente, o HDInsight fornece quatro tipos diferentes de clusters, cada um com um conjunto de componentes para fornecer certas funcionalidades.

| Tipo de cluster | Funcionalidade |
| --- | --- |
| O Hadoop |Consulta e análise (trabalhos em lotes) |
| HBase |Armazenamento de dados NoSQL |
| Storm |Processamento de eventos em tempo real |
| Spark (visualização) |Processamento na memória, consultas interativas, processamento de transmissão de microlotes |

Cada tipo de cluster tem seu próprio número de nós, terminologia para nós no cluster, bem como o tamanho da VM padrão para cada tipo de nó. Na tabela a seguir, o número de nós para cada tipo de nó está entre parênteses.

| Tipo | Nós (número de nós) | Diagrama |
| --- | --- | --- |
| O Hadoop |Nó de cabeçalho (2), Nó de dados (1+) |![Nós de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png) |
| HBase |Servidor de cabeçalho (2), Servidor de região (1 +), Nó mestre/do ZooKeeper (3) |![Nós de cluster HBase do HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png) |
| Storm |Nó do Nimbus (2), Servidor do supervisor (1+), Nó do ZooKeeper (3) |![Nós de cluster Storm do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png) |
| Spark |Nó de cabeçalho (2), nó de trabalho (1+), nó do ZooKeeper (3) (gratuito para tamanho de VM A1 do ZooKeeper) |![Nós de cluster Spark do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png) |

> [!IMPORTANT]
> Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.
> 
> 

É possível adicionar outros componentes, como Hue ou R, a esses tipos básicos usando as [Ações de Script](#customize-clusters-using-script-action).

> [!IMPORTANT]
> Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster. 
> 
> 

Se sua solução exige tecnologias que sejam distribuídas entre vários tipos de cluster HDInsight, você deve criar uma Rede Virtual do Azure e os tipos de cluster necessários dentro da rede virtual. Isso permite que os clusters e qualquer código que você implantar neles se comuniquem diretamente uns com os outros.

Para obter mais informações sobre como usar uma Rede Virtual do Azure com HDInsight, confira [Estender o HDInsight com Redes Virtuais do Azure](hdinsight-extend-hadoop-virtual-network.md).

Para obter um exemplo de como usar dois tipos de cluster em uma Rede Virtual do Azure, confira [Analisar dados de sensor com Storm e HBase](hdinsight-storm-sensor-data-analysis.md).

## <a name="basic-configuration-options"></a>Opções de configuração básicas
Veja a seguir as opções de configuração básica necessárias para criar um cluster HDInsight.

### <a name="cluster-name"></a>Nome do cluster
O nome do cluster é usado para identificar um cluster. O nome do cluster deve ser globalmente exclusivo e deve seguir estas diretrizes de nomenclatura:

* O campo deve ser uma cadeia com 3 a 63 caracteres
* O campo pode conter apenas letras, números e hifens.

### <a name="cluster-type"></a>Tipo de cluster
Veja [Tipos de cluster](#cluster-types).

### <a name="operating-system"></a>Sistema operacional
É possível criar clusters HDInsight em um dos dois sistemas operacionais a seguir:

* HDInsight no Linux. O HDInsight fornece a opção de configuração de clusters do Linux no Azure. Configure um cluster do Linux, se você estiver familiarizado com o Linux ou Unix, estiver migrando de uma solução Hadoop baseada em Linux existente ou desejar fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Para obter mais informações, consulte [Introdução ao Hadoop no Linux em HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
* HDInsight no Windows (Windows Server 2012 R2 Datacenter).

### <a name="hdinsight-version"></a>Versão do HDInsight
A versão do HDInsight é usada para determinar a versão do HDInsight para usar esse cluster. Para saber mais, confira [Versões e componentes do cluster Hadoop no HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

### <a name="subscription-name"></a>Nome da assinatura
Cada cluster HDInsight é vinculado a uma assinatura do Azure.

### <a name="resource-group-name"></a>Nome do grupo de recursos
Com o [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), você pode implantar, atualizar, monitorar ou excluir os recursos em seu aplicativo.

### <a name="credentials"></a>Credenciais
Com os clusters HDInsight, você pode configurar três contas de usuário durante a criação de cluster.

* O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ajuda você a trabalhar com os recursos de seu aplicativo como um grupo, chamado de grupo de recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada.
* Usuário HTTP. O nome de usuário padrão é *admin* , na configuração básica do Portal do Azure. Às vezes, o padrão é chamado de "Usuário de cluster".
* Usuário RDP (clusters do Windows). Conecte-se ao cluster usando RDP. Ao criar a conta, você deve definir uma data de expiração dentro de 90 dias a contar do dia em que criar a conta.
* Usuário SSH (clusters do Linux). Conecte-se ao cluster usando o SSH. Você pode criar contas de usuário SSH adicionais depois que o cluster for criado ao seguir as etapas em [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, do Unix ou do OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="data-source"></a>Fonte de dados
O sistema de arquivos distribuído Hadoop (HDFS) original usa muitos discos locais no cluster. O HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados. O armazenamento de blobs do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. Ao usar uma interface HDFS, o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados não estruturados do armazenamento de Blobs. Se armazenar dados no armazenamento de Blobs, você poderá excluir com segurança os clusters HDInsight usados para cálculo sem perder dados do usuário.

Durante a configuração, você deve especificar uma conta de armazenamento do Azure e um contêiner de armazenamento de Blobs do Azure na conta de armazenamento do Azure. Alguns processos de criação exigem que a conta de armazenamento do Azure e o contêiner de armazenamento de Blobs sejam criados anteriormente. O contêiner de armazenamento de Blobs é usado como o local de armazenamento padrão pelo cluster. Ou você pode especificar contas de armazenamento do Azure adicionais (armazenamento vinculado) que poderão ser acessadas pelo cluster. O cluster também pode acessar quaisquer contêineres de armazenamento de Blobs configurados com acesso de leitura público completo ou acesso de leitura público somente para blobs.  Para saber mais, confira [Gerenciar o acesso aos recursos de Armazenamento do Azure](../storage/storage-manage-access-to-resources.md).

![Armazenamento do HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

> [!NOTE]
> Um contêiner de armazenamento de Blobs oferece o agrupamento de um conjunto de blobs, conforme mostrado na imagem a seguir.
> 
> 

Durante a configuração, você deve especificar uma conta de armazenamento do Azure e um contêiner de armazenamento de Blobs do Azure na conta de armazenamento do Azure. Alguns processos de criação exigem que a conta de armazenamento do Azure e o contêiner de armazenamento de Blobs sejam criados anteriormente. O contêiner de armazenamento de Blobs é usado como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar contas de armazenamento do Azure adicionais (armazenamento vinculado) que o cluster possa acessar. Além disso, o cluster também pode acessar contêineres de Blob configurados com acesso de leitura público completo ou acesso de leitura público somente para blobs. Para saber mais, confira [Gerenciar o acesso aos recursos de Armazenamento do Azure](../storage/storage-manage-access-to-resources.md).

![Armazenamento do Blobs do Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

Não recomendamos o contêiner de armazenamento de Blobs padrão para armazenar dados corporativos. É uma prática recomendada excluir o contêiner de armazenamento de Blobs padrão após cada uso para reduzir o custo de armazenamento. O contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

> [!WARNING]
> O HDInsight não dá suporte ao compartilhamento de um contêiner de armazenamento de Blobs para vários clusters.
> 
> 

Para saber mais sobre o armazenamento de Blobs secundário, veja [Armazenamento de Blobs do Azure compatível com HDFS com Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md).

Além do armazenamento de Blobs do Azure, também é possível usar o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) como a conta de armazenamento padrão do cluster HBase no HDInsight e como um armazenamento vinculado para todos os quatro tipos de cluster HDInsight. Para saber mais, veja [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="location-region"></a>Local (região)
O cluster HDInsight e sua conta de armazenamento padrão devem estar localizados no mesmo local do Azure.

![Regiões do Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

Para ver uma lista das regiões com suporte, clique na lista suspensa **Região** em [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

### <a name="node-pricing-tiers"></a>Tipos de preço do nó
Os clientes são cobrados pelo uso desses nós pela duração da vida útil do cluster. A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído. Os clusters não podem ser desalocados ou colocados em espera.

Diferentes tipos de cluster têm diferentes tipos de nó, números de nós e tamanhos de nós. Por exemplo, um tipo de cluster Hadoop tem dois *nós de cabeçalho* e um padrão de quatro *nós de dados*, enquanto um tipo de cluster Storm tem dois *nós nimbus*, três *nós ZooKeeper* e um padrão de quatro *nós supervisores*. O custo de clusters HDInsight é determinado pelo número de nós e pelos tamanhos de máquinas virtuais para os nós. Por exemplo, se você souber que executará operações que precisam de muita memória, convém selecionar um recurso de computação com mais memória. Para fins de aprendizado, recomendamos trabalhar com um nó de dados. Para obter mais informações sobre os preços do HDInsight, confira [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> O limite de tamanho do cluster varia entre as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.
> 
> Os nós usados pelo seu cluster não contam como máquinas virtuais, pois as imagens de máquina virtual usadas para os nós são um detalhe de implementação do serviço HDInsight. No entanto, os núcleos de computação usados por nós são contados em relação ao número total de núcleos de computação disponíveis para sua assinatura. Você pode ver os núcleos disponíveis e o número de núcleos que o cluster usará na seção de resumo da folha Tipo de Preço do Nó ao criar um cluster HDInsight.
> 
> 

Quando você configura o cluster com o Portal do Azure, o tamanho do nó está disponível por meio da folha **Tipo de Preço do Nó** . Você também pode ver o custo associado aos diferentes tamanhos de nós. A captura de tela a seguir mostra as opções para um cluster Hadoop baseado em Linux.

![tamanhos de nó de vm do HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

As tabelas a seguir mostram os tamanhos com suporte dos clusters HDInsight e as capacidades que eles fornecem.

### <a name="standard-tier-a-series"></a>Camada Standard: série A
No modelo de implantação clássica, alguns tamanhos de VM são ligeiramente diferentes no PowerShell e na CLI.

* Standard_A3 é grande
* Standard_A4 é ExtraLarge

| Tamanho | Núcleos de CPU | Memória | NICs (máx.) | Máx. tamanho do disco | Máx. de discos de dados (1023 GB cada) | Máx. IOPS (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\Grande |4 |7 GB |2 |Temporário = 285 GB |8 |8 x 500 |
| Standard_A4\ExtraLarge |8 |14 GB |4 |Temporário = 605 GB |16 |16 x 500 |
| Standard_A6 |4 |28 GB |2 |Temporário = 285 GB |8 |8 x 500 |
| Standard_A7 |8 |56 GB |4 |Temporário = 605 GB |16 |16 x 500 |

### <a name="standard-tier-d-series"></a>Camada Standard: série D
| Tamanho | Núcleos de CPU | Memória | NICs (máx.) | Máx. tamanho do disco | Máx. de discos de dados (1023 GB cada) | Máx. IOPS (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3 |4 |14 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D4 |8 |28 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D12 |4 |28 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D13 |8 |56 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D14 |16 |112 GB |8 |Temporário (SSD) =800 GB |32 |32 x 500 |

### <a name="standard-tier-dv2-series"></a>Camada Standard: série Dv2
| Tamanho | Núcleos de CPU | Memória | NICs (máx.) | Máx. tamanho do disco | Máx. de discos de dados (1023 GB cada) | Máx. IOPS (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D4_v2 |8 |28 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D12_v2 |4 |28 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D13_v2 |8 |56 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D14_v2 |16 |112 GB |8 |Temporário (SSD) =800 GB |32 |32 x 500 |

Para obter as considerações de implantação a serem observadas ao planejar o uso desses recursos, confira [Tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para saber mais sobre os preços dos vários tamanhos, veja [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

> [!IMPORTANT]
> Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.
> 
> 

A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído. Para saber mais sobre preços, veja [Detalhes de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

| Tamanho | Núcleos de CPU | Memória | NICs (máx.) | Máx. tamanho do disco | Máx. de discos de dados (1023 GB cada) | Máx. IOPS (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D4_v2 |8 |28 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D12_v2 |4 |28 GB |4 |Temporário (SSD) =200 GB |8 |8 x 500 |
| Standard_D13_v2 |8 |56 GB |8 |Temporário (SSD) =400 GB |16 |16 x 500 |
| Standard_D14_v2 |16 |112 GB |8 |Temporário (SSD) =800 GB |32 |32 x 500 |

Para obter as considerações de implantação a serem observadas ao planejar o uso desses recursos, confira [Tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para obter informações sobre os preços dos vários tamanhos, veja [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).  

> [!IMPORTANT]
> Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.
> 
> 

 A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído. Para obter mais informações sobre preços, consulte [Detalhes de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="add-more-storage"></a>Adicionar mais armazenamento
Em alguns casos, convém adicionar mais armazenamento ao cluster. Por exemplo, você pode ter várias contas de armazenamento do Azure para diferentes regiões geográficas ou diferentes serviços, mas desejar analisá-los com o HDInsight.

Para saber mais sobre o armazenamento de Blobs secundário, veja [Usar o armazenamento de Blobs do Azure compatível com HDFS com Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md). Para saber mais sobre repositórios Data Lake secundários, veja [Criar clusters HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="use-a-hiveoozie-metastore"></a>Usar um Hive/Oozie metastore
É altamente recomendável usar um metastore personalizado se desejar manter as tabelas do Hive depois de excluir o cluster HDInsight, para fins de anexação desse metastore a outro cluster HDInsight no futuro.

> [!IMPORTANT]
> O metastore HDInsight não é compatível com versões anteriores. Por exemplo, você não pode usar um metastore de um cluster do HDInsight 3.3 para criar um cluster do HDInsight 3.2.
> 
> 

O metastore contém metadados de Hive e Oozie, como tabelas Hive, partições, esquemas e colunas. O metastore ajuda a reter seus metadados Hive e Oozie. Você não precisa recriar tabelas de Hive ou trabalhos do Oozie ao criar um novo cluster. Por padrão, o Hive usa um banco de dados SQL do Azure integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído. Por exemplo, se criar tabelas do Hive em um cluster criado com um metastore do Hive, você poderá ver as tabelas se excluir e recriar o cluster com o mesmo metastore do Hive.

A configuração de Metastore não está disponível para tipos de cluster HBase.

> [!IMPORTANT]
> Ao criar um metastore personalizado, não use um nome de banco de dados que contém a traços ou hifens, pois isso pode causar falha no processo de criação de cluster.
> 
> 

## <a name="use-azure-virtual-network"></a>Usar uma Rede Virtual do Azure
[Rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) : ajuda a criar uma rede segura e persistente que contém os recursos necessários para sua solução. Com uma rede virtual, você pode:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).
  
    ![diagrama da configuração somente nuvem](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)
* Conecte seus recursos de nuvem à sua rede de datacenter local (site a site ou ponto a site), usando uma VPN (rede privada virtual).

| Configuração site a site | Configuração ponto a site |
| --- | --- |
| Com a configuração site a site, você pode conectar vários recursos do data center à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.<br />![diagrama da configuração site a site](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) |A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.<br />![diagrama da configuração ponto a site](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Os clusters baseados em Windows exigem uma rede virtual clássica, enquanto os clusters baseados em Linux exigem uma rede virtual do Azure Resource Manager. Se você não tiver o tipo correto de rede, ele não poderá ser usado durante a criação do cluster.

Para saber mais sobre como o HDInsight funciona com uma rede virtual, incluindo requisitos de configuração específicos para a rede virtual, confira [Estender as funcionalidades do HDInsight usando uma Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="customize-clusters-by-using-hdinsight-cluster-customization-bootstrap"></a>Personalizar clusters usando a personalização de clusters do HDInsight (inicialização)
Às vezes, você deseja configurar os seguintes arquivos de configuração:

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

Para manter as alterações durante o tempo de vida dos clusters, você pode usar a personalização de clusters do HDInsight durante o processo de criação. Você também pode usar o Ambari em clusters baseados em Linux. Para saber mais, confira [Personalizar clusters HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).

> [!NOTE]
> Os clusters baseados em Windows não podem reter as alterações devido à recriação das imagens. Para obter mais informações, consulte [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para manter as alterações durante a vida útil de um cluster, você deve usar a personalização do cluster HDInsight durante o processo de criação.
> 
> 

## <a name="customize-clusters-by-using-script-action"></a>Personalizar clusters usando a Ação de Script
Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante a criação. Tais scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no portal do Azure, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para saber mais, veja [Personalizar um cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).

## <a name="cluster-creation-methods"></a>Métodos de criação de cluster
Neste artigo, você aprendeu as informações básicas sobre como criar um cluster HDInsight baseado em Windows. Use a tabela a seguir para obter informações específicas sobre como criar um cluster usando um método que melhor atenda às suas necessidades.

| Clusters criados com | Navegador da Web | Linha de comando | API REST | . | Linux, Mac OS X ou Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [O portal do Azure](hdinsight-hadoop-create-windows-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [CLI do Azure](hdinsight-hadoop-create-windows-clusters-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [PowerShell do Azure](hdinsight-hadoop-create-windows-clusters-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [SDK .NET](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Modelos do Gerenciador de Recursos do Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |




<!--HONumber=Nov16_HO3-->


