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
	ms.date="11/19/2015"
	ms.author="larryfr"/>

# Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-portal-management-selector.md)]


Com o [Portal do Azure][preview-portal], você pode provisionar e gerenciar clusters Hadoop baseados em Linux no Azure HDInsight.

> [AZURE.NOTE]As etapas deste documento são específicas dos clusters Hadoop baseados em Linux. Para obter informações sobre como trabalhar com clusters baseados no Windows, veja [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md)


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Outras ferramentas para administrar o HDInsight
Também há outras ferramentas disponíveis para administrar o HDInsight além do portal do Azure.

- [Administrar o HDInsight usando o a CLI do Azure](hdinsight-administer-use-command-line.md): a CLI do Azure é uma ferramenta de linha de comando entre plataformas que permite que você gerencie os serviços do Azure

- [Administrar o HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md): o Azure PowerShell fornece cmdlets do PowerShell para gerenciar serviços do Azure

## Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Provisionar clusters do HDInsight

Você pode provisionar clusters HDInsight no portal do Azure executando as seguintes etapas:

1. Entre no [Portal do Azure][preview-portal].

2. Selecione **NOVO**, selecione __Análises de Dados__ e, em seguida, selecione __HDInsight__

	![Criando um novo cluster no Portal do Azure](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. Insira um __Nome de Cluster__ e selecione o __Tipo de Cluster__ que você deseja criar. Uma marca de seleção verde será exibida ao lado do __Nome de Cluster__, se ele estiver disponível.

	![Nome do cluster, tipo de cluster e tipo de sistema operacional](./media/hdinsight-administer-use-portal-linux/clustername.png)

4. Se você tiver mais de uma assinatura, selecione a entrada __Assinatura__ para selecionar a assinatura do Azure que será usada para o cluster.

5. Para o __Grupo de Recursos__, você pode selecionar a entrada para ver uma lista de grupos de recursos existentes e, em seguida, selecionar um para criação do cluster. Ou então, você pode selecionar __Criar Novo__ e digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo está disponível.

	> [AZURE.NOTE]Por padrão, essa entrada será um de seus grupos de recursos existentes, se houver algum disponível.

6. Selecione __Credenciais__ e digite uma __Senha de Logon do Cluster__ para o __Nome de Usuário de Logon do Cluster__. Você também deve inserir um __Nome de Usuário de SSH__ e uma __SENHA__ ou uma __CHAVE PÚBLICA__, que será usada para autenticar o usuário SSH. Por fim, use o botão __Selecionar__ para definir as credenciais. A área de trabalho remota não será usada neste documento, então, você pode deixá-la desabilitada.

	![Folha de credenciais de cluster](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)
    
    > [AZURE.NOTE]O SSH é usado para acessar remotamente o cluster HDInsight usando uma linha de comando. O nome de usuário e a senha ou chave pública usados aqui serão usados para se conectar ao cluster por meio do SSH.

    Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows)

7. Para a __Fonte de Dados__, você pode selecionar a entrada para escolher uma fonte de dados existente ou criar uma nova.

	![Folha de fonte de dados](./media/hdinsight-administer-use-portal-linux/datasource.png)

	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha __Fonte de Dados__.

	- __Método de Seleção__: defina-o como __De todas as assinaturas__ para habilitar a procura de contas de armazenamento em suas assinaturas. Defina-o como __Chave de Acesso__ se você desejar inserir o __Nome de Armazenamento__ e a __Chave de Acesso__ de uma conta de armazenamento existente.

	- __Criar Novo__: use essa opção para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.

	- __Escolher Contêiner Padrão__: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome utilizado para o cluster, para que você possa reconhecer facilmente que o contêiner é usado para esse cluster específico.

	- __Local__: a região geográfica em que a conta de armazenamento estará ou na qual será criada.

		> [AZURE.IMPORTANT]Se for selecionando o local para a fonte de dados padrão, também será definido o local do cluster HDInsight. O cluster e a fonte de dados padrão devem estar localizados na mesma região.

	- __Selecionar__: use essa opção para salvar a configuração da fonte de dados.

	
8. Selecione __Camadas de Preços de Nó__ para exibir informações sobre os nós que serão criados para esse cluster. Por padrão, o número de nós de trabalho será definido como __4__.


	O custo estimado do cluster será mostrado na parte inferior da folha.

	![Folha de camadas de preços de nó](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)

	Use o botão __Selecionar__ para salvar as informações de __Camadas de Preços de Nó__.

