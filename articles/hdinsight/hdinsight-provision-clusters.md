<properties
   pageTitle="Criar clusters Hadoop baseados no Windows no HDInsight | Microsoft Azure"
   	description="Aprenda a criar clusters do Azure HDInsight."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/21/2016"
   ms.author="jgao"/>

# Crie clusters Hadoop baseados no Windows no HDInsight

[AZURE.INCLUDE [seletor](../../includes/hdinsight-selector-create-clusters.md)]

Um cluster Hadoop é composto por várias máquinas virtuais (nós) que são usadas para processamento distribuído de tarefas no cluster. O Azure abstrai os detalhes da implementação da instalação e configuração de nós individuais, portanto você precisa fornecer informações de configuração geral. Neste artigo, você aprenderá a usar essas definições de configuração.

>[AZURE.NOTE] As informações contidas neste documento são específicas de clusters HDInsight baseados em Windows. Para obter informações sobre clusters baseados em Linux, veja [Create Linux-based Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

##Tipos de cluster

Atualmente, o HDInsight fornece quatro tipos diferentes de clusters, cada um com um conjunto de componentes para fornecer certas funcionalidades:

| Tipo de cluster | Use se quiser... |
| ------------ | ----------------------------- |
| O Hadoop | consulta e análise (trabalhos em lotes) |
| HBase | Armazenamento de dados NoSQL |
| Storm | Processamento de eventos em tempo real |
| Spark (visualização) | Processamento na memória, consultas interativas, processamento de transmissão de microlotes |

Cada tipo de cluster tem sua própria terminologia para nós no cluster, bem como o número de nós e o tamanho da VM padrão para cada tipo de nó:

| Tipo| Nós (número de nós)| Diagrama|
|-----|------|--------|
|O Hadoop| Nó de cabeçalho (2), Nó de dados (1+)|![Nós de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|Servidor de cabeçalho (2), Servidor de região (1 +), Nó mestre/do Zookeeper (3)|![Nós de cluster HBase do HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Storm|Nó do Nimbus (2), Servidor do supervisor (1+), Nó do Zookeeper (3)|![Nós de cluster Storm do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Spark|Nó de cabeçalho (2), Nó de trabalho (1+), Nó do Zookeeper (3) (gratuito para tamanho de VM A1 do Zookeepers)|![Nós de cluster Spark do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|

* Entre parênteses está o número de nós para cada tipo de nó.

> [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.

É possível adicionar outros componentes, como Hue ou R, a esses tipos básicos usando as [Ações de Script](#customize-clusters-using-script-action).

## Opções de configuração básicas

Veja a seguir as opções de configuração básica para criar um cluster HDInsight.

- **Nome do cluster**

	O nome do cluster é usado para identificar um cluster. O nome do cluster deve ser globalmente exclusivo e deve seguir as seguintes diretrizes de nomenclatura:

	- O campo deve ser uma cadeia com 3 a 63 caracteres
	- O campo pode conter apenas letras, números e hifens.

- **Tipo de cluster**

    Veja [Tipos de cluster](#cluster-types).

- **Sistema operacional**

	É possível criar clusters HDInsight em um dos dois sistemas operacionais a seguir:
	- **HDInsight no Linux (Ubuntu 12.04 LTS para Linux)**: o HDInsight fornece a opção de configuração de clusters do Linux no Azure. Configure um cluster do Linux, se você estiver familiarizado com o Linux ou Unix, migre de uma solução Hadoop baseada em Linux existente ou deseja fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Para obter mais informações, consulte [Introdução ao Hadoop no Linux em HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
	- **HDInsight no Windows (Windows Server 2012 R2 Datacenter)**:
    
- **Versão do HDInsight**

	É usada para determinar a versão do HDInsight a ser usada para este cluster. Para obter mais informações, confira [Versões e componentes do cluster Hadoop no HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Nome da assinatura**

	Cada cluster HDInsight é vinculado a uma assinatura do Azure.
    
- **Nome do grupo de recursos**

	O [ARM (Azure Resource Manager)](resource-group-overview.md) permite trabalhar com os recursos em seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada.

- **Credenciais**

	Os clusters HDInsight permitem configurar duas contas de usuário durante a criação de cluster:

	- Usuário HTTP. O nome de usuário padrão é admin, com a configuração básica do Portal do Azure. Às vezes, ele é chamado “Usuário de cluster”.
	- Usuário RDP (clusters do Windows): é usado para conectar-se ao cluster usando o RDP. Quando cria a conta, você deve definir uma data de validade que esteja dentro de 90 dias a partir do dia atual.
	- Usuário SSH (clusters do Linux): é usado para conectar-se ao cluster usando SSH. Você pode criar contas de usuário SSH adicionais depois que o cluster for criado ao seguir as etapas em [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, do Unix ou do OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

- **Fonte de dados**

	O HDFS original usa vários discos locais no cluster. O HDInsight usa o armazenamento de Blobs do Azure em vez do armazenamento de dados. O armazenamento de blobs do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. Através de uma interface HDFS (Sistema de Arquivos Distribuído Hadoop), o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados não estruturados do armazenamento de Blobs. O armazenamento de dados no armazenamento de blobs permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

	Durante a configuração, você deve especificar uma conta de armazenamento do Azure e um contêiner de armazenamento de Blobs do Azure na conta de armazenamento do Azure. Alguns processos de criação requerem que a conta de armazenamento do Azure e o contêiner de armazenamento de Blobs sejam criados anteriormente. O contêiner de armazenamento de Blobs é usado como o local de armazenamento padrão pelo cluster. Ou você pode especificar contas de armazenamento do Azure adicionais (armazenamento vinculado) que poderão ser acessadas pelo cluster. Além disso, o cluster também pode acessar quaisquer contêineres de Blob configurados com acesso de leitura público completo ou acesso de leitura público somente para blobs. Para obter mais informações sobre o acesso restrito, confira [Gerenciar o acesso aos recursos do Armazenamento do Azure](storage-manage-access-to-resources.md).

	![Armazenamento do HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Um contêiner de armazenamento de Blobs oferece o agrupamento de um conjunto de blobs, conforme mostrado na imagem:

	![Armazenamento do blob do Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

    Não é recomendável usar o contêiner de Blob padrão para armazenar dados corporativos. É uma prática recomendada excluir o contêiner de Blob padrão após cada uso para reduzir o custo de armazenamento. Observe que o contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.
    
	>[AZURE.WARNING] Não há suporte para o compartilhamento de um contêiner do armazenamento de Blobs para vários clusters.

	Para obter mais informações sobre o uso de repositórios de blobs secundários, consulte [Usando o Armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).

    Além do Armazenamento de blobs do Azure, também é possível usar o [repositório Azure Data Lake](data-lake-store-overview.md) como a conta de armazenamento padrão do cluster HBase no HDInsight e como armazenamento vinculado para todos os quatro tipos de cluster HDInsight. Para obter instruções, veja [Criar um cluster HDInsight com o Repositório Data Lake usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
    
- **Local (Região)**

	O cluster HDInsight e sua conta de armazenamento padrão devem estar localizados no mesmo local do Azure.
	
	![Regiões do Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Para ver uma lista das regiões com suporte, clique na lista suspensa **Região** em [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Tipos de preço do nó**

    Os clientes são cobrados pelo uso desses nós pela duração da vida útil do cluster. A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído (clusters não podem ser desalocados ou colocados em espera).

	Diferentes tipos de cluster têm diferentes tipos de nó, números de nós e tamanhos de nós. Por exemplo, um tipo de cluster Hadoop tem dois _nós principais_ e um padrão de quatro _nós de dados_, enquanto um tipo de cluster Storm tem dois _nós nimbus_, três _nós zookeeper_ e um padrão de quatro _nós supervisores_. O custo de clusters HDInsight é determinado pelo número de nós e pelos tamanhos de máquinas virtuais para os nós. Por exemplo, se você souber que executará operações que precisam de muita memória, convém selecionar um recurso de computação com mais memória. Para fins de aprendizado, é recomendável usar 1 nó de dados. Para obter mais informações sobre os preços do HDInsight, confira [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

	>[AZURE.NOTE] O limite de tamanho do cluster varia segundo as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.
	
    >Os nós usados pelo cluster não contam como Máquinas Virtuais, pois as imagens das Máquinas Virtuais usadas para os nós são um detalhe de implementação do serviço HDInsight. No entanto, os núcleos de computação usados pelos nós contam para o número total de núcleos de computação disponíveis para sua assinatura. Você pode ver o número de núcleos que será usado pelo cluster, bem como o número de núcleos disponíveis, na seção Resumo da folha Tipos de Preço, ao criar um cluster HDInsight.

	Ao usar o portal do Azure para configurar o cluster, o tamanho do Nó estará disponível por meio da folha __Tipo de Preço do Nó__ e também exibirá o custo associado aos diferentes tamanhos de nó. A seguinte captura de tela mostra as opções para um cluster Hadoop baseado em Linux:

	![tamanhos de nó de vm do hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

    As tabelas a seguir mostram os tamanhos com suporte dos clusters HDInsight e as capacidades que eles fornecem.

    - Camada Standard: série A

        No modelo de implantação clássica, alguns tamanhos de VM são ligeiramente diferentes no PowerShell e na CLI.

        * Standard\_A3 é grande
        * Standard\_A4 é ExtraLarge

        <br>

        |Tamanho |Núcleos de CPU|Memória|NICs (Máx.)|Tamanho máximo do disco|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
        |---|---|---|---|---|---|---|
        |Standard\_A3\\Grande|4|7 GB|2|Temporário = 285 GB |8|8 x 500|
        |Standard\_A4\\ExtraLarge|8|14 GB|4|Temporário = 605 GB |16|16 x 500|
        |Standard\_A6|4|28 GB|2|Temporário = 285 GB |8|8 x 500|
        |Standard\_A7|8|56 GB|4|Temporário = 605 GB |16|16 x 500|


    - Camada Standard: série D

        |Tamanho |Núcleos de CPU|Memória|NICs (Máx.)|Tamanho máximo do disco|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
        |---|---|---|---|---|---|---|
        |Standard\_D3 |4|14 GB|4|Temporário (SSD) =200 GB |8|8 x 500|
        |Standard\_D4 |8|28 GB|8|Temporário (SSD) =400 GB |16|16 x 500|
        |Standard\_D12 |4|28 GB|4|Temporário (SSD) =200 GB |8|8 x 500|
        |Standard\_D13 |8|56 GB|8|Temporário (SSD) =400 GB |16|16 x 500|
        |Standard\_D14 |16|112 GB|8|Temporário (SSD) =800 GB |32|32 x 500|

    - Camada Standard: série Dv2

        |Tamanho |Núcleos de CPU|Memória|NICs (Máx.)|Tamanho máximo do disco|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
        |---|---|---|---|---|---|---|
        |Standard\_D3\_v2 |4|14 GB|4|Temporário (SSD) =200 GB |8|8 x 500|
        |Standard\_D4\_v2 |8|28 GB|8|Temporário (SSD) =400 GB |16|16 x 500|
        |Standard\_D12\_v2 |4|28 GB|4|Temporário (SSD) =200 GB |8|8 x 500|
        |Standard\_D13\_v2 |8|56 GB|8|Temporário (SSD) =400 GB |16|16 x 500|
        |Standard\_D14\_v2 |16|112 GB|8|Temporário (SSD) =800 GB |32|32 x 500|    
 
    Para obter considerações de implantação a ser observadas ao planejar o uso desses recursos, veja [Tamanhos das máquinas virtuais](../virtual-machines/virtual-machines-size-specs.md). Para obter informações sobre o preço dos vários tamanhos, veja [Preço do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight)
    
	> [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM. A cobrança é iniciada quando um cluster é criado e só para quando o cluster é excluído. Para obter mais informações sobre preços, consulte [Detalhes de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## Usar armazenamento adicional

Em alguns casos, você poderá adicionar mais armazenamento ao cluster. Por exemplo, se você tiver várias Contas do Armazenamento do Azure para diferentes regiões geográficas ou para diferentes serviços, mas desejar analisá-los com o HDInsight.

Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o armazenamento de Blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter mais informações sobre como usar repositórios Data Lake secundários, veja [Criar clusters HDInsight com o Repositório Data Lake usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


## Usar o Hive/Oozie metastore

É altamente recomendável usar um metastore personalizado se desejar manter as tabelas do Hive depois de excluir o cluster HDInsight, para fins de anexação desse metastore a outro cluster HDInsight no futuro.

O metastore contém metadados de Hive e Oozie, como tabelas Hive, partições, esquemas e colunas. Usar o metastore ajuda a manter seus metadados de Hive e Oozie, para que você não precise recriar tabelas Hive ou trabalhos do Oozie ao criar um novo cluster. Por padrão, o Hive usa um banco de dados SQL do Azure integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído. Por exemplo, você tem um cluster criado com um metastore do Hive. Você criou algumas tabelas do Hive. Depois de excluir o cluster e recriá-lo usando o mesmo Hive metastore, você poderá ver as tabelas do Hive criadas no cluster original.

> [AZURE.NOTE] A configuração de Metastore não está disponível para tipos de cluster HBase.

## Usar redes virtuais do Azure

Uma [Rede virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite criar uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

    | Configuração site a site | Configuração ponto a site |
    | -------------------------- | --------------------------- |
    | A configuração site a site permite conectar vários recursos de seu datacenter à rede virtual do Azure usando uma VPN de hardware ou o Serviço de roteamento e acesso remoto.<br />![diagrama da configuração site a site](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | A configuração ponto a site permite que você conecte um recurso específico à rede virtual do Azure usando uma VPN do software.<br />![diagrama da configuração ponto a site](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Para obter mais informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, veja [Estender as funcionalidades do HDInsight usando uma Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

## Personalizar clusters usando a personalização de clusters do HDInsight (inicialização)

Às vezes, você deseja configurar os arquivos de configuração:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Para manter as alterações durante o tempo de vida dos clusters, é possível usar a personalização de cluster HDInsight durante o processo de criação ou o Ambari em clusters baseados em Linux com segurança. Para obter mais informações, veja [Personalizar clusters HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).

>[AZURE.NOTE] Os clusters baseados em Windows não podem reter as alterações devido à recriação das imagens. Para obter mais informações, consulte [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para manter as alterações durante o tempo de vida dos clusters, é possível usar a personalização do cluster HDInsight durante o processo de criação.

## Personalizar clusters usando a ação de Script

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante a criação. Esses scripts são invocados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no Portal, os cmdlets do Windows PowerShell do HDInsight ou o SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).



## Métodos de criação de cluster

Neste artigo, você aprendeu as informações básicas sobre como criar um cluster HDInsight baseado em Windows. Use a tabela abaixo para obter informações específicas sobre como criar um cluster usando um método que melhor atenda às suas necessidades:

| Use esta opção para criar clusters... | Usar um navegador da Web... | Usar a linha de comando | Usar a API REST | Usar o SDK | No Linux, Mac OS X ou Unix | No Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portal do Azure](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [CLI do Azure](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [PowerShell do Azure](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [SDK .NET](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Modelos de ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!-----------HONumber=AcomDC_0330_2016-->