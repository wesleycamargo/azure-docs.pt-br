<properties
   	pageTitle="Criar clusters Hadoop, HBase ou Storm ou Spark no Linux no HDInsight | Microsoft Azure"
   	description="Aprenda a criar clusters Hadoop, HBase ou Storm ou Spark no Linux para o HDInsight usando um navegador, a CLI do Azure, o Azure PowerShell, REST ou por meio de um SDK."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="07/08/2016"
   	ms.author="jgao"/>


# Criar clusters Hadoop baseados em Linux em HDInsight

[AZURE.INCLUDE [seletor](../../includes/hdinsight-selector-create-clusters.md)]

Um cluster Hadoop é composto por várias máquinas virtuais (nós), usadas para processamento distribuído de tarefas no cluster. O Azure abstrai os detalhes da implementação da instalação e configuração de nós individuais, portanto você precisa fornecer informações de configuração geral. Neste artigo, você aprenderá a usar essas definições de configuração.

## Tipos de cluster

Atualmente, o Azure HDInsight fornece cinco tipos diferentes de clusters, cada um com um conjunto de componentes para fornecer certas funcionalidades.

| Tipo de cluster | Funcionalidade |
| ------------ | ----------------------------- |
| O Hadoop | Consulta e análise (trabalhos em lotes) |
| HBase | Armazenamento de dados NoSQL |
| Storm | Processamento de eventos em tempo real |
| Spark (visualização) | Processamento na memória, consultas interativas, processamento de transmissão de microlotes |
| R Server no Spark | Uma variedade de recursos de estatísticas de Big Data, modelagem preditiva e aprendizado de máquina |

Cada tipo de cluster tem seu próprio número de nós, terminologia para nós no cluster, bem como o tamanho da VM padrão para cada tipo de nó. Na tabela a seguir, o número de nós para cada tipo de nó está entre parênteses.

