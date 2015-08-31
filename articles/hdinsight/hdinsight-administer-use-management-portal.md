<properties
	pageTitle="Gerenciar clusters Hadoop no HDInsight usando o portal de visualização do Azure | Microsoft Azure"
	description="Saiba como administrar o serviço do HDInsight. Crie um cluster HDInsight, abra o console interativo do JavaScript e abra o console de comando Hadoop."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight usando o portal de visualização do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-portal-management-selector.md)]

Usando o [portal de visualização do Azure][azure-portal], você pode provisionar os clusters Hadoop no HDInsight do Azure, alterar a senha de usuário do Hadoop e habilitar o RDP (Protocolo de Área de Trabalho Remota) para que possa acessar o console de comando do Hadoop no cluster.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Gerenciar clusters Hadoop no HDInsight Usando o portal do Azure](hdinsight-administer-use-management-portal-v1.md)

> [AZURE.NOTE]As etapas deste documento são específicas para trabalhar com clusters Hadoop baseados no Windows. Para obter informações sobre como trabalhar com clusters baseados em Linux, confira [Gerenciar clusters Hadoop no HDInsight usando o portal de visualização do Azure](hdinsight-administer-use-portal-linux.md)



> [AZURE.NOTE]Além do portal de visualização do Azure, também há outras ferramentas disponíveis para a administração do HDInsight.
>
> - [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
> - [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)

**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Conta de armazenamento do Azure** - um cluster do HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Para obter mais informações sobre como o Armazenamento de Blob do Azure fornece uma experiência perfeita com os clusters HDInsight, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento](../storage-create-storage-account.md).


##Provisionar clusters do HDInsight

Para obter as instruções de provisionamento usando o portal de visualização, confira [Provisionar clusters HDInsight](hdinsight-provision-clusters.md#portal).

##Personalizar a provisão de clusters HDInsight

O HDInsight trabalha com uma ampla variedade de componentes do Hadoop. Para obter a lista dos componentes que foram verificados e suportados, consulte [Qual versão do Hadoop está no HDInsight do Azure](hdinsight-component-versioning.md) Você pode personalizar o HDInsight usando uma das seguintes opções:

- Use a ação de Script para executar scripts personalizados que possam personalizar um cluster para alterar a configuração de cluster ou instalar componentes personalizados como Giraph ou Solr. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).
- Use os parâmetros de personalização do cluster no SDK do .NET do HDInsight ou no PowerShell do Azure durante o provisionamento do cluster. Essas alterações de configuração são preservadas durante o tempo de vida do cluster e não são afetadas pelas novas imagens do nó do cluster que a plataforma do Azure refaz periodicamente para manutenção. Para obter mais informações sobre como usar os parâmetros de personalização do cluster, consulte [Provisionamento de clusters HDInsight](hdinsight-provision-clusters.md).
- Alguns componentes nativos do Java, como Mahout e Cascading, podem ser executados no cluster como arquivos JAR. Esses arquivos JAR podem ser distribuídos para o armazenamento de Blob do Azure e enviados aos clusters HDInsight por meio de mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE]Se você tiver problemas para implantar arquivos JAR nos clusters do HDInsight ou ao chamar arquivos JAR nesses clusters, entre em contato com o [Suporte da Microsoft](http://azure.microsoft.com/support/options/).

	> O Cascading não tem suporte do HDInsight e não é qualificado para o Suporte da Microsoft. Para obter as listas dos componentes suportados, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?](hdinsight-component-versioning.md)


Não há suporte para a instalação de software personalizado no cluster usando uma conexão de área de trabalho remota. Evite armazenar arquivos nas unidades de nó de cabeçalho, pois eles serão perdidos se você precisar recriar os clusters. É recomendável armazenar os arquivos no armazenamento de Blob do Azure. O armazenamento de Blob é persistente.

## Familiarizar-se com a interface do portal de cluster

**Para acessar o cluster**

