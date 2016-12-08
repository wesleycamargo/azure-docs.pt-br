---
title: Criar clusters Hadoop no HDInsight | Microsoft Docs
description: Aprenda a criar clusters do Azure HDInsight usando o Portal do Azure.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6680fb1e33b221a74d6c5db4cf5012d9109eac6


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Crie clusters Hadoop baseados no Windows no HDInsight usando o Portal do Azure
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Saiba como criar um cluster Hadoop no HDInsight usando o Portal do Azure. O Portal do Microsoft [Azure](../azure-portal-overview.md) é um local central no qual você pode provisionar e gerenciar os recursos do Azure. O Portal do Azure é uma das ferramentas que você pode usar para criar cluster do Hadoop baseado em Linux ou Windows no HDInsight. Para outros recursos e ferramentas de criação de cluster, clique na guia Selecionar na parte superior dessa página ou consulte [Métodos de criação de cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

## <a name="prerequisites"></a>Pré-requisitos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisitos de controle de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters
**Para criar um cluster HDInsight**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, em **Análises de Dados** e em **HDInsight**.
   
    ![Criando um novo cluster no Portal do Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creating a new cluster in the Azure Portal")
3. Digite ou selecione os valores a seguir:
   
   * **Nome do Cluster**: insira um nome para o cluster. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se o nome estiver disponível.
   * **Tipo de Cluster**: selecione **Hadoop**. As outras opções incluem **HBase**, **Storm** e **Spark**.
     
     > [!IMPORTANT]
     > Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster.
     > 
     > 
   * **Sistema Operacional do Cluster** – selecione **Windows**. Para criar um cluster baseado em Linux, selecione **Linux**.
   * **Versão**: consulte [versões do HDInsight](hdinsight-component-versioning.md).
   * **Assinatura**: selecione a assinatura do Azure que será usada para criar esse cluster.
   * **Grupo de Recursos**: escolha um grupo de recursos existente ou crie um novo. A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.
   * **Credenciais**: configure o nome de usuário e a senha do usuário do Hadoop (usuário HTTP). Se você habilitar a área de trabalho remota para o cluster, você precisará configurar uma data de vencimento de conta e senha e o nome de usuário do usuário de área de trabalho remota. Clique em **Selecionar** na parte inferior para salvar as alterações.
     
          ![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")
   * **Fonte de Dados**: crie uma nova conta de armazenamento do Azure ou escolha uma conta existente que será usada como o sistema de arquivos padrão do cluster.
     
          ![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")
     
         * **Método de Seleção**: defina essa opção como **De todas as assinaturas** para habilitar a procura de contas de armazenamento de todas as suas assinaturas. Defina essa opção como **Tecla de Acesso** se você desejar inserir o **Nome de Armazenamento** e a **Tecla de Acesso** de uma conta de armazenamento existente.
         * **Selecionar conta de armazenamento/Criar nova**: clique em** Selecionar conta de armazenamento** para procurar e escolher uma conta de armazenamento existente que você deseja associar ao cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.
         * **Escolher Contêiner Padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.
         * **Local**: a região geográfica na qual a conta de armazenamento está ou será criada. Esse local determinará o local do cluster.  O cluster e sua conta de armazenamento padrão devem estar localizados no mesmo data center do Azure.
   * **Tipos de Preço do Nó**: defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

        ![Folha de camadas de preços de nó](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


    * **Configuração Opcional**: para selecionar a versão do cluster, bem como definir outras configurações opcionais, como adicionar uma **Rede Virtual**, definir um **Metastore Externo** para armazenar dados de Hive e Oozie, use as Ações de Script para personalizar um cluster para instalar componentes personalizados ou use contas de armazenamento adicionais com o cluster.

    * **Versão do HDInsight**: selecione a versão que deseja usar para o cluster. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).
    * **Rede virtual**: selecione uma rede virtual do Azure e a sub-rede, se você deseja colocar o cluster em uma rede virtual.  

        ![Folha de rede virtual](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

        Para obter informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, veja [Estender as funcionalidades do HDInsight usando uma Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).



    * **Metastores externos**: especifique um banco de dados SQL do Azure para armazenar metadados de Hive e Oozie associados ao cluster.

        > [AZURE.NOTE] A configuração de Metastore não está disponível para tipos de cluster HBase.

    ![Personalize a folha de metastores](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")

    Para **Usar um banco de dados SQL existente para metadados do Hive**, clique em **Sim**, selecione um banco de dados SQL e forneça o nome de usuário e a senha para o banco de dados. Repita essas etapas se você quiser **Usar um banco de dados SQL existente para metadados de Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração Opcional**.

    >[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

            &nbsp;

            > [AZURE.IMPORTANT] Ao criar um metastore, não use um nome de banco de dados que contém a traços ou hifens, pois isso pode causar falha no processo de criação de cluster.

          * **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.


            ![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


        * **Chaves de Armazenamento do Azure**: especifique contas de armazenamento adicionais para associar ao cluster. Na folha **Chaves de Armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento** e selecione uma conta de armazenamento existente ou crie uma nova conta.


            ![Folha de armazenamento adicional](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


1. Clique em **Criar**. Escolher **Fixar no Quadro Inicial** adicionará um bloco para o cluster ao Quadro Inicial do Portal. O ícone indica que o cluster está sendo criado e será alterado para exibir o ícone de HDInsight após a conclusão da criação.
   
    Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.
2. Quando a criação for concluída, clique no bloco para o cluster no quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.

    ![Folha de cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Cluster properties")


    Use o seguinte para entender os ícones na parte superior desta folha e na seção **Informações gerais** :


    * **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, que permite acessar informações de configuração detalhadas do cluster.
    * **Painel**, **Painel do Cluster** e **URL**: essas são as maneiras de acessar o painel do cluster, que é um portal da Web para executar trabalhos no cluster.
    * **Área de Trabalho Remota**: permite habilitar/desabilitar a área de trabalho remota nos nós do cluster.
    * **Cluster em Escala**: permite alterar o número de nós de trabalho deste cluster.
    * **Excluir**: exclui o cluster HDInsight.
    * **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-provision-clusters/quickstart.png)): exibe informações que ajudarão você a começar a usar o HDInsight.
    * **Usuários** (![ícone de usuários](./media/hdinsight-provision-clusters/users.png)): permite definir permissões para o _gerenciamento do portal_ deste cluster para outros usuários em sua assinatura do Azure.


        > [AZURE.IMPORTANT] Isso afeta _apenas_ o acesso e as permissões para este cluster no Portal e não quem pode se conectar ao cluster HDInsight ou enviar trabalhos para ele.

    * **Marcações** (![ícone de marcação](./media/hdinsight-provision-clusters/tags.png)): as marcações permitem estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__e usar um valor comum para todos os serviços associados a um projeto específico.

## <a name="customize-clusters"></a>Personalizar clusters
* Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalizar clusters do HDInsight baseados em Windows usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - aprenda como começar a trabalhar com seu cluster HDInsight
* [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md) - aprenda como enviar trabalhos ao HDInsight de forma programática
* [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md)




<!--HONumber=Nov16_HO3-->


