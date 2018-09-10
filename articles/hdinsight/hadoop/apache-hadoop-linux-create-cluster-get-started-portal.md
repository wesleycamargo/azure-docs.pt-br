---
title: 'Início Rápido: Introdução ao Hadoop e ao Hive no Azure HDInsight usando o portal do Azure '
description: Saiba como criar clusters do HDInsight usando o Portal do Azure e consultar dados com o Hive.
keywords: introdução ao hadoop, hadoop linux, início rápido do hadoop, introdução ao hive, início rápido do hive
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jasonh
ms.openlocfilehash: 802c35f94457cc60ec27b3dd061d763d1acab4f5
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042229"
---
# <a name="quickstart-get-started-with-hadoop-and-hive-in-azure-hdinsight-using-the-azure-portal"></a>Início Rápido: Introdução ao Hadoop e ao Hive no Azure HDInsight usando o Portal do Azure

Neste artigo, você aprenderá a criar clusters [Hadoop](http://hadoop.apache.org/) no HDInsight usando o Portal do Azure e, depois, executar trabalhos do Hive no HDInsight. A maioria dos trabalhos de Hadoop consiste em trabalhos em lotes. Criar um cluster, executar alguns trabalhos e excluir o cluster. Neste artigo, você deve executar as três tarefas.

Neste início rápido, você usa o Portal do Azure para criar um cluster Hadoop do HDInsight. Também é possível criar um cluster usando o [modelo do Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Atualmente, o HDInsight vem com [sete tipos diferentes de cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Cada tipo de cluster dá suporte a um conjunto diferente de componentes. Todos os tipos de cluster dão suporte ao Hive. Para obter uma lista de componentes com suporte no HDInsight, confira [Novidades nas versões de cluster Hadoop fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)  

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-hadoop-cluster"></a>Criar um cluster Hadoop

Nesta seção, você criará um cluster Hadoop no HDInsight usando o portal do Azure. 

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. No portal do Azure, selecione **Criar um recurso** > **Dados + Análise** > **HDInsight**. 

    ![Databricks no portal do Azure](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-hdinsight-on-portal.png "Databricks no portal do Azure")

2. Em **HDInsight** > **Criação Rápida** > **Noções Básicas**, forneça os valores conforme sugerido na captura de tela a seguir:

    ![HDInsight para Linux - Introdução ao fornecimento de valores básicos do cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-provide-basic-values.png "Fornecer valores básicos para a criação de um cluster HDInsight")

    Digite ou selecione os valores a seguir:
    
    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Nome do cluster**     | Insira um nome para o cluster Hadoop. Como todos os clusters no HDInsight compartilham o mesmo namespace DNS esse nome precisa ser exclusivo. O nome pode ter até 59 caracteres incluindo letras, números e hifens. O primeiro e último caracteres do nome não podem ser hifens. |
    |**Assinatura**     |  Selecione sua assinatura do Azure. |
    |**Tipo de cluster**     | Ignorar isso por enquanto. Forneça essa entrada na próxima etapa deste procedimento.|
    |**Nome de usuário e senha de logon do cluster**     | O nome padrão de logon é **admin**. A senha deve ter no mínimo 10 caracteres e deve conter pelo menos um dígito, uma letra maiúscula, uma minúscula e um caractere não alfanumérico (exceto os caracteres ' " ` \). **Não forneça** senhas comuns, como "Pass@word1".|
    |**Nome de usuário de SSH** | O nome de usuário padrão é **sshuser**.  Você pode fornecer outro nome para o nome de usuário de SSH. |
    | **Use a mesma senha como logon do cluster** | Marque essa caixa de seleção para usar a mesma senha para o usuário SSH que aquela fornecida para o usuário de logon do cluster.|
    |**Grupo de recursos**     | Crie um grupo de recursos ou selecione um grupo de recursos existente.  Um grupo de recursos é um contêiner de componentes do Azure.  Nesse caso, o grupo de recursos contém o cluster HDInsight e a conta de Armazenamento do Azure dependente. |
    |**Localidade**     | Selecione um local do Azure no qual você deseja criar o cluster.  Escolha um local mais próximo a você para obter melhor desempenho. |
        
3. Selecione **Tipo de cluster** e forneça as entradas, conforme mostra a captura de tela a seguir:

    ![HDInsight para Linux - Introdução ao fornecimento de valores básicos do cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-provide-cluster-type.png "Fornecer valores básicos para a criação de um cluster HDInsight")

    Digite os valores a seguir:
    
    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Tipo de cluster**     | Selecione **Hadoop** |
    |**Sistema operacional**     |  Selecione sua assinatura do Azure. |
    |**Versão**     | Selecione **Hadoop 2.7.3 (HDI 3.6)**|

    Clique em **Selecionar** e clique em **Avançar**.

4. Na guia **Armazenamento**, forneça as entradas, conforme mostra a captura de tela a seguir:

    ![HDInsight para Linux - Introdução ao fornecimento de valores de armazenamento de cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-select-storage.png "Fornecer valores de armazenamento para a criação de um cluster HDInsight")

    Digite os valores a seguir:
    
    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Tipo de armazenamento primário**     | Neste artigo, selecione **Armazenamento do Azure** para usar o Azure Storage Blob como a conta de armazenamento padrão. Também é possível usar o Azure Data Lake Storage como o armazenamento padrão. |
    |**Método de seleção**     |  Neste artigo, selecione **Minhas assinaturas** para usar uma conta de armazenamento de sua assinatura do Azure. Para usar a conta de armazenamento de outras assinaturas, selecione **Chave de acesso** e, em seguida, forneça a chave de acesso dessa conta. |
    |**Criar uma nova conta de armazenamento**     | Forneça um nome para a conta de armazenamento.|

    Aceite todos os outros valores padrão e selecione **Avançar**.

5. Na guia **Resumo**, verifique os valores que você selecionou nas etapas anteriores.

    ![HDInsight para Linux - introdução ao resumo do cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-summary.png "HDInsight para Linux - introdução ao resumo do cluster")
      
4. Selecione **Criar**. Você verá um novo bloco chamado **Enviando a implantação para HDInsight** no painel do portal. Demora cerca de 20 minutos para criar um cluster.

    ![Grupo de recursos de introdução ao HDInsight para Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/deployment-progress-tile.png "Grupo de recursos de cluster do Azure HDInsight")

4. Após a criação do cluster, você verá a página de visão geral do cluster no Portal do Azure.
   
    ![Configurações de cluster de introdução do HDInsight para Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "Propriedades do cluster do HDInsight")    
    
    Cada cluster tem uma dependência na [conta de Armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou [conta do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Ela é conhecida como a conta de armazenamento padrão. O cluster HDInsight e sua conta de armazenamento padrão devem estar colocalizados na mesma região do Azure. A exclusão dos clusters não exclui a conta de armazenamento.

    > [!NOTE]
    > Para obter outros métodos de criação de cluster e Noções básicas sobre as propriedades usadas neste tutorial, confira [Criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).       
    > 
    >

## <a name="run-hive-queries"></a>Execute consultas Hive

[Apache Hive](hdinsight-use-hive.md) é o componente mais popular usado no HDInsight. Há várias maneiras de executar trabalhos do Hive no HDInsight. Neste tutorial, você usará o modo de exibição do Ambari Hive no portal. Para obter outros métodos para enviar trabalhos do Hive, confira [Usar o Hive no HDInsight](hdinsight-use-hive.md).

1. Para abrir o Ambari, na captura de tela anterior, selecione **Painel do Cluster**.  Você também pode navegar até **https://&lt;NomeDoCluster>.azurehdinsight.net**, em que &lt;NomeDoCluster> é o cluster que você criou na seção anterior.

    ![HDInsight para Linux - introdução ao painel do cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight para Linux - introdução ao painel do cluster")

2. Insira o nome de usuário e a senha do Hadoop que você especificou durante a criação do cluster. O nome de usuário padrão é **admin**.

3. Abra a **Exibição do Hive** , conforme mostrado na seguinte captura de tela:
   
    ![Selecionar modos de exibição do Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu do Visualizador do Hive no HDInsight")

4. Na guia **CONSULTA**, cole as seguintes instruções HiveQL na planilha:
   
        SHOW TABLES;

    ![Modos de exibição de Hive do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor de consulta de modo de exibição de Hive do HDInsight")
   
   > [!NOTE]
   > O ponto-e-vírgula é obrigatório para o Hive.       
   > 
   > 

5. Selecione **Executar**. Uma guia **RESULTADOS** aparece abaixo da guia **CONSULTA** e exibe informações sobre o trabalho. 
   
    Após a conclusão da consulta, a guia **CONSULTA** exibirá os resultados da operação. Você deverá ver uma tabela chamada **hivesampletable**. Essa tabela do Hive de exemplo é fornecida com todos os clusters HDInsight.
   
    ![Modos de exibição de Hive do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor de consulta de modo de exibição de Hive do HDInsight")

6. Repita as etapas 4 e 5 para executar a seguinte consulta:
   
        SELECT * FROM hivesampletable;
   
7. Você também pode salvar os resultados da consulta. Selecione o botão de menu à direita e especifique se deseja baixar os resultados como um arquivo CSV ou armazená-los na conta de armazenamento associada ao cluster.

    ![Salvar resultado da consulta de Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Salvar resultado da consulta de Hive")

Depois de concluir um trabalho do Hive, você pode [exportar os resultados para o banco de dados SQL Azure ou do SQL Server](apache-hadoop-use-sqoop-mac-linux.md). Também pode [visualizar os resultados usando o Excel](apache-hadoop-connect-excel-power-query.md). Para obter mais informações sobre como usar o Hive no HDInsight, confira [Usar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um arquivo log4j do Apache de exemplo](hdinsight-use-hive.md).

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="clean-up-resources"></a>Limpar recursos
Após concluir o tutorial, convém excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. 

> [!NOTE]
> Se você for prosseguir *imediatamente* no próximo tutorial para saber como executar operações de ETL usando Hadoop no HDInsight, convém manter o cluster em execução. Isso porque, no tutorial, você precisa criar um cluster Hadoop novamente. No entanto, se você não for conferir o próximo tutorial imediatamente, exclua o cluster agora.
> 
>  

**Para excluir o cluster e/ou a conta de armazenamento padrão**

1. Volte para a guia do navegador onde você tem o portal do Azure. Você deve estar na página de visão geral do cluster. Se você quiser apenas excluir o cluster, mas manter a conta de armazenamento padrão, selecione **Excluir**.

    ![Excluir o cluster HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Excluir o cluster HDInsight")

2. Se você quiser excluir o cluster, bem como a conta de armazenamento padrão, selecione o nome do grupo de recursos (realçado na captura de tela anterior) para abrir a página do grupo de recursos.

3. Selecione **Excluir grupo de recursos** para excluir o grupo de recursos, que contém o cluster e a conta de armazenamento padrão. Observe que a exclusão do grupo de recursos exclui a conta de armazenamento. Para manter a conta de armazenamento, exclua apenas o cluster.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar um cluster HDInsight baseado em Linux usando um modelo do Resource Manager e como executar consultas básicas do Hive. No próximo artigo, saiba como executar uma operação de ETL (extração, transformação e carregamento) usando o Hadoop no HDInsight.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados usando o Apache Hive no HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)

Se você estiver pronto para começar a trabalhar com seus próprios dados e precisa saber mais sobre como o HDInsight armazena dados ou obtém dados no HDInsight, consulte os seguintes artigos:

* Para saber mais sobre como o HDInsight usa o Armazenamento do Azure, veja [Usar Armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como criar um cluster HDInsight com o Data Lake Storage, consulte o [Guia de início rápido: configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* Para obter informações sobre como carregar arquivos no HDInsight, consulte [Carregar dados no HDInsight](../hdinsight-upload-data.md).

Para saber mais sobre como analisar dados com o HDInsight, consulte os seguintes artigos:

* Para saber mais sobre como usar o Hive com HDInsight, inclusive como executar consultas de Hive do Visual Studio, consulte [Usar o Hive com HDInsight](hdinsight-use-hive.md).
* Para saber mais sobre o Pig, uma linguagem usada para transformar dados, consulte [Usar o Pig com o HDInsight](hdinsight-use-pig.md).
* Para saber mais sobre o MapReduce, uma maneira de gravar programas que processam dados no Hadoop, consulte [Usar o MapReduce com HDInsight](hdinsight-use-mapreduce.md).
* Para saber mais sobre como usar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Introdução às ferramentas do Hadoop do Visual Studio para o HDInsight](apache-hadoop-visual-studio-tools-get-started.md).


Se você quiser saber mais sobre como criar ou gerenciar um cluster HDInsight, consulte os seguintes artigos:

* Para saber mais sobre como gerenciar o cluster HDInsight baseado em Linux, consulte [Gerenciar clusters HDInsight usando o Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que você pode selecionar ao criar um cluster HDInsight, confira [Como criar o HDInsight no Linux usando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