1. Entre no [portal de visualização][azure-portal].
2. Clique em **Procurar Tudo** no menu à esquerda para abrir a folha **Procurar**.
3. Clique em **Clusters HDInsight** para abrir a folha **Clusters HDInsight**.
4. Clique em um dos clusters na lista para abrir a folha do cluster:

	![Portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.blade.png)

5. Clique em **Configurações** para ver os detalhes de configuração e para configurar o cluster:

	![Configurações de cluster do portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	|Configuração|Descrição|
	|-----|-----|
	|Propriedades| Exiba as propriedades do cluster.|
	|Chaves de Armazenamento do Azure| Exiba as informações padrão da conta de armazenamento do Azure. |
	|Logon no Cluster| Conceda/revogue o acesso a serviços Web HTTP e configure as informações de logon do cluster. |
	|Metastores Externas| Exiba as informações do metastore Hive/Oozie.|
	|Dimensionar o cluster| Aumente/reduza o número de nós de trabalhadores do cluster.|
	|Área de Trabalho Remota| Habilite/desabilite a conectividade da Área de Trabalho Remota, conecte-se ao cluster por meio da Área de Trabalho Remota.|


##Conceder/revogar acesso aos serviços HTTP

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso no portal do Azure.

>[AZURE.NOTE]Ao conceder/revogar o acesso, você redefinirá o nome de usuário de cluster e a senha.

**Para conceder/revogar acesso aos serviços Web HTTP**

1. Entre no [portal de visualização][azure-portal].
2. Clique em **Procurar Tudo** no menu à esquerda, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Logon do Cluster**.
5. Clique em **ATIVADO** ou em **DESATIVADO** em **Acesso de Logon ao Cluster**.  
6. Para **Nome de Usuário de Logon no Cluster** e **Senha de Logon no Cluster**, insira o novo nome de usuário e a senha (respectivamente) para o cluster.
7. Clique em **SALVAR**.

	![hdinsight geral remover acesso http serviço web](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)


Isso também pode ser feito por meio de cmdlets do PowerShell do Azure:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Consulte [Administrar clusters HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md).

##Alterar o nome de usuário e a senha do cluster HDInsight
Um cluster HDInsight pode ter duas contas de usuário. A conta de usuário do cluster HDInsight é criada durante o processo de provisionamento. Você também pode criar uma conta de usuário RDP para acessar o cluster via RDP. Consulte [Habilitar área de trabalho remota](#connect-to-hdinsight-clusters-by-using-rdp).

**Para alterar o nome de usuário e a senha do cluster HDInsight**

1. Entre no [portal de visualização][azure-portal].
2. Clique em **Procurar Tudo** no menu à esquerda, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Logon do Cluster**.
4. Altere o **Nome de Logon do Cluster** e/ou a **Senha de Logon do Cluster** e clique em **Salvar**.

	![hdinsight alterar nome de usuário senha usuário cluster usuário http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##Dimensionar clusters
O recurso de dimensionamento de clusters permite que você altere o número de nós de trabalhador usados por um cluster em execução no Azure HDInsight sem precisar recriar o cluster.

>[AZURE.NOTE]Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades. Confira [Familiarizar-se com a interface do portal de cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

O impacto da alteração do número de nós de dados em cada tipo de cluster com suporte do HDInsight:

- O Hadoop

	Você pode aumentar continuamente o número de nós de trabalhador em um cluster Hadoop em execução sem afetar os trabalhos pendentes ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.

	Quando um cluster Hadoop é reduzido verticalmente pela diminuição do número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.

- HBase

	Você pode adicionar ou remover diretamente nós do cluster HBase enquanto ele é executado. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode equilibrar manualmente os servidores regionais fazendo logon no nó de cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Para obter mais informações sobre como usar o shell do HBase, confira
- Storm

	Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Mas, após a conclusão bem-sucedida da operação de dimensionamento, você precisará redistribuir a topologia.

	A redistribuição pode ser feita de duas maneiras:

	* Interface da Web Storm
	* Ferramenta CLI (interface de linha de comando)

	Consulte a [documentação do Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

	A IU da Web do Storm está disponível no cluster HDInsight:

	![hdinsight storm dimensionar novo balanceamento](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia do Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Para dimensionar clusters**

1. Entre no [portal de visualização][azure-portal].
2. Clique em **Procurar Tudo** no menu à esquerda, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Dimensionar o Cluster**.
4. Insira **Número de nós de Trabalhador**. O limite no número de nós do cluster varia entre as assinaturas do Azure. Contate o suporte de cobrança para aumentar o limite. As informações de custo refletirão as alterações feitas no número de nós.


	![hdinsight hadoop hbase storm spark dimensionar](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)



##Conectar ao clusters HDInsight usando o RDP

As credenciais do cluster que você forneceu em sua criação concedem acesso aos serviços do cluster, mas não ao próprio cluster através da área de trabalho remota. Você pode ativar o acesso de Área de Trabalho Remota ao provisionar um cluster ou depois que um cluster for provisionado. Para obter instruções sobre como habilitar a Área de Trabalho Remota na provisão, confira [Provisionar cluster HDInsight](hdinsight-provision-clusters.md).

**Para habilitar a área de trabalho remota**

1. Entre no [portal de visualização][azure-portal].
2. Clique em **Procurar Tudo** no menu à esquerda, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Área de Trabalho Remota**.
4. Insira **Expira em**, **Nome de Usuário de Área de Trabalho Remota** e **Senha de Área de Trabalho Remota** e clique em **Habilitar**.

	![hdinsight habilitar desabilitar configurar área de trabalho remota](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	O valor padrão para Expira em é uma semana.
> [AZURE.NOTE]Você também pode usar o SDK do .NET HDInsight para habilitar a área de trabalho remota em um cluster. Use o **EnableRdp** método no objeto de cliente HDInsight da seguinte maneira: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Da mesma forma, para desabilitar a área de trabalho remota no cluster, você pode usar **client.DisableRdp(clustername, location)**. Para obter mais informações sobre esses métodos, consulte [Referência do SDK do .NET HDInsight](http://go.microsoft.com/fwlink/?LinkId=529017). Isso é aplicável apenas a clusters do HDInsight em execução no Windows.

**Para conectar-se ao cluster usando o RDP**

1. Entre no [portal de visualização][azure-portal].
2. Clique em **Procurar Tudo** no menu à esquerda, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Área de Trabalho Remota**.
4. Clique em **Conectar** e siga as instruções. Se Conectar estiver desabilitado, você deverá habilitá-lo primeiro. Use o nome do usuário e a senha da Área de Trabalho Remota. Não é possível usar as credenciais de usuário do Cluster.

##Abrir a linha de comando do Hadoop

Para conectar-se ao cluster usando a área de trabalho remota e usar a linha de comando do Hadoop, você deve primeiro habilitar o acesso à área de trabalho remota para o cluster conforme descrito na seção anterior.

**Para abrir a linha de comando do Hadoop**

1. Conecte-se ao cluster usando a Área de Trabalho Remota.
8. Na área de trabalho, clique duas vezes em **Linha de Comando do Hadoop**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Para obter mais informações sobre os comandos Hadoop, consulte [Referência de comandos Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Na captura de tela anterior, o nome da pasta tem o número de versão do Hadoop incorporado. O número da versão pode ser alterado com base na versão dos componentes do Hadoop instalados no cluster. Você pode usar variáveis de ambiente do Hadoop para referir-se a essas pastas. Por exemplo:

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%


##Próximas etapas
Neste artigo, você aprendeu a criar um cluster HDInsight usando o portal do de visualização e a abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Provisionar clusters HDInsight](hdinsight-provision-clusters.md)
* [Enviar trabalhos Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](../hdinsight-get-started.md)
* [Qual versão do Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Página inicial do cluster"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Linha de comando do Hadoop"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!---HONumber=August15_HO8-->