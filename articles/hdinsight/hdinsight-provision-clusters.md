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
   ms.date="01/22/2016"
   ms.author="jgao"/>

# Crie clusters Hadoop baseados no Windows no HDInsight

[AZURE.INCLUDE [seletor](../../includes/hdinsight-create-windows-cluster-selector.md)]

Aprenda a planejar para criar clusters do HDInsight.

###Pré-requisitos:

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opções de configuração básicas

Veja a seguir as opções de configuração básica para criar um cluster HDInsight.

- **Nome do cluster**

	O nome do cluster é usado para identificar um cluster. O nome do cluster deve seguir as diretrizes seguintes:

	- O campo deve ser uma cadeia com 3 a 63 caracteres
	- O campo pode conter apenas letras, números e hifens.

- **Nome da assinatura**

	Um cluster HDInsight é vinculado a uma assinatura do Azure.

- **Nome do grupo de recursos**

	O Gerenciador de Recursos do Azure (ARM) permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Para obter mais informações, veja [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md).
	
- **Sistema operacional**

	Você pode criar clusters HDInsight em um dos dois seguintes sistemas operacionais: - **HDInsight no Windows (Windows Server 2012 R2 Datacenter)**: - **HDInsight no Linux (Ubuntu 12.04 LTS para Linux)**: o HDInsight oferece a opção de configurar clusters Linux no Azure. Configure um cluster do Linux, se você estiver familiarizado com o Linux ou Unix, migre de uma solução Hadoop baseada em Linux existente ou deseja fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Para obter mais informações, consulte [Introdução ao Hadoop no Linux em HDInsight](hdinsight-hadoop-linux-get-started.md).

- **Tipo do cluster** e **tamanho do cluster (ou seja, nós de dados)**

	O HDInsight permite que os clientes implantem uma variedade de tipos de cluster, para diferentes cargas de trabalho de análise de dados. Os tipos do cluster oferecidos atualmente são:

	- Clusters do Hadoop: para cargas de trabalho de consulta e análise
	- Clusters do HBase: para cargas de trabalho NoSQL
	- Clusters do Storm: para cargas de trabalho de processamento de eventos em tempo real
	- Clusters do Spark (visualização): para cargas de trabalho de processamento na memória, consultas interativas, fluxo e aprendizado de máquina.

	![Clusters do HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] O *cluster Azure HDInsight* também é chamado de *clusters Hadoop no HDInsight* ou *cluster HDInsight*. Às vezes, ele é usado alternadamente com o *cluster Hadoop*. Todos eles denotam clusters do Hadoop hospedados no ambiente do Microsoft Azure.

	Em um determinado tipo cluster, há diferentes funções para os diferentes nós, que permitem que um cliente dimensione os nós em uma determinada função apropriada para os detalhes de sua carga de trabalho. Por exemplo, um cluster do Hadoop pode ter seus nós de trabalho criados com uma grande quantidade de memória se o tipo de análise executada tiver um uso intensivo de memória.

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Os clusters Hadoop para HDInsight são implantados com duas funções:

	- Nó principal (2 nós)
	- Nó de dados (pelo menos 1 nó)

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	Clusters do HBase para HDInsight são implantados com três funções: - Servidores principais (2 nós) - Servidores de região (pelo menos 1 nó) - Nós mestre/zookeeper (3 nós)

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Clusters do Storm para HDInsight são implantados com três funções: - Nós Nimbus (2 nós) - Servidores de supervisão (pelo menos 1 nó) - Nós zookeeper (3 nós)


	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Clusters do Spark para HDInsight são implantados com três funções: - Nó principal (2 nós) - Nó de trabalho (pelo menos 1 nó) - Nós zookeeper (3 nós) (gratuito para Zookeepers A1)

	Os clientes são cobrados pelo uso desses nós pela duração da vida útil do cluster. A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído (clusters não podem ser desalocados ou colocados em espera). O tamanho do cluster afeta seu preço. Para fins de aprendizado, é recomendável usar 1 nó de dados. Para obter mais informações sobre os preços do HDInsight, confira [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE] O limite de tamanho do cluster varia segundo as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.

- **Versão do HDInsight**

	É usada para determinar a versão do HDInsight a ser usada para este cluster. Para obter mais informações, confira [Versões e componentes do cluster Hadoop no HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)


