<properties
   	pageTitle="Criar clusters Hadoop, HBase ou Storm ou Spark no Linux no HDInsight | Microsoft Azure"
   	description="Aprenda a criar clusters Hadoop, HBase ou Storm ou Spark no Linux para o HDInsight usando um navegador, a CLI do Azure, o Azure PowerShell, REST ou por meio de um SDK."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="01/28/2016"
   	ms.author="nitinme"/>


#Criar clusters Hadoop baseados em Linux em HDInsight

[AZURE.INCLUDE [seletor](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste documento, você aprenderá sobre as diferentes maneiras de criar um cluster HDInsight baseado em Linux no Azure, bem como configurações opcionais que podem ser usadas com o cluster. O HDInsight fornece Apache Hadoop, Apache Storm e Apache HBase como serviços na plataforma de nuvem do Azure.

> [AZURE.NOTE] Este documento fornece instruções sobre as diferentes maneiras de criar um cluster. Se você procura uma abordagem de início rápido para criar um cluster, consulte [Introdução ao Azure HDInsight no Linux](../hdinsight-hadoop-linux-get-started.md).

## O que é um cluster HDInsight?

Um cluster Hadoop consiste em várias máquinas virtuais (nós) que são usadas para processamento distribuído de tarefas no cluster. O Azure abstrai os detalhes da implementação da instalação e configuração de nós individuais, portanto você precisa fornecer informações de configuração geral.

###Tipos de cluster

Há vários tipos de HDInsight disponíveis:

| Tipo de cluster | Use se quiser... |
| ------------ | ----------------------------- |
| O Hadoop | consulta e análise (trabalhos em lotes) |
| HBase | Armazenamento de dados NoSQL |
| Storm | Processamento de eventos em tempo real |
| Spark (visualização) | Processamento na memória, consultas interativas, processamento de transmissão de microlotes |

Durante a configuração, você selecionará um desses tipos para o cluster. Você pode adicionar outras tecnologias, como Hue ou R a esses tipos básicos usando [Ações de Script](#scriptaction).

Cada tipo de cluster tem sua própria terminologia para nós no cluster, bem como o número de nós e o tamanho da VM padrão para cada tipo de nó:

> [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.

![Nós de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

Os clusters Hadoop para HDInsight têm dois tipos de nós :

- Nó principal (2 nós)
- Nó de dados (pelo menos 1 nó)

![Nós de cluster HBase do HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

Clusters do HBase para HDInsight têm três tipos de nó: - Servidores principais (2 nós) - Servidores de região (pelo menos 1 nó) - Nós mestre/zookeeper (3 nós)

![Nós de cluster Storm do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

Clusters do Storm para HDInsight são implantados com três funções: - Nós Nimbus (2 nós) - Servidores de supervisão (pelo menos 1 nó) - Nós zookeeper (3 nós)


![Nós de cluster Spark do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

Clusters do Spark para HDInsight têm três tipos de nós: - Nó principal (2 nós) - Nó de trabalho (pelo menos 1 nó) - Nós zookeeper (3 nós) (gratuito para tamanho de VM Zookeepers A1)

###Armazenamento do Azure para HDInsight

Cada tipo de cluster também terá uma ou mais contas do Armazenamento do Azure associadas ao cluster. O HDInsight usa os blobs do Azure dessas contas de armazenamento como o repositório de dados do seu cluster. Manter os dados separados do cluster permite excluir clusters quando não estiverem em uso e preservar seus dados. Em seguida, você pode usar a mesma conta de armazenamento em um novo cluster se precisar fazer mais análises. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md).

## <a id="configuration"></a>Opções de configuração básicas

As seções a seguir descrevem as opções de configuração necessárias disponíveis ao criar um cluster HDInsight.

###Nome do cluster

O nome do cluster fornece um identificador exclusivo para o cluster e é usado como parte do nome do domínio para acessar o cluster pela Internet. Por exemplo, um cluster denominado _mycluster_ estará disponível na Internet como _mycluster_.azurehdinsight.net.

O nome do cluster deve seguir estas diretrizes:

- O campo deve ser uma cadeia com 3 a 63 caracteres.
- O campo pode conter apenas letras, números e hifens.

###Tipo de cluster

O tipo de cluster permite selecionar configurações de finalidade especiais no cluster. A seguir estão os tipos disponíveis para o HDInsight baseado em Linux:

| Tipo de cluster | Use se quiser... |
| ------------ | ----------------------------- |
| O Hadoop | consulta e análise (trabalhos em lotes) |
| HBase | Armazenamento de dados NoSQL |
| Storm | Processamento de eventos em tempo real |
| Spark (visualização) | Processamento na memória, consultas interativas, processamento de transmissão de microlotes |

Você pode adicionar outras tecnologias, como Hue ou R a esses tipos básicos usando [Ações de Script](#scriptaction).

###Sistema operacional de cluster

Você pode provisionar clusters HDInsight em um dos dois sistemas operacionais a seguir:

- **HDInsight no Windows (Windows Server 2012 R2 Datacenter)**: selecione esta opção se precisar integrar com serviços baseados em Windows e tecnologias que serão executadas no cluster com Hadoop, ou se você estiver migrando de uma distribuição Hadoop baseada em Windows existente.

- **HDInsight em Linux (Ubuntu 12.04 LTS para Linux)**: selecione essa opção se estiver familiarizado com Linux ou Unix, migrando de uma solução Hadoop existente baseada no Linux ou se quiser fácil integração com componentes do ecossistema do Hadoop criados para Linux. Para obter mais informações, consulte [Introdução ao Hadoop no Linux em HDInsight](hdinsight-hadoop-linux-get-started.md).

> [AZURE.NOTE] As informações neste documento pressupõem que você está usando um cluster HDInsight baseado em Linux. Para obter informações específicas de clusters baseados no Windows, consulte [Criar clusters Hadoop baseados no Windows no HDInsight](hdinsight-provision-clusters.md).

###Nome da assinatura

Se você tiver várias assinaturas do Azure, use isso para selecionar a que deseja usar ao criar o cluster HDInsight.

###Grupo de recursos

Aplicativos normalmente são compostos por vários componentes, como, por exemplo, um aplicativo Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. O Gerenciador de Recursos do Azure (ARM) permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo. Para obter mais informações, veja [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md).

###Credenciais

Há dois tipos de autenticação usados com clusters HDInsight:

* Usuário __Admin__ ou __HTTPs__: a conta de administrador de um cluster é usada principalmente ao acessar a Web ou serviços REST expostos pelo cluster. Ela não pode ser usada para fazer logon diretamente no cluster.

* __Nome de usuário SSH__: uma conta de usuário SSH é usada para acessar remotamente o cluster usando um cliente [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell). Isso geralmente é usado para fornecer uma linha de comando de acesso remoto aos nós de cluster principais.

A conta de administrador é protegida por senha, e todas as comunicações Web ao cluster usando a conta de administrador devem ser executadas por uma conexão HTTPS segura.

O usuário SSH pode ser autenticado usando uma senha ou um certificado. A autenticação de certificado é a opção mais segura, mas requer a criação e manutenção de um par de certificados públicos e privados.

Para obter mais informações sobre como usar SSH com o HDInsight, incluindo como criar e usar chaves SSH, consulte um dos seguintes artigos:

* [Para clientes Linux, Unix ou Mac OS X - usando SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Para clientes Windows - usando SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

###Fonte de dados

O HDInsight usa o Armazenamento de Blobs do Azure como o armazenamento subjacente do cluster. Internamente, o Hadoop e outros softwares no cluster veem esse armazenamento como um sistema compatível com Hadoop Distributed File System (HDFS).

Ao criar um novo cluster, você deve criar uma nova Conta de Armazenamento do Azure ou usar uma já existente.

> [AZURE.IMPORTANT] A localização geográfica selecionada para a conta de armazenamento determinará o local do cluster HDInsight, como o cluster deve estar no mesmo data center da conta de armazenamento padrão.
>
> Para ver uma lista das regiões com suporte, clique na lista suspensa **Região** em [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

####Contêiner de armazenamento padrão

O HDInsight também criará um _contêiner de armazenamento padrão_ na conta de armazenamento. Isso é o armazenamento padrão para o cluster HDInsight.

Por padrão, esse contêiner tem o mesmo nome do cluster. Para saber mais sobre como o HDInsight funciona com o armazenamento de Blob do Azure, consulte [Usar armazenamento de Blob do Azure compatível com HDFS com o Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md).

>[AZURE.WARNING] Não compartilhe um contêiner entre vários clusters. Não há suporte para isso.

###Tamanho do nó

Você pode selecionar o tamanho dos recursos de computação usados pelo cluster. Por exemplo, se você souber que executará operações que precisam de muita memória, convém selecionar um recurso de computação com mais memória.

>[AZURE.NOTE] Os nós usados pelo cluster não contam como Máquinas Virtuais, pois as imagens das Máquinas Virtuais usadas para os nós são um detalhe de implementação do serviço HDInsight. No entanto, os núcleos de computação usados pelos nós contam para o número total de núcleos de computação disponíveis para sua assinatura. Você pode ver o número de núcleos que será usado pelo cluster, bem como o número de núcleos disponíveis, na seção Resumo da folha Tipos de Preço, ao criar um cluster HDInsight.

Diferentes tipos de cluster têm diferentes tipos de nó, números de nós e tamanhos de nós. Por exemplo, um tipo de cluster Hadoop tem dois _nós principais_ e um padrão de quatro _nós de dados_, enquanto um tipo de cluster Storm tem dois _nós nimbus_, três _nós zookeeper_ e um padrão de quatro _nós supervisores_.

> [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, será preciso escolher um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.

Ao usar o portal de visualização do Azure para configurar o cluster, o tamanho do nó é exposto por meio da folha __Camada de preços do nó__ e também exibirá o custo associado aos diferentes tamanhos de nó.

> [AZURE.IMPORTANT] A cobrança é iniciada quando um cluster é criado e só para quando o cluster é excluído. Para obter mais informações sobre preços, consulte [Detalhes de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a id="optionalconfiguration"></a>Configuração opcional

As seções a seguir descrevem opções de configuração opcionais, bem como cenários que exigem essas configurações.

### Versão do HDInsight

Use para determinar a versão do HDInsight a ser usada com esse cluster. Para obter mais informações, confira [Versões e componentes do cluster Hadoop no HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

### Usar redes virtuais do Azure

Uma [Rede virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite criar uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

    | Configuração site a site | Configuração ponto a site |
    | -------------------------- | --------------------------- |
    | A configuração site a site permite conectar vários recursos de seu datacenter à rede virtual do Azure usando uma VPN de hardware ou o Serviço de roteamento e acesso remoto.<br />![diagrama da configuração site a site](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | A configuração ponto a site permite que você conecte um recurso específico à rede virtual do Azure usando uma VPN do software.<br />![diagrama da configuração ponto a site](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Para obter mais informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, confira [Estender os recursos do HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

### Metastore

O metastore contém metadados Hive e Oozie, como informações sobre tabelas Hive, partições, esquemas e colunas. Usar o metastore ajuda a manter seus metadados Hive e Oozie para que você não tenha que recriar tabelas Hive ou trabalhos do Oozie ao criar um novo cluster.

Usar a opção de configuração Metastore permite especificar um metastore externo usando o Banco de Dados SQL. Isso permite que as informações de metadados sejam preservadas quando você excluir um cluster, já que elas são armazenadas externamente no banco de dados. Para obter instruções sobre como criar um banco de dados SQL do Azure, consulte [Criar seu primeiro banco de dados SQL do Azure](sql-database-get-started.md).

> [AZURE.NOTE] A configuração de Metastore não está disponível para tipos de cluster HBase.

###<a id="scriptaction"></a>Ação de script

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante o provisionamento de cluster. Esses scripts são invocados por meio da **Ação de Script**. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

> [AZURE.IMPORTANT] Não há suporte para a adição de componentes após a criação de um cluster, pois esses componentes não estarão disponíveis após a recriação de um nó de cluster. Os componentes instalados por meio de ações de script são reinstalados como parte do processo de recriação de imagens.

### Armazenamento adicional

Em alguns casos, você poderá adicionar mais armazenamento ao cluster. Por exemplo, se você tiver várias Contas do Armazenamento do Azure para diferentes regiões geográficas ou para diferentes serviços, mas desejar analisá-los com o HDInsight.

Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md).

##<a id="nextsteps"></a><a id="options"></a> Métodos de criação

Neste artigo, você aprendeu as informações básicas sobre como criar um cluster HDInsight baseado em Linux. Use a tabela abaixo para obter informações específicas sobre como criar um cluster usando um método que melhor atenda às suas necessidades:

| Use esta opção para criar um cluster... | Usando um navegador da Web... | Usando uma linha de comando | Usando a API REST | Usando o SDK | No Linux, Mac OS X ou Unix | No Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portal de visualização do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [PowerShell do Azure](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Listar e mostrar clusters"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use o Sqoop com o HDInsight"

<!---HONumber=AcomDC_0128_2016-->