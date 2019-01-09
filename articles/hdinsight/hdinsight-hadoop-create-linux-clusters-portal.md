---
title: Criar clusters do Apache Hadoop usando um navegador da Web – Azure HDInsight
description: Saiba como criar clusters do Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark no Linux para HDInsight usando um navegador da Web e o portal de visualização do Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: ca8b398a796284e34ce4e0b577c335552ca75a98
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974260"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight usando o portal do Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, você aprenderá a criar clusters HDInsight baseados em Linux usando o portal.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um navegador da Web**. O Portal do Azure usa HTML5 e Javascript e pode não funcionar corretamente em navegadores mais antigos.

## <a name="create-clusters"></a>Criar clusters
O portal do Azure expõe a maioria das propriedades do cluster. Usando modelos do Azure Resource Manager, é possível ocultar muitos detalhes. Para saber mais, consulte [Criar clusters Apache Hadoop baseados em Linux no HDInsight usando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **+ Criar um recurso**.

1.  Em **Azure Marketplace**, selecione **Análises**.

1.  Sob **Em destaque**, selecione **HDInsight**.
   
    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Criar um novo cluster no portal do Azure")

1. Na página **HDInsight**, selecione **Personalizado (tamanho, configurações, aplicativos)**.

1. Selecione **1 Básico** e insira as seguintes informações:

    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Criar um novo cluster no portal do Azure")

    * Insira o **Nome do cluster**: Esse nome deve ser globalmente exclusivo.

    * No menu suspenso **Assinatura**, selecione a assinatura do Azure usada para o cluster.

    * Selecione **Tipo de cluster** e, em seguida, selecione o tipo de cluster (Hadoop, Spark etc.) que você quer criar. O **Sistema operacional** será **Linux**. Depois, selecione uma versão de tipo de cluster. Use a versão padrão se não souber o que escolher. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster.
        
    * Para **Nome de Usuário de Logon no Cluster** e **Senha de Logon no Cluster**, forneça o nome de usuário e a senha para o usuário administrador.

    * Insira um **nome de usuário SSH** e, se você quiser que a senha SSH seja igual à senha de administrador que você especificou anteriormente, selecione a caixa de seleção **Usar a mesma senha do logon do cluster**. Se não quiser, forneça uma **SENHA** ou uma **CHAVE PÚBLICA**, que será usada para autenticar o usuário SSH. Usar uma chave pública é a abordagem recomendada. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.
   
        Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Para **Grupo de recursos**, especifique se deseja criar um novo grupo de recursos ou usar um existente.

    * Especifique um **local** de data center para criar o cluster.

    * Selecione **Próximo** para ir para a próxima página.

4. Em **2 Segurança + rede**, você pode conectar seu cluster a uma rede virtual usando o menu suspenso fornecido. Selecione uma rede virtual do Azure e a sub-rede se você desejar colocar o cluster em uma rede virtual. Para obter informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, veja [Estender as funcionalidades do HDInsight usando uma Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md). Se quiser usar o **Enterprise Security Package**, também é possível seguir estas instruções: [Configurar um cluster HDInsight com o Enterprise Security Package usando o Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selecione **Próximo** para ir para a próxima página.


5. Em **3 Armazenamento**, especifique se deseja o WASB (Armazenamento do Microsoft Azure) ou Data Lake Storage como armazenamento padrão. Consulte a tabela abaixo para obter mais informações.

     ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Criar um novo cluster no portal do Azure")

     | Armazenamento                                      | Descrição |
     |----------------------------------------------|-------------|
     | **Blobs de armazenamento do Azure como armazenamento padrão**   | <ul><li>Para o **tipo de armazenamento primário**, selecione **Armazenamento do Azure**. Depois disso, para **Método de seleção**, você poderá escolher **Minhas assinaturas** se desejar especificar uma conta de armazenamento que faça parte de sua assinatura do Azure e, em seguida, selecionar a conta de armazenamento. Caso contrário, clique em **Chave de acesso** e forneça as informações da conta de armazenamento que você deseja escolher de fora de sua assinatura do Azure.</li><li>Para **contêiner padrão**, você pode optar por usar o nome do contêiner padrão sugerido pelo Portal ou especificar seu próprio nome.</li><li>Se você estiver usando o WASB como o armazenamento padrão, você poderá (opcionalmente) clicar em **Contas de Armazenamento Adicionais** para especificar contas de armazenamento adicionais para associar ao cluster. Em **Chaves de Armazenamento do Microsoft Azure**, clique em **Adicionar uma chave de armazenamento** e, em seguida, você poderá fornecer uma conta de armazenamento de suas assinaturas do Azure ou de outras assinaturas (fornecendo a tecla de acesso da conta de armazenamento).</li><li>Se você estiver usando o WASB como armazenamento padrão, poderá clicar (opcionalmente) em **Acesso ao Data Lake Storage** para especificá-lo como armazenamento adicional. Para saber mais, consulte [Início Rápido: configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storeage como armazenamento padrão** | Para **Tipo de armazenamento primário**, selecione **Azure Data Lake Storage Gen1** ou **Azure Data Lake Storage Gen2** e, em seguida, consulte o artigo [Início Rápido: configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) para saber mais. |
     | **Metastores externas**                      | Opcionalmente, você pode especificar um Banco de Dados SQL para salvar metadados de Hive e Oozie associados ao cluster. Para **Selecionar um Banco de Dados SQL para Hive**, selecione um Banco de Dados SQL e então forneça o nome de usuário e a senha para ele. Repita essas etapas para metadados do Oozie.<br><br>Há algumas considerações ao usar o Banco de Dados SQL do Azure para metastores. <ul><li>O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.</li><li>Ao criar um metastore, não use um nome de banco de dados que contém a traços ou hifens, pois isso pode causar falha no processo de criação de cluster.</li></ul> |

     > [!WARNING]  
     > Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

     Selecione **Próximo** para ir para a próxima página.


6. Em **4 Aplicativos (opcional)**, selecione os aplicativos desejados.  Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria. Para saber mais, veja [HDInsight instalar aplicativos](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Selecione **Próximo** para ir para a próxima página.


6. **5 Tamanho do cluster** mostra informações sobre os nós usados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado de executar o cluster também é mostrado.
   
    ![Tipos de preço de nó](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Especificar o número de nós de cluster")
   
   > [!IMPORTANT]  
   > Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
   > 
   > Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Selecione **Próximo** para ir para a próxima página.

8. Em **6 Ações de script**, você pode personalizar um cluster para instalar componentes personalizados.  Use essa opção se você quiser usar um script personalizado para personalizar um cluster enquanto o mesmo estiver sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

   Selecione **Próximo** para ir para a próxima página.

9. Em **7 Resumo**, verifique as informações que você inseriu anteriormente e, em seguida, selecione **Criar**.

     ![Tipos de preço de nó](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Especificar o número de nós de cluster")
    
    > [!NOTE]  
    > Levará algum tempo para que o cluster seja criado, geralmente, cerca de 20 minutos. Monitore as **Notificações** para conferir o processo de provisionamento.

10. Após a conclusão do processo de criação, selecione **Ir para o Recurso** na notificação **Implantação bem-sucedida**. A janela do cluster fornece as informações a seguir.
    
    ![Interface de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriedades do cluster")
    
    Use o descrito a seguir para entender os ícones na parte superior.
    
    * A guia **Visão geral** fornece todas as informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.
    * **Painel** direciona você para o portal do Ambari associado ao cluster.
    * **Secure Shell**: informações necessárias para acessar o cluster usando o SSH.
    * **Dimensionar cluster** permite aumentar o número de nós de trabalho associados ao cluster.
      * **Excluir**: exclui o cluster HDInsight.
    

## <a name="customize-clusters"></a>Personalizar clusters
* Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Próximas etapas
Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster:

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase
* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters do Apache Storm
* [Desenvolver topologias em Java para o Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes de Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters do Apache Spark
* [Criar um aplicativo autônomo usando Scala](spark/apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)

