<properties
	pageTitle="Gerenciar clusters Hadoop baseados em Linux no HDInsight usando o portal do Azure | Microsoft Azure"
	description="Aprenda a criar e gerenciar cluster HDInsight baseados em Linux usando o portal do Azure."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="jgao"/>

#Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-portal-management-selector.md)]

Com o [Portal do Azure][azure-portal], você pode gerenciar clusters baseados em Linux no Azure HDInsight. Use o seletor de guias para saber mais sobre como criar clusters Hadoop no HDInsight usando outras ferramentas.

**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##Abra o Portal

1. Entre em [https://portal.azure.com](https://portal.azure.com).
2. Depois de abrir o portal, você poderá:

	- Clique em **Novo** no menu esquerdo para criar um novo cluster:
	
		![novo botão do cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
	- Clique em **Clusters HDInsight** no menu à esquerda para listar os clusters existentes
	
		![Botão do cluster HDInsight do portal do Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

        Se **HDInsight** não aparecer no menu esquerdo, clique em **Procurar**.

        ![Botão Procurar cluster do portal do Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-browse-button.png)

##Criar clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

O HDInsight trabalha com uma ampla variedade de componentes do Hadoop. Para obter a lista dos componentes que foram verificados e suportados, consulte [Qual versão do Hadoop está no HDInsight do Azure](hdinsight-component-versioning.md) Para obter informações gerais sobre a criação do cluster, veja [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

##Listar e mostrar clusters

1. Entre em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu à esquerda para listar os clusters existentes.
3. Clique no nome do cluster. Se a lista de clusters for longa, você poderá usar o filtro na parte superior da página.
4. Clique duas vezes em um cluster da lista para exibir os detalhes.

	**Menu e fundamentos**:

	![fundamentos do cluster hdinsight do portal do Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
	
	- **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, o que permite acessar informações de configuração detalhadas do cluster.
	- **Painel**, **Painel do Cluster** e **URL: são maneiras de acessar o painel do cluster, que é o Ambari Web para clusters baseados em Linux.
- **Secure Shell**: mostra as instruções para se conectar ao cluster usando uma conexão Secure Shell (SSH).
	- **Dimensionar o Cluster**: permite alterar o número de nós de trabalho para o cluster.
	- **Excluir**: exclui o cluster.
	- **Início Rápido (![ícone de nuvem e raio = início rápido](./media/hdinsight-administer-use-portal-linux/quickstart.png))**: exibe informações que ajudarão você a começar a usar o HDInsight.
	- **Usuários (![ícone de usuários](./media/hdinsight-administer-use-portal-linux/users.png))**: permite definir permissões para o _gerenciamento do portal_ deste cluster para outros usuários em sua assinatura do Azure.
	
		> [AZURE.IMPORTANT] Isso afeta _apenas_ o acesso e as permissões para esse cluster no portal do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.
	- **Marcas (![ícone de marca](./media/hdinsight-administer-use-portal-linux/tags.png))**: as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.
	- **Ambari Views**: links para o Ambari Web.
	
	> [AZURE.IMPORTANT] Para gerenciar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para saber mais sobre como usar o Ambari, consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md).

	**Uso**:
	
	![Uso do cluster hdinsight do portal do Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-cluster-usage.png)
	
5. Clique em **Configurações**.

	![Uso do cluster hdinsight do portal do Azure](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.cluster.settings.png)

	- **Logs de auditoria**:
    - **Início Rápido**: exibe informações que ajudarão você a começar a usar o HDInsight.
	- **Logon do Cluster**: altere o nome de usuário e a senha do HTTP de cluster.
	- **Dimensionar o Cluster**: aumente e diminua o número de nós de trabalho do cluster.
    - **Secure Shell**: mostra as instruções para se conectar ao cluster usando uma conexão Secure Shell (SSH).
    - **Parceiro HDInsight**: adicione/remova o Parceiro HDInsight atual.
	- **Metastores Externos**: exiba os Hive e Oozie metastores. Os metastores só podem ser configurados durante o processo de criação do cluster.
    - **Ações de Script**: execute scripts Bash no cluster.
    - **Propriedades**: exiba as propriedades do cluster.
	- **Chaves de Armazenamento do Azure**: exiba a conta de armazenamento padrão e a chave dela. A conta de armazenamento é configurada durante o processo de criação do cluster.
	- **Identidade do AAD do Cluster**: 
	- **Usuários**: permite definir permissões para o _gerenciamento do portal_ deste cluster para outros usuários em sua assinatura do Azure.
	- **Marcas**: as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.
    
    > [AZURE.NOTE] Esta é uma lista genérica de configurações disponíveis; nem todas elas estarão presentes para todos os tipos de cluster.

6. Clique em **Propriedades**:

	As propriedades são:
	
	- **Nome do host**: nome do cluster.
	- **URL do Cluster**.
	- **Status**: incluindo Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
	- **Região**: local do Azure. Para ter acesso à lista de locais do Azure com suporte, consulte a caixa de listagem suspensa **Região** em [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
	- **Dados criados**.
	- **Sistema operacional**: **Windows** ou **Linux**.
	- **Tipo**: Hadoop, HBase, Storm, Spark. 
	- **Versão**. Consulte [versões do HDInsight](hdinsight-component-versioning.md)
	- **Assinatura**: nome da assinatura.
	- **ID da assinatura**.
    - **Fonte de dados padrão**: o sistema de arquivos padrão do cluster.
	- **Faixa de preço dos nós de trabalho**.
	- **Faixa de preço do nó de cabeça**.

##Excluir clusters

Excluir um cluster não excluirá a conta de armazenamento padrão, nem nenhuma conta de armazenamento vinculada. Você pode recriar o cluster usando as mesmas contas de armazenamento e as mesmas metastores. É recomendável usar um novo contêiner de Blobs padrão ao recriar o cluster.

1. Entre no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu esquerdo, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Excluir** no menu superior e siga as instruções.

Consulte também [Pausar/desligar clusters](#pauseshut-down-clusters).

##Dimensionar clusters
O recurso de dimensionamento de clusters permite que você altere o número de nós de trabalhador usados por um cluster em execução no Azure HDInsight sem precisar recriar o cluster.

>[AZURE.NOTE] Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades. Consulte [Listar e mostrar clusters](hdinsight-adminster-use-management-portal/#list-and-show-clusters).

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

	![hdinsight storm dimensionar novo balanceamento](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia do Storm:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Para dimensionar clusters**

1. Entre no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu esquerdo, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Dimensionar o Cluster**.
4. Insira o **Número de Nós de Trabalho**. O limite no número de nós do cluster varia entre as assinaturas do Azure. Contate o suporte de cobrança para aumentar o limite. As informações de custo refletirão as alterações feitas no número de nós.

	![hdinsight hadoop hbase storm spark dimensionar](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

##Pausar/desligar clusters

A maioria dos trabalhos do Hadoop é composta por trabalhos em lotes que só são executados ocasionalmente. Para a maioria dos clusters do Hadoop, há grandes períodos de tempo em que o cluster não está sendo usado para processamento. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Há várias maneiras de programar o processo:

- Use o Azure Data Factory. Veja [Criar clusters Hadoop baseados em Linux sob demanda usando o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para criar serviços vinculados do HDInsight sob demanda.
- Use o Azure PowerShell. Consulte [Analisar dados de atraso de voo](hdinsight-analyze-flight-delay-data.md).
- Use a CLI do Azure. Consulte [Gerenciar clusters HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
- Use o SDK .NET do HDInsight. Consulte [Enviar trabalhos do Hadoop](hdinsight-submit-hadoop-jobs-programmatically.md).

Para saber mais sobre preços, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para excluir um cluster do Portal, veja [Excluir clusters](#delete-clusters)

##Alterar nome de usuário do cluster

Um cluster HDInsight pode ter duas contas de usuário. A conta de usuário do cluster HDInsight (também conhecido como conta de usuário HTTP) e a conta de usuário SSH são criadas durante o processo de criação. Você pode usar a interface do usuário do Ambari Web para alterar o nome de usuário e a senha da conta de usuário do cluster:

**Para alterar a senha de usuário do cluster HDInsight**

1. Entre na interface do usuário do Ambari Web usando as credenciais de usuário do cluster HDInsight. O nome de usuário padrão é **admin**. A URL é **https://<HDInsight Cluster Name>azurehdinsight.net**.
2. Clique em **Administrador** no menu superior e depois clique em “Gerenciar Ambari”. 
3. No menu à esquerda, clique em **Usuários**.
4. Clique em **Administrador**.
5. Clique em **Alterar Senha**.

##Conceder/revogar acesso

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso usando a [CLI do Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) e o [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

##Localizar a ID da assinatura

**Para localizar suas IDs da assinatura do Azure**

1. Entre no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu à esquerda e clique em **Assinaturas**. Cada assinatura tem um nome e uma ID.

Cada cluster é vinculado a uma assinatura do Azure. A ID da assinatura é mostrada no bloco **Fundamentos** do cluster. Consulte [Listar e mostrar clusters](#list-and-show-clusters).

##Encontrar o grupo de recursos 

No modo ARM, cada cluster HDInsight é criado com um grupo de recursos do Azure. O grupo de recursos do Azure ao qual um cluster pertence aparece em:

- A lista de clusters tem uma coluna **Grupo de Recursos**.
- Bloco **Fundamentos** do cluster.  

Consulte [Listar e mostrar clusters](#list-and-show-clusters).


##Encontrar a conta de armazenamento padrão

Cada cluster HDInsight tem uma conta de armazenamento padrão. A conta de armazenamento padrão e as chaves para um cluster são exibidos em **Configurações**/**Propriedades**/**Chaves de armazenamento do Azure**. Consulte [Listar e mostrar clusters](#list-and-show-clusters).


##Execute consultas Hive

Você não pode executar o trabalho do Hive diretamente do portal do Azure, mas pode usar a Exibição do Hive na interface do usuário do Ambari Web.

**Para executar consultas do Hive usando a Exibição do Hive do Ambari**

1. Entre na interface do usuário do Ambari Web usando as credenciais de usuário do cluster HDInsight. O nome de usuário padrão é **admin**. A URL é **https://<HDInsight Cluster Name>azurehdinsight.net**.
2. Abra a Exibição do Hive como mostrado nesta captura de tela:  

	![exibição do hive do hdinsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. Clique em **Consulta** no menu superior
4. Insira uma consulta do Hive no **Editor de Consultas** e clique em **Executar**.

##Monitorar trabalhos

Veja [Gerenciar clusters HDInsight usando a interface do usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md#monitoring)

##Procurar arquivos

Usando o portal do Azure, você pode procurar conteúdos do contêiner padrão.

1. Entre em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu à esquerda para listar os clusters existentes.
3. Clique no nome do cluster. Se a lista de clusters for longa, você poderá usar o filtro na parte superior da página.
4. Clique em **Configurações**.
5. Na folha **Configurações**, clique em **Chaves do Armazenamento do Azure**.
6. Clique no nome da conta de armazenamento padrão.
7. Clique no bloco **Blobs**.
8. Clique no nome de contêiner padrão.


##Monitorar o uso do cluster

A seção __Uso__ da folha do cluster HDInsight exibe informações sobre o número de núcleos disponíveis para sua assinatura para uso com o HDInsight, bem como o número de núcleos alocados para esse cluster e como eles são alocados para os nós presentes no cluster. Consulte [Listar e mostrar clusters](#list-and-show-clusters).

> [AZURE.IMPORTANT] Para monitorar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para saber mais sobre como usar o Ambari, consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

##Conectar a um cluster

veja [Usar o Hive com Hadoop no HDInsight com SSH](hdinsight-hadoop-use-hive-ssh.md#ssh).
	
##Próximas etapas
Neste artigo, você aprendeu como criar um cluster HDInsight usando o Portal e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Criar clusters HDInsight](hdinsight-provision-clusters.md)
* [Usar o Hive no HDInsight](hdinsight-use-hive.md)
* [Usar o Pig no HDInsight](hdinsight-use-pig.md)
* [Usar o Sqoop no HDInsight](hdinsight-use-sqoop.md)
* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Qual versão do Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Linha de comando do Hadoop"

<!---HONumber=AcomDC_0413_2016-->