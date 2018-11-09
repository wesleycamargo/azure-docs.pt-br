---
title: Migrar clusters do Apache Hadoop local para o Azure HDInsight – práticas recomendadas de infraestrutura
description: Aprenda as melhores práticas de infraestrutura para migrar clusters do Hadoop locais para o Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: a1dbc04a83e80281fb6cd516c546c1dddff6db37
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413784"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrar clusters do Apache Hadoop local para o Azure HDInsight – práticas recomendadas de infraestrutura

Este artigo apresenta recomendações para gerenciar a infraestrutura de clusters do Azure HDInsight. Ele faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas locais do Apache Hadoop para o Azure HDInsight.

## <a name="plan-well-for-the-capacity-needed-for-hdinsight-clusters"></a>Planeje bem para a capacidade necessária para clusters do HDInsight

As principais escolhas a fazer para planejamento de capacidade de cluster do HDInsight são as seguintes:

- **Escolher a região** – a região do Azure determina o local em que o cluster é provisionado fisicamente. Para minimizar a latência de leituras e gravações, o cluster deverá estar na mesma região que os dados.
- **Escolher a localização e o tamanho do armazenamento** – o armazenamento padrão deve estar na mesma região que o cluster. Para um cluster de 48 nós, é recomendável ter de quatro a oito contas de armazenamento. Embora já possa haver um armazenamento total suficiente, cada conta de armazenamento fornece largura de banda de rede adicional para os nós de computação. Quando houver várias contas de armazenamento, use um nome aleatório para cada conta de armazenamento, sem um prefixo. A finalidade da nomeação aleatória é a redução da chance de gargalos (limitações) de armazenamento ou falhas de modo comum em todas as contas. Para obter melhor desempenho, use apenas um contêiner por conta de armazenamento.
- **Escolher o tamanho e o tipo da VM (agora dá suporte à série G)** – cada tipo de cluster tem um conjunto de tipos de nó e cada tipo de nó tem opções específicas para seu tamanho e tipo de VM. O tamanho e o tipo da VM são determinados pelo poder de processamento da CPU, pelo tamanho da RAM e pela latência da rede. Uma carga de trabalho simulada pode ser usada para determinar o tamanho e o tipo ideais da VM para cada tipo de nó.
- **Escolher o número de nós de trabalho** – o número inicial de nós de trabalho pode ser determinado usando as cargas de trabalho simuladas. O cluster pode ser dimensionado mais tarde adicionando mais nós de trabalho para atender às demandas de carga de pico. O cluster mais tarde pode ser dimensionado quando os nós de trabalho adicionais não são necessários.

Para obter mais informações, confira o artigo [Planejamento de capacidade para clusters do HDInsight](../hdinsight-capacity-planning.md)

## <a name="use-the-recommended-virtual-machine-types-for-cluster-nodes"></a>Use os tipos de máquina virtual recomendados para nós de cluster

Veja [Configuração de nó padrão e tamanhos de máquina virtual para clusters](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) para os tipos de máquina virtual recomendados para cada tipo de cluster do HDInsight.

## <a name="check-the-availability-of-hadoop-components-in-hdinsight"></a>Verificar a disponibilidade de componentes do Hadoop no HDInsight

Cada versão do HDInsight é uma distribuição em nuvem de uma versão da HDP (Plataforma de Dados Hortonworks) e consiste em um conjunto de componentes do ecossistema do Hadoop. Veja [Controle de versão do componente HDInsight](../hdinsight-component-versioning.md) para obter detalhes sobre todos os componentes do HDInsight e suas versões atuais.

Você também pode usar a interface do usuário do Ambari ou a API REST do Ambari para verificar os componentes e versões do Hadoop no HDInsight.

Aplicativos ou componentes que estavam disponíveis em clusters locais, mas que não fazem parte dos clusters do HDInsight, podem ser adicionados em um nó de borda ou em uma VM na mesma rede virtual que o cluster HDInsight. Um aplicativo Hadoop de terceiros que não está disponível no Azure HDInsight pode ser instalado usando a opção "Aplicativos" no cluster do HDInsight. Aplicativos personalizados do Hadoop podem ser instalados no cluster do HDInsight usando "ações de script". A tabela a seguir lista alguns dos aplicativos comuns e suas opções de integração do HDInsight:

|**Aplicativo**|**Integração**
|---|---|
|Fluxo de ar|Nó de Borda do HDInsight ou IaaS
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Nenhum (Somente HDP)
|Datameer|Nó de Borda do HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB, uma alternativa no Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB, uma alternativa no Azure)
|NiFi|IaaS 
|Pronto|Nó de Borda do HDInsight ou IaaS
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW, uma alternativa no Azure)
|Tableau|IaaS 
|Waterline|Nó de Borda do HDInsight
|StreamSets|Borda do HDInsight 
|Palantir|IaaS 
|Sailpoint|IaaS 