| Tipo| Nós | Diagrama|
|-----|------|--------|
|O Hadoop| Nó de cabeçalho (2), Nó de dados (1+)|![Nós de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|Servidor de cabeçalho (2), Servidor de região (1 +), Nó mestre/do Zookeeper (3)|![Nós de cluster HBase do HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Storm|Nó do Nimbus (2), Servidor do supervisor (1+), Nó do Zookeeper (3)|![Nós de cluster Storm do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Spark|Nó de cabeçalho (2), Nó de trabalho (1+), Nó do Zookeeper (3) (gratuito para tamanho de VM A1 do Zookeepers)|![Nós de cluster Spark do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|


A tabela a seguir lista os tamanhos de VM padrão para o HDInsight.

|Tipo de cluster|	O Hadoop|	HBase|	Storm|	Spark|
|------------|--------|------|-------|-------|
|Cabeçalho – Tamanho padrão da VM|	D3 |A3|	A3|	D12|
|Cabeçalho – Tamanhos recomendados de VM|	D3, D4, D12 |A3, A4, A5 |A3, A4, A5|	D12, D13, D14|
|Trabalho – Tamanho de VM padrão|	D3|	D3|	D3|	D12|
|Trabalho – Tamanhos de VM recomendados |	D3, D4, D12|	D3, D4, D12 |D3, D4, D12|	D12, D13, D14|
|Zookeeper – Tamanho de VM padrão| |	A2|	A2 | |
|Zookeeper – Tamanhos de VM recomendados | |A2, A3, A4 |A2, A3, A4 | |

Observe que esse cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm. O trabalhador é conhecido como *Região* para o tipo de cluster HBase e como *Supervisor* para o tipo de cluster Storm.



> [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.

É possível adicionar outros componentes, como Hue ou R a esses tipos básicos usando as [Ações de Script](#customize-clusters-using-script-action).

## Camadas de cluster

O Azure HDInsight oferece ofertas de nuvem de Big Data em duas categorias: Standard e [Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium). O HDInsight Premium inclui R e outros componentes adicionais. O HDInsight Premium tem suporte apenas no HDInsight versão 3.4.

A tabela a seguir lista o tipo de cluster do HDInsight e a matriz de suporte do HDInsight Premium.

| Tipo de cluster | Standard | Premium |
|--------------|---------------|--------------|
| O Hadoop | Sim | Sim |
| Spark | Sim | Sim |
| HBase | Sim | Não |
| Storm | Sim | Não |
| R Server no Spark | Não | Sim |

Essa tabela será atualizada conforme mais tipos de cluster forem incluídos no HDInsight Premium. A captura de tela a seguir mostra as informações do portal do Azure para escolher os tipos de cluster.

![Configuração do HDInsight premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


## Opções de configuração básicas

Veja a seguir as opções de configuração básica usadas para criar um cluster HDInsight.

### Nome do cluster ###

O nome do cluster é usado para identificar um cluster. O nome do cluster deve ser globalmente exclusivo e seguir as seguintes diretrizes de nomenclatura:

- O campo deve ser uma cadeia com três a 63 caracteres.
- O campo pode conter apenas letras, números e hifens.

### Tipo de cluster###

Consulte [Tipos de cluster](#cluster-types) e [Camadas de cluster](#cluster-tiers).

### Sistema operacional ###

É possível criar clusters HDInsight em um dos dois sistemas operacionais a seguir:

- HDInsight no Linux (Ubuntu 12.04 LTS para Linux). O HDInsight fornece a opção de configuração de clusters do Linux no Azure. Configure um cluster do Linux, se você estiver familiarizado com o Linux ou Unix, migre de uma solução Hadoop baseada em Linux existente ou deseja fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Para obter mais informações, consulte [Introdução ao Hadoop no Linux em HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
- HDInsight no Windows (Windows Server 2012 R2 Datacenter).

### Versão do HDInsight###

Ela é usada para determinar a versão do HDInsight a ser usada para esse cluster. Para saber mais, confira [Versões e componentes do cluster Hadoop no HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

### Nome da assinatura###

Cada cluster HDInsight é vinculado a uma assinatura do Azure.

### Nome do grupo de recursos ###

O [Azure Resource Manager](../resource-group-overview.md) ajuda você a trabalhar com os recursos de seu aplicativo como um grupo, chamados de Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada.

### Credenciais###

Com os clusters HDInsight, você pode configurar duas contas de usuário durante a criação de cluster:

- Usuário HTTP. O nome de usuário padrão é *admin*, com a configuração básica do Portal do Azure. Às vezes, ele é chamado "Usuário de cluster".
- Usuário SSH (clusters do Linux). Ele é usado para conectar-se ao cluster usando SSH. É possível criar outras contas de usuário SSH após a criação do cluster seguindo as etapas em [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-unix.md).

    >[AZURE.NOTE] Para os clusters baseados no Windows, você pode criar um usuário RDP para se conectar ao cluster usando o RDP.

### Fonte de dados###

O sistema de arquivos distribuído Hadoop (HDFS) original usa muitos discos locais no cluster. O HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados. O armazenamento de blobs do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. Ao usar uma interface HDFS, o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados não estruturados do armazenamento de Blobs. Armazenar dados no armazenamento de blobs permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

Durante a configuração, você deve especificar uma conta de armazenamento do Azure e um contêiner de armazenamento de Blobs do Azure na conta de armazenamento do Azure. Alguns processos de criação exigem que a conta de armazenamento do Azure e o contêiner de armazenamento de Blobs sejam criados anteriormente. O contêiner de armazenamento de Blobs é usado como o local de armazenamento padrão pelo cluster. Ou você pode especificar contas de armazenamento do Azure adicionais (armazenamento vinculado) que poderão ser acessadas pelo cluster. O cluster também pode acessar quaisquer contêineres de armazenamento de Blobs configurados com acesso de leitura público completo ou acesso de leitura público somente para blobs. Para saber mais, confira [Gerenciar o acesso aos recursos de Armazenamento do Azure](../storage/storage-manage-access-to-resources.md).

![Armazenamento do HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

>[AZURE.NOTE] Um contêiner de armazenamento de Blobs oferece o agrupamento de um conjunto de blobs, conforme mostrado na imagem a seguir.

![Armazenamento do blob do Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

Não recomendamos usar o contêiner de armazenamento de Blobs padrão para armazenar dados corporativos. É uma prática recomendada excluir o contêiner de armazenamento de Blobs padrão após cada uso para reduzir o custo de armazenamento. Observe que o contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

>[AZURE.WARNING] Não há suporte para o compartilhamento de um contêiner do armazenamento de Blobs para vários clusters.

Para saber mais sobre o uso de armazenamento de Blobs secundário, veja [Uso do Armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).

Além do armazenamento de Blobs do Azure, também é possível usar o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) como a conta de armazenamento padrão do cluster HBase no HDInsight e como um armazenamento vinculado para todos os quatro tipos de cluster HDInsight. Para saber mais, veja [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### Local (Região) ###

O cluster HDInsight e sua conta de armazenamento padrão devem estar localizados no mesmo local do Azure.

![Regiões do Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

Para ver uma lista das regiões com suporte, clique na lista suspensa **Região** em [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

### Tipos de preço do nó###

Os clientes são cobrados pelo uso desses nós pela duração da vida útil do cluster. A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído. Os clusters não podem ser desalocados ou colocados em espera.

Diferentes tipos de cluster têm diferentes tipos de nó, números de nós e tamanhos de nós. Por exemplo, um tipo de cluster Hadoop tem dois _nós principais_ e um padrão de quatro _nós de dados_, enquanto um tipo de cluster Storm tem dois _nós nimbus_, três _nós zookeeper_ e um padrão de quatro _nós supervisores_. O custo de clusters HDInsight é determinado pelo número de nós e pelos tamanhos de máquinas virtuais para os nós. Por exemplo, se você souber que executará operações que precisam de muita memória, convém selecionar um recurso de computação com mais memória. Para fins de aprendizado, é recomendável usar um nó de dados. Para obter mais informações sobre os preços do HDInsight, confira [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

>[AZURE.NOTE] O limite de tamanho do cluster varia segundo as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.

>Os nós usados pelo seu cluster não contam como máquinas virtuais, pois as imagens de máquina virtual usadas para os nós são um detalhe de implementação do serviço HDInsight. Os núcleos de computação usados por nós são contados em relação ao número total de núcleos de computação disponíveis para sua assinatura. Você pode ver o número de núcleos disponíveis e os núcleos que serão usados pelo cluster na seção de resumo da folha Tipos de Preço ao criar um cluster HDInsight.

Quando você usar o portal do Azure para configurar o cluster, o tamanho do nó está disponível na folha __Tipo de Preço de Nó__. Você também pode ver o custo associado aos diferentes tamanhos de nós. A captura de tela a seguir mostra as opções para um cluster Hadoop baseado em Linux.

![tamanhos de nó de vm do hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

As tabelas a seguir mostram os tamanhos com suporte dos clusters HDInsight e as capacidades que eles fornecem.

#### Camada Standard: série A####

No modelo de implantação clássica, alguns tamanhos de VM são ligeiramente diferentes no PowerShell e na CLI.
* Standard\_A3 é grande
* Standard\_A4 é ExtraLarge

|Tamanho |Núcleos de CPU|Memória|NICs (Máx.)|Tamanho máximo do disco|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|---|
|Standard\_A3\\Grande|4|7 GB|2|Temporário = 285 GB |8|8 x 500|
|Standard\_A4\\ExtraLarge|8|14 GB|4|Temporário = 605 GB |16|16 x 500|
|Standard\_A6|4|28 GB|2|Temporário = 285 GB |8|8 x 500|
|Standard\_A7|8|56 GB|4|Temporário = 605 GB |16|16 x 500|


#### Camada Standard: série D####

|Tamanho |Núcleos de CPU|Memória|NICs (Máx.)|Tamanho máximo do disco|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|---|
|Standard\_D3 |4|14 GB|4|Temporário (SSD) =200 GB |8|8 x 500|
|Standard\_D4 |8|28 GB|8|Temporário (SSD) =400 GB |16|16 x 500|
|Standard\_D12 |4|28 GB|4|Temporário (SSD) =200 GB |8|8 x 500|
|Standard\_D13 |8|56 GB|8|Temporário (SSD) =400 GB |16|16 x 500|
|Standard\_D14 |16|112 GB|8|Temporário (SSD) =800 GB |32|32 x 500|

#### Camada Standard: série Dv2####

|Tamanho |Núcleos de CPU|Memória|NICs (Máx.)|Tamanho máximo do disco|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|---|
|Standard\_D3\_v2 |4|14 GB|4|Temporário (SSD) =200 GB |8|8 x 500|
|Standard\_D4\_v2 |8|28 GB|8|Temporário (SSD) =400 GB |16|16 x 500|
|Standard\_D12\_v2 |4|28 GB|4|Temporário (SSD) =200 GB |8|8 x 500|
|Standard\_D13\_v2 |8|56 GB|8|Temporário (SSD) =400 GB |16|16 x 500|
|Standard\_D14\_v2 |16|112 GB|8|Temporário (SSD) =800 GB |32|32 x 500|    

Para obter as considerações de implantação a serem observadas ao planejar o uso desses recursos, confira [Tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md). Para obter informações sobre os preços dos vários tamanhos, veja [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

> [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.

A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído. Para obter mais informações sobre preços, consulte [Detalhes de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## Usar armazenamento adicional

Em alguns casos, você poderá adicionar mais armazenamento ao cluster. Por exemplo, você pode ter várias contas de armazenamento do Azure para diferentes regiões geográficas ou diferentes serviços, mas desejar analisá-los com o HDInsight.

Para saber mais sobre o armazenamento de Blobs secundário, veja [Usar o armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para saber mais sobre repositórios Data Lake secundários, veja [Criar clusters HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).


## Usar o Hive/Oozie metastore

É altamente recomendável que você use um metastore personalizado se quiser manter suas tabelas Hive depois de excluir o cluster HDInsight. Você poderá anexar esse metastore a outro cluster HDInsight.

> [AZURE.IMPORTANT] O metastore HDInsight não é compatível com versões anteriores. Por exemplo, você não pode usar um metastore de um cluster do HDInsight 3.4 para criar um cluster do HDInsight 3.3.

O metastore contém metadados de Hive e Oozie, como tabelas Hive, partições, esquemas e colunas. O metastore ajuda a manter seus metadados do Hive e Oozie, para que você não precise recriar tabelas Hive ou trabalhos do Oozie ao criar um novo cluster. Por padrão, o Hive usa um banco de dados SQL do Azure integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído. Quando você cria a tabela do Hive em um cluster HDInsight com um metastore do Hive configurado, as tabelas serão mantidas ao recriar o cluster usando o mesmo metastore do Hive.

A configuração de Metastore não está disponível para tipos de cluster HBase.

> [AZURE.IMPORTANT] Ao criar um metastore personalizado, não use um nome de banco de dados que contém a traços ou hifens. Isso pode fazer com que o processo de criação de cluster falhe.

## Usar redes virtuais do Azure

Com uma [rede virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/), você pode criar uma rede segura e persistente contendo os recursos necessários para sua solução. Com uma rede virtual, você pode:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede de datacenter local (site a site ou ponto a site), usando uma VPN (rede privada virtual).

| Configuração site a site | Configuração ponto a site |
| -------------------------- | --------------------------- |
| Com a configuração site a site, você pode conectar vários recursos do data center à rede virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.<br />![diagrama da configuração site a site](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | A configuração ponto a site permite que você conecte um recurso específico à rede virtual do Azure usando uma VPN do software.<br />![diagrama da configuração ponto a site](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Os clusters baseados em Windows exigem uma rede virtual v1 (Clássica), enquanto os clusters baseados em Linux exigem uma rede virtual v2 (Azure Resource Manager). Se você não tiver o tipo correto de rede, ele não poderá ser usado durante a criação do cluster.

Para saber mais sobre como usar o HDInsight com uma rede virtual, incluindo requisitos de configuração específicos para a rede virtual, confira [Estender as funcionalidades do HDInsight usando uma rede virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

## Personalizar clusters usando a personalização de clusters do HDInsight (inicialização)

Às vezes, você deseja configurar os seguintes arquivos de configuração:

- clusterIdentity.xml
- core-site.xml
- gateway.xml
- hbase-env.xml
- hbase-site.xml
- hdfs-site.xml
- hive-env.xml
- hive-site.xml
- mapred-site
- oozie-site.xml
- oozie-env.xml
- storm-site.xml
- tez-site.xml
- webhcat-site.xml
- yarn-site.xml

Para manter as alterações durante o tempo de vida dos clusters, é possível usar a personalização de cluster HDInsight durante o processo de criação ou o Ambari em clusters baseados em Linux. Para saber mais, confira [Personalizar clusters HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).

>[AZURE.NOTE] Os clusters baseados em Windows não podem reter as alterações devido à recriação das imagens. Para obter mais informações, consulte [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para manter as alterações durante o tempo de vida dos clusters, é possível usar a personalização do cluster HDInsight durante o processo de criação.

## Personalizar clusters usando a Ação de Script

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante a criação. Tais scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no portal do Azure, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

Alguns componentes nativos do Java, como Mahout e Cascading, podem ser executados no cluster como arquivos Java Archive (JAR). Esses arquivos JAR podem ser distribuídos para o armazenamento de Blobs do Azure e enviados aos clusters HDInsight por meio de mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md).

>[AZURE.NOTE] Se você tiver problemas para implantar arquivos JAR nos clusters do HDInsight ou ao chamar arquivos JAR nesses clusters, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

> A cascata não tem suporte do HDInsight e não está qualificada para o Suporte da Microsoft. Para obter as listas dos componentes com suporte, veja [O que há de novo nas versões de cluster fornecidas pelo HDInsight?](hdinsight-component-versioning.md)

## Métodos de criação de cluster

Neste artigo, você aprendeu as informações básicas sobre como criar um cluster HDInsight baseado em Linux. Use a tabela a seguir para obter informações específicas sobre como criar um cluster usando um método que melhor atenda às suas necessidades.

| Clusters criados com | Navegador da Web | Linha de comando | API REST | . | Linux, Mac OS X ou Unix | Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [O portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) | ✔ | ✔ | ✔ |✔ | ✔ | ✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [PowerShell do Azure](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0803_2016-->