- **Local (Região)**

	O cluster HDInsight e sua conta de armazenamento padrão devem estar localizados no mesmo local do Azure.
	
	![Regiões do Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Para ver uma lista das regiões com suporte, clique na lista suspensa **Região** em [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Tamanho do nó**

	![tamanhos de nó de vm do hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Selecione o tamanho da VM para os nós. Para obter mais informações, consulte [Tamanhos para Serviços de Nuvem](cloud-services-sizes-specs.md). Você pode selecionar o tamanho dos recursos de computação usados pelo cluster. Por exemplo, se você souber que executará operações que precisam de muita memória, convém selecionar um recurso de computação com mais memória.

	>[AZURE.NOTE] Os nós usados pelo cluster não contam como Máquinas Virtuais, pois as imagens das Máquinas Virtuais usadas para os nós são um detalhe de implementação do serviço HDInsight. No entanto, os núcleos de computação usados pelos nós contam para o número total de núcleos de computação disponíveis para sua assinatura. Você pode ver o número de núcleos que será usado pelo cluster, bem como o número de núcleos disponíveis, na seção Resumo da folha Tipos de Preço, ao criar um cluster HDInsight.

	Diferentes tipos de cluster têm diferentes tipos de nó, números de nós e tamanhos de nós. Por exemplo, um tipo de cluster Hadoop tem dois _nós principais_ e um padrão de quatro _nós de dados_, enquanto um tipo de cluster Storm tem dois _nós nimbus_, três _nós zookeeper_ e um padrão de quatro _nós supervisores_.

	> [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.

	Ao usar o portal de visualização do Azure para configurar o cluster, o tamanho do nó é exposto por meio da folha __Camada de preços do nó__ e também exibirá o custo associado aos diferentes tamanhos de nó.

	> [AZURE.IMPORTANT] A cobrança é iniciada quando um cluster é criado e só para quando o cluster é excluído. Para obter mais informações sobre preços, consulte [Detalhes de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


- **Usuários do HDInsight**

	Os clusters do HDInsight permitem que você configure duas contas de usuário durante o provisionamento:

	- Usuário HTTP. O nome de usuário padrão é admin, com a configuração básica do Portal do Azure.
	- Usuário RDP (clusters do Windows): é usado para conectar-se ao cluster usando o RDP. Quando cria a conta, você deve definir uma data de validade que esteja dentro de 90 dias a partir do dia atual.
	- Usuário SSH (clusters do Linux): é usado para conectar-se ao cluster usando SSH. Você pode criar contas de usuário SSH adicionais depois que o cluster for criado ao seguir as etapas em [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, do Unix ou do OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Conta de armazenamento do Azure**

	O HDFS original usa um dos muitos discos locais no cluster. O HDInsight usa o armazenamento de Blobs do Azure em vez do armazenamento de dados. O armazenamento de blobs do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. Através de uma interface HDFS (Sistema de Arquivos Distribuído Hadoop), o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados não estruturados do armazenamento de Blobs. O armazenamento de dados no armazenamento de blobs permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

	Durante a configuração, você deve especificar uma conta de armazenamento do Azure e um contêiner de armazenamento de Blobs do Azure na conta de armazenamento do Azure. Alguns processos de criação requerem que a conta de armazenamento do Azure e o contêiner de armazenamento de Blobs sejam criados anteriormente. O contêiner de armazenamento de Blobs é usado como o local de armazenamento padrão pelo cluster. Ou você pode especificar contas de armazenamento do Azure adicionais (armazenamento vinculado) que poderão ser acessadas pelo cluster. Além disso, o cluster também pode acessar qualquer contêiner de Blob configurado com acesso de leitura público completo ou acesso de leitura para blobs somente. Para obter mais informações sobre o acesso restrito, confira [Gerenciar o acesso aos recursos do Armazenamento do Azure](storage-manage-access-to-resources.md).

	![Armazenamento do HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Um contêiner de armazenamento de Blobs oferece o agrupamento de um conjunto de blobs, conforme mostrado na imagem:

	![Armazenamento do blobs do Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING] Não compartilhe um contêiner de armazenamento de Blobs entre múltiplos clusters. Não há suporte para isso.

	Para obter mais informações sobre o uso de repositórios de blobs secundários, consulte [Usando o Armazenamento de Blobs do Azure com o HDInsight](hdinsight-use-blob-storage.md).

- **Metastore do Hive/Oozie**

	O metastore contém metadados de Hive e Oozie, como tabelas Hive, partições, esquemas e colunas. Usar o metastore ajuda a manter seus metadados de Hive e Oozie, para que você não precise recriar tabelas Hive ou trabalhos do Oozie ao criar um novo cluster. Por padrão, o Hive usa um banco de dados SQL do Azure integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído. Por exemplo, você tem um cluster criado com um metastore do Hive. Você criou algumas tabelas do Hive. Depois de excluir o cluster e recriá-lo usando o mesmo metastore do Hive, você poderá ver as tabelas do Hive criadas no cluster original.
    
    > [AZURE.NOTE] A configuração de Metastore não está disponível para tipos de cluster HBase.

## Personalizar clusters usando a personalização de clusters do HDInsight (inicialização)

Às vezes, você deseja configurar os arquivos de configuração:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Os clusters não podem reter as alterações devido à recriação das imagens. Para obter mais informações, consulte [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para manter as alterações durante o tempo de vida dos clusters, você pode usar a personalização de clusters do HDInsight durante o processo de criação. Essa é a maneira recomendável de mudar as configurações de um cluster e persisti-las entre esses eventos de reinicialização do refazimento de imagem do Azure. Essas mudanças de configuração são aplicadas antes do início de um serviço para que os serviços não precisem ser reiniciados.

Para obter mais um exemplo, confira [Personalizar clusters HDInsight usando a inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).

## Personalizar clusters usando a ação de Script

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante a criação. Esses scripts são invocados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no Portal, os cmdlets do Windows PowerShell do HDInsight ou o SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).


## Usar redes virtuais do Azure

A [Rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.

	![diagrama da configuração ponto a site](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obter informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, confira [Estender os recursos do HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

## Métodos de criação de cluster

Neste artigo, você aprendeu as informações básicas sobre como criar um cluster HDInsight baseado em Windows. Use a tabela abaixo para obter informações específicas sobre como criar um cluster usando um método que melhor atenda às suas necessidades:

| Use esta opção para criar um cluster... | Usando um navegador da Web... | Usando uma linha de comando | Usando a API REST | Usando o SDK | No Linux, Mac OS X ou Unix | No Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portal do Azure](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [CLI do Azure](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [PowerShell do Azure](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [SDK .NET](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Modelos de ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0128_2016-->