Para obter mais informações, confira o artigo [Componentes do Hadoop disponíveis com diferentes versões do HDInsight](../hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Personalizar clusters do HDInsight utilizando ações de script

O HDInsight fornece um método de configuração de cluster chamado de **ação de script**. Uma ação de script é um script Bash executado em nós em um cluster do HDInsight e pode ser usado para instalar componentes adicionais e alterar as definições de configuração.

Ações de script devem estar armazenadas em um URI que pode ser acessado do cluster HDInsight. Podem ser usadas durante ou após a criação do cluster e também podem ser restrita para a execução apenas de determinados tipos de nó.

O script pode ser persistentes ou executado uma vez. Os scripts persistentes são usados para personalizar novos nós de trabalho adicionados ao cluster por meio de operações de colocação em escala. Um script persistente também pode aplicar alterações a outro tipo de nó, como um nó de cabeçalho, quando ocorrem operações de dimensionamento.

O HDInsight fornece scripts pré-escritos para instalar os seguintes componentes em clusters do HDInsight:

- Adicionar uma conta de Armazenamento do Azure
- Instalar o Hue
- Instalar o Presto
- Instalar o Solr
- Instalar o Giraph
- Pré-carregar bibliotecas Hive
- Instalar ou atualizar o Mono

> [!Note]
> O HDInsight não fornece suporte direto para componentes do Hadoop personalizados ou componentes instalados usando ações de script.

Ações de script também podem ser publicadas no Azure Marketplace como um aplicativo do HDInsight.

Para obter mais informações, consulte os seguintes artigos:

- [Instalar Aplicativos de terceiros do Hadoop no HDInsight](../hdinsight-apps-install-applications.md)
- [Personalizar clusters do HDInsight utilizando ações de script](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publicar um aplicativo HDInsight no Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Personalizar configurações do HDInsight usando Inicialização

Alterações em configurações nos arquivos de configuração, como `core-site.xml`, `hive-site.xml` e `oozie-env.xml`, podem ser feitas usando a inicialização. O script a seguir é um exemplo usando o PowerShell:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzureRmHDInsightClusterConfig '
    | Set—AzureRmHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzureRmHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzureRmHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Para saber mais, confira o artigo [Personalizar clusters HDInsight usando a Inicialização](../hdinsight-hadoop-customize-cluster-bootstrap.md)

## <a name="use-edge-nodes-on-hadoop-clusters-in-hdinsight-to-access-the-client-tools"></a>Usar nós de borda em clusters Hadoop no HDInsight para acessar as ferramentas de cliente

Um nó de borda vazio é uma máquina virtual do Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos nós de cabeçalho, mas sem serviços do Hadoop em execução. O nó de borda pode ser usado para as seguintes finalidades:

- como acessar o cluster
- testando aplicativos cliente
- hospedando aplicativos cliente

Os nós de borda podem ser criados e excluídos por meio do portal do Azure e usados durante ou após a criação do cluster. Depois de criar um nó de borda, você pode conectar-se a ele usando SSH e executar as ferramentas de cliente para acessar o cluster Hadoop no HDInsight. O ponto de extremidade SSH do nó de borda é `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.

Para obter mais informações, confira o artigo [Usar nós de borda vazios em clusters Hadoop no HDInsight](../hdinsight-apps-use-edge-node.md)

## <a name="use-the-scale-up-and-scale-down-feature-of-clusters"></a>Usar o recurso de ampliação e redução de clusters

O HDInsight proporciona elasticidade, oferecendo a opção de escalar e reduzir verticalmente o número de nós de trabalho em seus clusters. Esse recurso permite que você reduza um cluster depois do horário comercial ou nos finais de semana e o expanda durante picos da demanda empresarial.

A colocação em escala do cluster pode ser automatizada usando os seguintes métodos:

### <a name="powershell-cmdlet"></a>Cmdlet do Powershell

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

### <a name="azure-cli"></a>CLI do Azure

```powershell
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```

### <a name="azure-portal"></a>Portal do Azure

Ao adicionar nós ao cluster HDInsight em execução, trabalhos pendentes ou em execução não serão afetados. Novos trabalhos podem ser enviados com segurança enquanto o processo de colocação em escala está em execução. Se as operações de dimensionamento falharem por qualquer motivo, a falha será sutilmente tratada, mantendo o cluster em um estado funcional.

No entanto, se você estiver reduzindo o cluster removendo nós, todos os trabalhos pendentes ou em execução falharão quando operação de colocação em escala for concluída. Essa falha é causada pelo reinício de alguns dos serviços durante o processo. Para resolver esse problema, você pode aguardar a conclusão dos trabalhos antes de reduzir verticalmente o cluster, encerrar os trabalhos manualmente ou enviar novamente os trabalhos após a conclusão da operação de colocação em escala.

Se você reduzir o cluster ao mínimo de apenas um nó de trabalho, o HDFS pode ficar preso no modo de segurança quando os nós de trabalho forem reiniciados para a aplicação de patches ou imediatamente após a operação de colocação em escala. Você pode executar o seguinte comando para retirar o HDFS do modo de segurança:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Depois de sair do modo de segurança, você pode remover manualmente os arquivos temporários ou aguardar que o Hive limpe-os automaticamente.

Para saber mais, confira o artigo [Dimensionar clusters HDInsight](../hdinsight-scaling-best-practices.md)

## <a name="use-hdinsight-with-azure-virtual-network"></a>Usar o HDInsight com a Rede Virtual do Azure

Redes virtuais do Azure habilitam recursos do Azure, como máquinas virtuais do Azure, a comunicarem-se com segurança entre si, com a Internet e redes locais filtrando e roteando o tráfego de rede.

Usar a Rede Virtual do Azure com o HDInsight permite os seguintes cenários:

- Conectar-se ao HDInsight diretamente em uma rede local.
- Conectar o HDInsight a armazenamentos de dados em uma Rede virtual do Azure.
- Acessar diretamente os serviços do Hadoop que não estão disponíveis publicamente na Internet. Por exemplo, as APIs do Kafka ou a API Java do HBase.

O HDInsight pode ser adicionado a uma Rede Virtual do Azure nova ou existente. Se o HDInsight estiver sendo adicionado a uma Rede Virtual existente, os grupos de segurança de rede e as rotas definidas pelo usuário existentes precisarão ser atualizados para permitir o acesso irrestrito a [vários endereços IP](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip-1) no data center do Azure. Além disso, não bloqueie tráfego para as [portas](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ports) que estão sendo usadas pelos serviços do HDInsight.

> [!Note]
> O HDInsight no momento não dá suporte a túnel forçado. O túnel forçado é uma configuração de sub-rede que força o tráfego da Internet de saída para um dispositivo para inspeção e log. Remova o túnel forçado antes de instalar o HDInsight em uma sub-rede ou crie uma nova sub-rede para o HDInsight. O HDInsight também não dá suporte a restringir a conectividade de rede de saída.

Para obter mais informações, consulte os seguintes artigos:

- [Visão-geral-de-redes-virtuais do Azure](../../virtual-network/virtual-networks-overview.md)
- [Estender o Azure HDInsight usando uma Rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md)

## <a name="use-azure-virtual-network-service-endpoints-to-securely-connect-to-other-azure-services"></a>Usar pontos de extremidade de serviço da Rede Virtual do Azure para se conectar com segurança a outros serviços do Azure

O HDInsight dá suporte a [pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) que permitem que você se conecte com segurança ao Armazenamento de Blobs do Azure, ao Azure Data Lake Storage Gen2, ao Cosmos DB e a bancos de dados SQL. Ao habilitar um ponto de extremidade de serviço para o Azure HDInsight, o tráfego flui por meio de uma rota protegida de dentro do data center do Azure. Com este nível aprimorado de segurança na camada de rede, você pode bloquear contas de armazenamento de Big Data para suas VNETs (redes virtuais) especificadas e ainda usar clusters HDInsight diretamente para acessar e processar os dados.

Para obter mais informações, consulte os seguintes artigos:

- [Pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Aumentar a segurança do HDInsight com pontos de extremidade de serviço](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/.md)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Conectar o HDInsight à rede local

O HDInsight pode ser conectado à rede local por meio de redes virtuais do Azure e um gateway de VPN. As etapas a seguir podem ser usadas para estabelecer a conectividade:

- Use o HDInsight em uma Rede Virtual do Azure que tenha conectividade com a rede local.
- Como configurar a resolução de nome DNS entre a rede virtual e sua rede local.
- Configurar grupos de segurança de rede ou UDRs (rotas definida pelo usuário) para controlar o tráfego de rede.

Para obter mais informações, confira o artigo [Conectar o HDInsight à sua rede local](../connect-on-premises-network.md)

## <a name="next-steps"></a>Próximas etapas

Leia o próximo artigo desta série:

- [Melhores práticas de armazenamento para migração do local para o Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md)