<properties
	pageTitle="Gerenciar clusters Hadoop baseados em Linux no HDInsight usando o portal do Azure | Microsoft Azure"
	description="Aprenda a criar e gerenciar cluster HDInsight baseados em Linux usando o portal do Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="larryfr"/>

#Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-portal-management-selector.md)]


Com o [Portal do Azure][preview-portal], você pode provisionar e gerenciar clusters Hadoop baseados em Linux no Azure HDInsight.

> [AZURE.NOTE] As etapas deste documento são específicas dos clusters Hadoop baseados em Linux. Para obter informações sobre como trabalhar com clusters baseados no Windows, veja [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md)


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Outras ferramentas para administrar o HDInsight
Também há outras ferramentas disponíveis para administrar o HDInsight além do portal do Azure.

- [Administrar o HDInsight usando o a CLI do Azure](hdinsight-administer-use-command-line.md): a CLI do Azure é uma ferramenta de linha de comando entre plataformas que permite que você gerencie os serviços do Azure

- [Administrar o HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md): o Azure PowerShell fornece cmdlets do PowerShell para gerenciar serviços do Azure

## Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Criar clusters HDInsight

Você pode criar clusters HDInsight no portal do Azure. Isso é discutido detalhadamente no documento [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## Gerenciar um cluster

Se um cluster for selecionado no Portal do Azure, serão exibidas informações essenciais sobre o cluster, como o nome, o grupo de recursos, o sistema operacional e a URL do painel do cluster (usada para acessar o Ambari Web para clusters do Linux).

![Detalhes do cluster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Use o seguinte para entender os ícones na parte superior dessa folha e nas seções __Essentials__ e __Links rápidos__:

* __Configurações__ e __Todas as Configurações__: exibem a folha __Configurações__ do cluster, o que permite acessar informações de configuração detalhadas do cluster.

* __Painel__, __Painel do Cluster__ e __URL__: são maneiras de acessar o painel do cluster. Dependendo do tipo de cluster, pode ser apresentada uma lista dos painéis no cluster. Por exemplo, o tipo de cluster Spark exibirá uma lista dos painéis quando você selecionar o ícone __Painel__, enquanto um cluster Hadoop abrirá a interface do usuário da Web do Ambari.

* __Secure Shell__: informações necessárias para acessar o nó principal do cluster usando o SSH.

* __Escalar Cluster__: permite alterar o número de nós de trabalho deste cluster.

* __Excluir__: exclui o cluster HDInsight.

* __Início Rápido (![ícone de nuvem e raio = início rápido](./media/hdinsight-administer-use-portal-linux/quickstart.png))__: exibe informações que ajudarão você a começar a usar o HDInsight.

* __Usuários (![ícone de usuários](./media/hdinsight-administer-use-portal-linux/users.png))__: permite definir permissões para o _gerenciamento do portal_ deste cluster para outros usuários em sua assinatura do Azure.

	> [AZURE.IMPORTANT] Isso afeta _apenas_ o acesso e as permissões para esse cluster no Portal do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.

* __Marcas (![ícone de marca](./media/hdinsight-administer-use-portal-linux/tags.png))__: as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

* __Documentação__: links para a documentação do Azure HDInsight.

> [AZURE.IMPORTANT] Para gerenciar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para saber mais sobre como usar o Ambari, veja [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md).

### Configurações

Selecionar o ícone __Configurações__ ou o link __Todas as configurações__ exibirá a folha Configurações. Várias das funções disponíveis na área __Essentials__ discutida anteriormente também estão disponíveis aqui, como Dimensionamento ou Informações do Secure Shell. Você também pode acessar o seguinte de Configurações:

* __Logs de auditoria__: informações registradas úteis para diagnosticar problemas com a integridade de clusters.

* __Logon do Cluster__: exibe o __Nome de Usuário de Logon do Cluster__ e o __Endereço remoto__ que pode ser usado para acessar o cluster usando HTTPS.

* __Metastores Externos__: exibirá informações sobre metastores externos usados pelo seu cluster, se houver algum. Se você não configurou um metastore personalizado durante a configuração do cluster, não será exibida nenhuma informação.

* __Ações de Script__: exibe informações sobre Ações de Script que foram executadas nesse cluster. Você também pode executar novas Ações de Script e manter ou excluir Ações de Script que foram executadas anteriormente. Para obter mais informações sobre as Ações de Script, veja [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

* __Aplicativos__: exibe informações sobre os aplicativos que foram instalados no cluster e permite que você adicione novos aplicativos ao cluster do Azure Marketplace.

* __Chaves de Armazenamento do Azure__: exibe informações sobre as contas do Armazenamento do Azure usadas pelo cluster. Selecionar uma conta de armazenamento carregará a folha da conta do Armazenamento da conta selecionada.

* __Identidade AAD do Cluster__: exibe informações sobre a Entidade de Serviço para este cluster HDInsight. A Entidade de Serviço é usada para acessar Repositório Azure Data Lake. Se você não associou o cluster ao Repositório Azure Data Lake durante a criação do cluster, as entradas nessa folha exibirão __Não Configurado__. Para obter mais informações sobre o uso do Repositório Azure Data Lake com o HDInsight, veja [Criar um cluster HDInsight com o Repositório Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="scaling"></a>Escala

Para escalar um cluster usando o portal, selecione o cluster HDInsight e __Escalar Cluster__. Insira o __Número de nós de trabalho__ que você deseja definir para o cluster e, em seguida, clique em __Salvar__.

![imagem do dimensionamento da interface do usuário](./media/hdinsight-administer-use-portal-linux/scaling.png)

Para obter mais informações sobre como dimensionar as operações, consulte [Informações sobre como usar o HDInsight no Linux](hdinsight-hadoop-linux-information.md#scaling).

## Monitorar um cluster

A seção __Uso__ da folha do cluster HDInsight exibe informações sobre o número de núcleos disponíveis para sua assinatura para uso com o HDInsight, bem como o número de núcleos alocados para esse cluster e como eles são alocados para os nós presentes no cluster.

![Informações de uso](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] Para monitorar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para obter mais informações sobre como usar o Ambari, consulte [Gerenciar clusters HDInsight usando Ambari](hdinsight-hadoop-manage-ambari.md)

## Próximas etapas
Neste artigo, você aprendeu como criar um cluster HDInsight usando o portal do Azure e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Provisionar clusters HDInsight](hdinsight-provision-clusters.md)
* [Enviar trabalhos Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Qual versão do Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->