9. Selecione __Configuração opcional__. Essa folha permite configurar os seguintes itens:

	* __Versão do HDInsight__: a versão do HDInsight usada para o cluster. Para obter mais informações sobre o controle de versão do HDInsight, consulte [Controle de versão de componente do HDInsight](hdinsight-component-versioning.md)

	* __Metastores Externos__: essa opção permite que você selecione um Banco de Dados SQL, que será usado para armazenar informações de configuração para o Oozie e o Hive. Isso permite reutilizar a configuração ao excluir e recriar um cluster, em vez de sempre precisar recriar a configuração do Hive e do Oozie.

	* __Rede Virtual__: permite que você coloque o cluster HDInsight na mesma rede virtual que outros recursos, como um Banco de Dados SQL ou uma Máquina Virtual do Azure. Colocar recursos em uma rede virtual permite que eles se comuniquem diretamente entre si, ignorando os gateways públicos que manipulam o tráfego de entrada da Internet. Para obter mais informações sobre como o HDInsight aproveita as Redes Virtuais do Azure, veja [Estender os recursos do HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

		> [AZURE.IMPORTANT]Você deve criar a Rede Virtual do Azure antes de criar o cluster HDInsight, já que você não pode criar uma nova rede por meio da configuração do HDInsight.
		>
		> Atualmente (25/8/2015), existe uma limitação que determina que apenas um cluster HDInsight baseado em Linux pode estar presente em uma Rede Virtual do Azure.
        >
        > Não é possível usar uma Rede Virtual v1 (Clássica) do Azure com o HDInsight baseado em Linux. A Rede Virtual deve ser v2 (Gerenciador de Recursos do Azure) para que seja listada como uma opção durante o processo de criação do cluster HDInsight no Portal do Azure ou para poder ser usada durante a criação de um cluster a partir da CLI do Azure ou do Azure PowerShell.
        >
        > Se você tiver recursos em uma rede v1 e quiser disponibilizar o HDInsight diretamente para esses recursos por meio de uma rede virtual, veja [Conectando Redes Virtuais clássicas a novas Redes Virtuais](../virtual-network/virtual-networks-arm-asm-s2s.md) para obter informações sobre como conectar uma Rede Virtual v2 a uma Rede Virtual v1. Quando essa conexão for estabelecida, você poderá criar o cluster HDInsight na Rede Virtual v2.

	* __Ações de Script__: isso permite que você especifique scripts Bash que personalizam o cluster HDInsight durante o provisionamento. Por exemplo, há um [script que instala o Hue](hdinsight-hadoop-hue-linux.md) (um cliente gráfico para trabalhar com o Hadoop). Para saber mais sobre Ações de Script, veja [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

	* __Chaves de Armazenamento do Azure__: isso permite associar outras contas de armazenamento ao servidor do HDInsight.

		> [AZURE.NOTE]O HDInsight só pode acessar contas de Armazenamento do Azure usadas como o armazenamento de dados padrão, adicionadas por meio dessa seção de configuração ou que sejam acessíveis publicamente.

	![Folha de configuração opcional](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

10. Verifique se a opção __Fixar no Quadro Inicial__ está marcada e selecione __Criar__. Isso criará o cluster e adicionará um bloco para o mesmo para o Quadro inicial do seu Portal do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.

	| Durante o provisionamento | Provisionamento concluído |
	| ------------------ | --------------------- |
	| ![Indicador de provisionamento no quadro inicial](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada __Notificações__ à esquerda da página para verificar o processo de provisionamento.

## Gerenciar um cluster

Se um cluster for selecionado no Portal do Azure, serão exibidas informações essenciais sobre o cluster, como o nome, o grupo de recursos, o sistema operacional e a URL do painel do cluster (usada para acessar o Ambari Web para clusters do Linux).

![Detalhes do cluster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Use o seguinte para entender os ícones na parte superior desta folha e nas seções __Noções Básicas__ e __Links Rápidos__:

* __Configurações__ e __Todas as Configurações__: exibem a folha __Configurações__ do cluster, que permite acessar informações de configuração detalhadas do cluster.

* __Painel__, __Painel do Cluster__ e __URL__: essas são todas as maneiras de acessar o painel do cluster, que é o Ambari Web para clusters baseados em Linux.

* __Secure Shell__: informações necessárias para acessar o cluster usando o SSH.

* __Escalar Cluster__: permite alterar o número de nós de trabalho deste cluster.

* __Excluir__: exclui o cluster HDInsight.

* __Início Rápido (![ícone de nuvem e raio = início rápido](./media/hdinsight-administer-use-portal-linux/quickstart.png))__: exibe informações que ajudarão você a começar a usar o HDInsight.

* __Usuários (![ícone de usuários](./media/hdinsight-administer-use-portal-linux/users.png))__: permite definir permissões para o _gerenciamento do portal_ deste cluster para outros usuários em sua assinatura do Azure.

	> [AZURE.IMPORTANT]Isso afeta _apenas_ o acesso e as permissões para esse cluster no Portal do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.

* __Marcas (![ícone de marca](./media/hdinsight-administer-use-portal-linux/tags.png))__: as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

* __Documentação__: links para a documentação do Azure HDInsight.

> [AZURE.IMPORTANT]Para gerenciar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para saber mais sobre como usar o Ambari, veja [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="scaling"></a>Escala

Para escalar um cluster usando o portal, selecione o cluster HDInsight e __Escalar Cluster__. Insira o __Número de nós de trabalho__ que deseja definir para o cluster e clique em __Salvar__.

![imagem do dimensionamento da interface do usuário](./media/hdinsight-administer-use-portal-linux/scaling.png)

Para obter mais informações sobre como escalar as operações, veja [Informações sobre como usar o HDInsight no Linux](hdinsight-hadoop-linux-information.md#scaling).

## Monitorar um cluster

A seção __Uso__ da folha do cluster HDInsight exibe informações sobre o número de núcleos disponíveis para sua assinatura para uso com o HDInsight, bem como o número de núcleos alocados para esse cluster e como eles são alocados para os nós presentes no cluster.

![Informações de uso](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]Para monitorar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para saber mais sobre como usar o Ambari, veja [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

## Próximas etapas
Neste artigo, você aprendeu como criar um cluster HDInsight usando o portal do Azure e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Provisionar clusters HDInsight](hdinsight-provision-clusters.md)
* [Enviar trabalhos Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](../hdinsight-get-started.md)
* [Qual versão do Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_1203_2015-->