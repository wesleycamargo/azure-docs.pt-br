---
title: "Configurar a replicação de HBase | Microsoft Docs"
description: "Saiba como configurar a replicação de HBase para balanceamento de carga, alta disponibilidade, migração/atualização de uma versão do HDInsight para outra sem tempo de inatividade e recuperação de desastres."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ec82fb896bc6c7212660746445af940f52546ad9
ms.openlocfilehash: 62ded222bc72ded7c6ce51efe911dd84992c05b1


---
# <a name="configure-hbase-replication"></a>Configurar replicação HBase

Saiba como configurar a replicação de HBase em uma rede virtual (VNet) ou entre duas redes virtuais.

A replicação de cluster usa uma metodologia de envio de origem. Um cluster HBase pode ser uma fonte, um destino ou pode atender a ambas as funções de uma vez. A replicação é assíncrona e o objetivo da replicação é a consistência eventual. Quando a origem recebe uma edição para uma família de coluna com replicação habilitada, essa edição é propagada para todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e todos os clusters que já consumiram os dados são rastreados para evitar loops de replicação.

Neste tutorial, você configurará uma replicação de origem/destino. Para obter outras topologias de cluster, consulte o [Guia de referência do Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Casos de uso de replicação de HBase para uma única rede virtual:

* Balanceamento de carga: por exemplo, executando verificações ou trabalhos MapReduce no cluster de destino e ingestão de dados no cluster de origem
* Alta disponibilidade
* Migrar dados de um cluster de HBase para outro
* Atualizar um cluster HDInsight do Azure de uma versão para outra

Casos de uso de replicação de HBase para duas redes virtuais:

* Recuperação de desastre
* Balanceamento de carga e particionamento do aplicativo
* Alta disponibilidade

Replicar clusters usando scripts [ação de script](hdinsight-hadoop-customize-cluster-linux.md) localizados no [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deverá ter uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="configure-the-environments"></a>Configurar os ambientes

Há três opções de configuração possíveis:

- Dois clusters de HBase em uma rede virtual do Azure
- Dois clusters de HBase em duas redes virtuais diferentes na mesma região
- Dois clusters de HBase em duas redes virtuais diferentes em duas regiões diferentes (replicação geográfica)

Para facilitar a configuração dos ambientes, nós criamos alguns [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Se você preferir configurar os ambientes usando outros métodos, veja:

- [Criar clusters Hadoop baseados em Linux em HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Criar clusters HBase na rede virtual do Azure](hdinsight-hbase-provision-vnet.md)

### <a name="configure-one-virtual-network"></a>Configurar uma rede virtual

Clique na imagem a seguir para criar dois clusters HBase na mesma rede virtual. O modelo está localizado em um contêiner de blob público do Azure.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="configure-two-virtual-networks-in-the-same-region"></a>Configurar duas redes virtuais na mesma região

Clique na imagem a seguir para criar duas redes virtuais com emparelhamento VNet e dois clusters HBase na mesma região. O modelo está localizado em um contêiner de blob público do Azure.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-replication-with-two-vnets-peering-in-one-region.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Esse cenário requer o [emparelhamento VNet](../virtual-network/virtual-network-peering-overview.md). O modelo permite o emparelhamento VNet.   

A replicação de HBase usa endereços IP das VMs do ZooKeeper. Você deve configurar endereços IP estáticos para os nós de destino do HBase ZooKeeper.

**Para configurar endereços IP estáticos**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Grupos de recursos**.
3. Clique em seu grupo de recursos que contém o cluster HBase de destino. Esse é o grupo de recursos que você especificou ao usar o modelo do Resource Manager para criar o ambiente. Você pode usar o filtro para restringir a lista. É possível exibir uma lista de recursos que contém as duas redes virtuais.
4. Clique na rede virtual que contém o cluster HBase de destino. Por exemplo, clique em **xxxx-vnet2**. É possível ver três dispositivos com nomes que começam com **nic-zookeepermode-**. Esses dispositivos são as três VMs do ZooKeeper.
5. Clique em uma das VMs do ZooKeeper.
6. Clique em **Configurações de IP**.
7. Clique em **ipConfig1** na lista.
8. Clique em **Estático** e anote o endereço IP real. Você precisará do endereço IP ao executar a ação de script para habilitar a replicação.

  ![IP estático do ZooKeeper para replicação de HBase no HDInsight](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Repita a etapa 6 para definir o endereço IP estático para os outros dois nós ZooKeeper.

Para o cenário de rede virtual cruzada, você deve usar a opção **-ip** ao chamar a ação de script **hdi_enable_replication.sh**.

### <a name="configure-two-virtual-networks-in-two-different-regions"></a>Configurar duas redes virtuais em duas regiões diferentes

Clique na imagem a seguir para criar duas redes virtuais em duas regiões diferentes. O modelo está localizado em um contêiner de blob público do Azure.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Crie um gateway de VPN entre duas redes virtuais. Para obter instruções, veja [Criar uma VNet com uma conexão site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

A replicação de HBase usa endereços IP das VMs do ZooKeeper. Você deve configurar endereços IP estáticos para os nós de destino do HBase ZooKeeper. Para configurar o endereço IP estático, veja a seção "Configurar duas redes virtuais na mesma região" neste artigo.

Para o cenário de rede virtual cruzada, você deve usar a opção **-ip** ao chamar a ação de script **hdi_enable_replication.sh**.

## <a name="load-test-data"></a>Carregar dados de teste

Ao replicar um cluster, você deve especificar as tabelas a serem replicadas. Nesta seção, você carregará alguns dados no cluster de origem. Na próxima seção, você habilitará a replicação entre os dois clusters.

Siga as instruções em [Tutorial do HBase: Introdução ao uso do Apache HBase com o Hadoop baseado em Linux no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md) para criar uma tabela **Contatos** e inserir alguns dados na tabela.

## <a name="enable-replication"></a>Habilitar a replicação

As etapas a seguir mostram como chamar o script de ação de script no Portal do Azure. Para executar uma ação de script usando o Azure PowerShell e a CLI (interface de linha de comando) do Azure, veja [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

**Para habilitar a replicação de HBase no Portal do Azure**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HBase de origem.
3. No menu do cluster, clique em **Ações de Script**.
4. Clique em **Enviar Novo** na parte superior da folha.
5. Selecione ou insira as seguintes informações:

  - **Nome**: insira **Habilitar a replicação**.
  - **URL do Script Bash**: insira **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  - **Cabeçalho**: selecionado. Desmarque os outros tipos de nós.
  - **Parâmetros**: os seguintes parâmetros de exemplo habilitam a replicação de todas as tabelas existentes e copiam todos os dados do cluster de origem para o cluster de destino:

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. Clique em **Criar**. O script pode demorar, especialmente quando o argumento -copydata for usado.

Argumentos necessários:

|Nome|Descrição|
|----|-----------|
|-s, --src-cluster | Especifique o nome DNS do cluster HBase de origem. Por exemplo: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Especifique o nome DNS do cluster HBase de destino (réplica). Por exemplo: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Especifique a senha de administrador para Ambari no cluster HBase de origem. |
|-dp, --dst-ambari-password | Especifique a senha de administrador para Ambari no cluster HBase de destino.|

Argumentos opcionais:

|Nome|Descrição|
|----|-----------|
|-su, --src-ambari-user | Especifique o nome de usuário de administrador para Ambari no cluster HBase de origem. O valor padrão é **admin**. |
|-du, --dst-ambari-user | Especifique o nome de usuário de administrador para Ambari no cluster HBase de destino. O valor padrão é **admin**. |
|-t, --table-list | Especifique as tabelas a replicar. Por exemplo: --table-list="table1;table2;table3". Se você não especificar tabelas, todas as tabelas HBase existentes serão replicadas.|
|-m, --machine | Especifique o nó de cabeçalho em que a ação de script será executada. O valor é hn1 ou hn0. Como hn0 geralmente está mais ocupado, é recomendável usar hn1. Use essa opção quando estiver executando o script de $0 como uma ação de script do portal do HDInsight ou do Azure PowerShell.|
|-ip | Esse argumento é necessário quando você estiver habilitando a replicação entre duas redes virtuais. Esse argumento atua como uma opção para usar os IPs estáticos de nós ZooKeeper de clusters de réplica em vez de nomes FQDN. Os IPs estáticos precisam ser pré-configurados antes de habilitar a replicação. |
|-cp, -copydata | Habilite a migração dos dados existentes nas tabelas em que a replicação está habilitada. |
|-rpm, -replicate-phoenix-meta | Habilite a replicação nas tabelas do sistema Phoenix. <br><br>*Use esta opção com cuidado.* É recomendável que você recrie tabelas Phoenix em clusters de réplica antes de usar esse script. |
|-h, --help | Exibir informações de uso. |

A seção print_usage() do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) fornece uma explicação detalhada dos parâmetros.

Após a ação de script ser implantada com êxito, você pode usar SSH para se conectar ao cluster HBase de destino e verificar se os dados foram replicados.

### <a name="replication-scenarios"></a>Cenários de replicação

A lista a seguir mostra alguns casos de uso geral e suas configurações de parâmetro:

- **Habilitar a replicação em todas as tabelas entre os dois clusters**. Esse cenário não requer a cópia/migração dos dados existentes nas tabelas, e não usa tabelas Phoenix. Use os seguintes parâmetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Habilitar a replicação em tabelas específicas**. Use os parâmetros a seguir para habilitar a replicação em table1, table2 e table3:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Habilitar a replicação em tabelas específicas e copiar os dados existentes**. Use os parâmetros a seguir para habilitar a replicação em table1, table2 e table3:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Habilitar a replicação em todas as tabelas com metadados phoenix replicados da origem para o destino**. A replicação de metadados Phoenix não é ideal e deve ser habilitada com cuidado.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copiar e migrar dados

Há dois scripts de ação de script separados para copiar/migrar dados após a replicação ser habilitada:

- [Script para tabelas pequenas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (apenas alguns gigabytes de tamanho e deve levar menos que uma hora para copiar)

- [Script para tabelas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (devem levar mais de uma hora para copiar)

Você pode seguir o mesmo procedimento em [Habilitar replicação](#enable-replication) para chamar a ação de script com os seguintes parâmetros:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

A seção print_usage() do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) fornece uma descrição detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Copiar tabelas específicas (test1, test2 e test3) para todas as linhas editadas até o momento (carimbo de hora atual)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  ou o

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiar tabelas específicas com intervalo de tempo especificado**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Desabilitar a replicação

Para desabilitar a replicação, use outro script de ação de script localizado no [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Você pode seguir o mesmo procedimento em [Habilitar replicação](#enable-replication) para chamar a ação de script com os seguintes parâmetros:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari Password> <-all|-t "table1;table2;...">  

A seção print_usage() do [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) fornece uma explicação detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Desabilitar a replicação em todas as tabelas**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  ou o

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari username> --src-ambari-password=<source cluster Ambari password>

- **Desabilitar a replicação em tabelas específicas (table1, table2 e table3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar a replicação do HBase entre dois data centers. Para saber mais sobre HDInsight e HBase, consulte:

* [Introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started]
* [Visão geral do HDInsight HBase][hdinsight-hbase-overview]
* [Criar clusters HBase na rede virtual do Azure][hdinsight-hbase-provision-vnet]
* [Analisar o sentimento do Twitter em tempo real com o HBase][hdinsight-hbase-twitter-sentiment]
* [Analisar dados de sensor com o Storm e o HBase no HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md



<!--HONumber=Dec16_HO4-->


