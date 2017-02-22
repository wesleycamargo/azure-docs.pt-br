---
title: Criar Azure HDInsight (Hadoop) usando um navegador da Web | Microsoft Docs
description: "Aprenda a criar clusters Hadoop, HBase, Storm ou Spark em Linux para HDInsight usando um navegador da Web e o portal de visualização do Azure."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: eed8a3863cc8f2fa7fbd9fe3306bd1bff0f5e91e


---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight usando o portal do Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, você aprenderá a criar clusters HDInsight baseados em Linux usando o portal.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um navegador da Web**. O Portal do Azure usa HTML5 e Javascript e pode não funcionar corretamente em navegadores mais antigos.

### <a name="access-control-requirements"></a>Requisitos de controle de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters
O portal do Azure expõe a maioria das propriedades do cluster. Usando um modelo do Azure Resource Manager, é possível ocultar muitos detalhes. Para obter mais informações, veja [Criar clusters Hadoop baseados em Linux no HDInsight usando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, em **Análises de Dados** e em **HDInsight**.
   
    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Criar um novo cluster no portal do Azure")

3. Insira o **Nome do Cluster**: esse nome deve ser globalmente exclusivo.

4. No menu suspenso **Assinatura**, selecione a assinatura do Azure que será usada para o cluster.

5. Clique em **configuração de Cluster**e, em seguida, selecione:
   
   * **Tipo de Cluster**: se não souber o que escolher, selecione **Hadoop**. É o tipo de cluster mais popular.
     
     > [!IMPORTANT]
     > Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster. 
     > 
     > 
   * **Sistema Operacional**: selecione **Linux**.
   * **Versão**: use a versão padrão se não souber o que escolher. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).
   * **Camada de Cluster**: o Azure HDInsight fornece as ofertas de nuvem de Big Data em duas categorias: camada Standard e camada Premium. Para obter mais informações, consulte [Camadas de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
     
     ![Configuração da camada premium do HDInsight](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-cluster-type-configuration.png)

6. Clique em **aplicativos** para instalar aplicativos que funcionam com clusters HDInsight. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria. Para saber mais, veja [HDInsight instalar aplicativos](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation). 

7. Clique em **Credenciais** e, em seguida, digite uma senha para o usuário admin. Você também deve inserir um **Nome de Usuário de SSH** e uma **SENHA** ou uma **CHAVE PÚBLICA**, que será usada para autenticar o usuário SSH. Usar uma chave pública é a abordagem recomendada. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.
   
    ![Forneça credenciais de cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "fornecer credenciais de cluster")
   
    Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:
   
   * [Usar SSH com Hadoop no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Usar SSH com Hadoop no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


8. Na folha **Fonte de Dados**, especifique se deseja o Armazenamento do Azure (WASB) ou Data Lake Store como seu armazenamento padrão.  

    * **Blobs de armazenamento do Azure como armazenamento padrão**

        Para **o tipo de armazenamento primário**, clique em **armazenamento do Azure**. Especifique os detalhes da conta de armazenamento e do contêiner de armazenamento, especifique um local e clique em **Identidade AAD do Cluster**.

        ![Adicionar entidade de serviço ao cluster do HDInsight](./media/hdinsight-hadoop-create-linux-cluster-portal/hdi.adl.1.png "Adicionar entidade de serviço ao cluster do HDInsight")

      >[!NOTE]
      > A Identidade AAD do Cluster é usada para disponibilizar o cluster para os repositórios Azure Data Lake com base na configuração do AAD. Você usará essa opção se você quiser usar uma conta do Data Lake Store como um armazenamento adicional associado ao cluster.
      > 
      >

        Clique em **Selecionar** para salvar a configuração de fonte de dados.

    * **Azure Data Lake Store como armazenamento padrão**

        Para obter instruções sobre como criar um cluster HDInsight com o Data Lake Store como armazenamento padrão, veja [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        Clique em **Selecionar** para salvar a configuração de fonte de dados.

9. Clique em **Camadas de preços do nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.
   
    ![Nó folha de camadas de preços](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "especificar o número de nós de cluster")
   
   > [!IMPORTANT]
   > Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
   > 
   > Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Clique em **Selecionar** para salvar a configuração de preços do nó.

10. Clique em **Configuração Avançada** para definir outras configurações opcionais, como adicionar uma **Rede Virtual**, definir um **Metastore Externo** para armazenar dados de Hive e Oozie, use as **Ações de Script** para personalizar um cluster para instalar componentes personalizados ou use **Contas de armazenamento adicionais** com o cluster.

    * **Rede virtual**: selecione uma rede virtual do Azure e a sub-rede, se você deseja colocar o cluster em uma rede virtual.  
      
        ![Folha de rede virtual](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "especificar detalhes da rede virtual")
      
        Para obter informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, veja [Estender as funcionalidades do HDInsight usando uma Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

    * Clique em **Metastores Externo** para especificar o banco de dados SQL que você deseja usar para salvar os metadados de Hive e Oozie associados ao cluster.
      
      > [!NOTE]
      > A configuração de Metastore não está disponível para tipos de cluster HBase.
      > 
      > 
      
        ![Folha de metastores personalizado](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "especificar metastores externos")
      
        Para **Usar um banco de dados SQL existente para metadados do Hive**, clique em **Sim**, selecione um banco de dados SQL e forneça o nome de usuário e a senha para o banco de dados. Repita essas etapas se você quiser **Usar um banco de dados SQL existente para metadados de Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração Opcional**.
      
      > [!NOTE]
      > O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.
      > 
      > 
      
        &nbsp;
      
      > [!IMPORTANT]
      > Ao criar um metastore, não use um nome de banco de dados que contém a traços ou hifens, pois isso pode causar falha no processo de criação de cluster.
      > 
      > 

    * **Ações de Script** se você quiser usar um script personalizado para personalizar um cluster enquanto o mesmo estiver sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md). Na folha Ações de Script forneça os detalhes como mostrado na captura de tela.
      
        ![Folha de ação de script](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "especificar a ação de script")

    * Clique em **Contas de Armazenamento Adicionais** para especificar contas de armazenamento adicionais para associar ao cluster. Na folha **Chaves de Armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento** e selecione uma conta de armazenamento existente ou crie uma nova conta.
      
        ![Folha de armazenamento adicional](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "especificar contas de armazenamento adicionais")
      
        Você também pode adicionar mais contas de armazenamento após um cluster ser criado.  Confira [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).
      
        Clique em **Selecionar** até voltar para a folha **Novo cluster HDInsight**.
      
        Além da conta de Armazenamento de blobs, também é possível vincular repositórios Azure Data Lake. A configuração pode ser feita com a definição do AAD por meio da Fonte de Dados na qual a conta de armazenamento padrão e o contêiner padrão foram configurados.

11. Na folha **Novo Cluster HDInsight**, certifique-se de que a opção **Fixar no Quadro Inicial** está marcada e clique em **Criar**. Isso criará o cluster e adicionará um bloco para ele no quadro inicial do seu portal do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.
    
    | Durante o provisionamento | Provisionamento concluído |
    | --- | --- |
    | ![Indicador de provisionamento no quadro inicial](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) |![Bloco de cluster provisionado](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |
    
    > [!NOTE]
    > Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.
    > 
    > 
12. Quando o processo de criação for concluído, clique no bloco do cluster no quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.
    
    ![Folha de cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "propriedades do Cluster")
    
    Use o seguinte para entender os ícones na parte superior desta folha e na seção **Informações gerais** :
    
    * **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, que permite acessar informações de configuração detalhadas do cluster.
    * **Painel**, **Painel do Cluster** e **URL**: essas são as maneiras de acessar o painel do cluster, que é um portal da Web para executar trabalhos no cluster.
    * **Secure Shell**: informações necessárias para acessar o cluster usando o SSH.
    * **Excluir**: exclui o cluster HDInsight.
    * **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): exibe informações que ajudarão você a começar a usar o HDInsight.
    * **Usuários** (![ícone de usuários](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): permite definir permissões para o *gerenciamento do portal* deste cluster para outros usuários em sua assinatura do Azure.
      
      > [!IMPORTANT]
      > Isso afeta *apenas* o acesso e as permissões para esse cluster no Portal do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.
      > 
      > 
    * **Marcações** (![ícone de marcação](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): as marcações permitem estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada **projeto**e usar um valor comum para todos os serviços associados a um projeto específico.

## <a name="customize-clusters"></a>Personalizar clusters
* Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster:

### <a name="hadoop-clusters"></a>Clusters do Hadoop
* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters do HBase
* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm
* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clusters do Spark
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)




<!--HONumber=Jan17_HO4-->


