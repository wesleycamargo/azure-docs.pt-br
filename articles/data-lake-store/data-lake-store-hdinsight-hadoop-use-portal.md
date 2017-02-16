---
title: Criar clusters HDInsight com o Azure Data Lake Store usando o portal | Microsoft Docs
description: "Usar o Portal do Azure para criar e usar clusters HDInsight com o Repositório Azure Data Lake"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 3a9a4df2d260e8eebd8621b22efdb2927e9f5ecf
ms.openlocfilehash: a4fb47f9f517d66cf0ff9fde039d7bfd8edc29eb


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>Criar um cluster HDInsight com o Repositório Data Lake usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usando o PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usando o Gerenciador de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o Portal do Azure para criar um cluster HDInsight com acesso ao Azure Data Lake Store. Para tipos de cluster compatíveis, o Data Lake Store pode ser usado como um armazenamento padrão ou uma conta de armazenamento adicional. Quando o Data Lake Store é usado como armazenamento adicional, a conta de armazenamento padrão para os clusters ainda será Blobs de Armazenamento do Azure (WASB) e os arquivos relacionados ao cluster (como logs, etc.) ainda serão gravados no armazenamento padrão, embora os dados que você queira processar possam ser armazenados em uma conta do Data Lake Store. O uso do Repositório Data Lake como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de leitura/gravação no armazenamento do cluster.

Algumas considerações importantes:

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento padrão está disponível para o HDInsight versão 3.5.

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento adicional está disponível para o HDInsight versões 3.2, 3.4 e 3.5.

* Para clusters HBase (Windows e Linux), o Data Lake Store **não é compatível** como uma opção de armazenamento, tanto para armazenamento padrão quanto para armazenamento adicional.

* Para clusters Storm (Windows e Linux), o Data Lake Store pode ser usado para gravar dados de uma topologia do Storm. O Repositório Data Lake também pode ser usado para armazenar dados de referência que podem ser lidos por uma topologia do Storm. Para saber mais, confira [Usar o Repositório Data Lake em uma topologia do Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Repositório Azure Data Lake**. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md).

* **Entidade de serviço do Azure Active Directory**. As etapas neste tutorial fornecem instruções sobre como criar uma entidade de serviço no Azure AD. No entanto, você deve ser administrador do Azure AD para poder criar uma entidade de serviço. Se você for administrador do Azure AD, poderá ignorar esse pré-requisito e continuar com o tutorial.

    **Se você não for um administrador do Azure AD**, não poderá executar as etapas necessárias para criar uma entidade de serviço. Nesse caso, o administrador do Azure AD deverá primeiro criar uma entidade de serviço antes de criar um cluster HDInsight com Data Lake Store. Além disso, a entidade de serviço deve ser criada usando um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="do-you-learn-faster-with-videos"></a>Você aprende mais rapidamente com vídeos?
Assista aos vídeos a seguir para entender como provisionar clusters HDInsight com acesso ao Repositório Data Lake.

* [Criar um cluster HDInsight com acesso ao Repositório Data Lake](https://mix.office.com/watch/l93xri2yhtp2)
* Depois que o cluster for configurado, [Acesse dados no Repositório Data Lake usando scripts do Hive e Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>Criar um cluster HDInsight com acesso ao repositório Azure Data Lake
Nesta seção, você cria um cluster HDInsight Hadoop que usa o Repositório Data Lake como um armazenamento adicional. Nesta versão, para um cluster Hadoop, o Repositório Data Lake pode ser usado apenas como um armazenamento adicional para o cluster. O armazenamento padrão ainda será nos blobs de armazenamento do Azure (WASB). Portanto, vamos criar primeiro a conta de armazenamento e os contêineres de armazenamento exigidos para o cluster.

1. Entre no novo [Portal do Azure](https://portal.azure.com).

2. Siga as etapas em [Criar clusters de Hadoop no HDInsight](../hdinsight/hdinsight-provision-clusters.md) para começar a provisionar um cluster HDInsight.

3. Na folha **Fonte de Dados**, especifique se deseja o Armazenamento do Azure (WASB) ou Data Lake Store como seu armazenamento padrão. Se quiser usar o Azure Data Lake Store como armazenamento padrão, pule para a próxima etapa. 

    Se quiser usar os Blobs de Armazenamento do Azure como armazenamento padrão, para **Tipo de Armazenamento Primário**, clique em **Armazenamento do Azure**. Especifique os detalhes da conta de armazenamento e do contêiner de armazenamento, especifique **Local** como **Leste dos EUA 2** e clique em **Identidade AAD do Cluster**.
    
    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Add service principal to HDInsight cluster")


4. Se quiser usar o Azure Data Lake Store como armazenamento padrão, para **Tipo de Armazenamento Primário**, clique em **Data Lake Store**. Selecione uma conta do Data Lake Store que já existe, forneça um caminho de pasta raiz em que os arquivos específicos do cluster serão armazenados (veja nota abaixo), especifique **Local** como **Leste dos EUA 2** e clique em **Identidade AAD do Cluster**. Você pode usar essa opção somente com clusters HDInsight 3.5. Em clusters HDInsight 3.5, essa opção não está disponível para o tipo de cluster HBase.

    Na captura de tela abaixo, o caminho da pasta raiz é /clusters/myhdiadlcluster, onde **myhdiadlcluster** é o nome do cluster que está sendo criado. Nesse caso, verifique se a pasta **/clusters** já existe na conta do Data Lake Store. A pasta **myhdiadlcluster** será criada durante a criação do cluster. De modo semelhante, se o caminho raiz foi definido como /hdinsight/clusters/data/myhdiadlcluter, você deverá garantir que **/hdinsight/clusters/data/** já exista na conta do Data Lake Store.
        
    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Add service principal to HDInsight cluster")

5. Na folha **Identidade do AAD do Cluster** , é possível selecionar uma Entidade de Serviço existente ou criar uma novo. Se você quiser usar uma entidade de serviço existente, pule para a próxima etapa.

    Se quiser criar uma nova Entidade de Serviço, na folha **Identidade AAD do Cluster**, clique em **Criar novo**, clique em **Entidade de Serviço** e, na folha **Criar uma Entidade de Serviço**, forneça valores para criar uma nova entidade de serviço. Como parte disso, um certificado e um aplicativo do Active Directory do Azure também é criado. Clique em **Criar**.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Add service principal to HDInsight cluster")

    Você também pode clicar em **Baixar Certificado** para baixar o certificado associado à entidade de serviço que criou. Isso é útil se você quiser usar a mesma entidade de serviço no futuro, ao criar clusters HDInsight adicionais. Clique em **Selecionar**.

6. Se quiser usar uma Entidade de Serviço existente, na folha **Identidade AAD do Cluster**, clique em **Usar existente**, clique em **Entidade de Serviço** e, na folha **Selecionar uma Entidade de Serviço**, procure uma entidade de serviço existente. Clique em um nome de entidade de serviço e, em seguida, clique em **Selecionar**.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Add service principal to HDInsight cluster")

    Na folha **Identidade AAD do Cluster** , carregue o certificado (.pfx) associado à entidade de serviço selecionada e forneça a senha do certificado.

6. Na folha **Identidade AAD do Cluster**, clique em **Gerenciar Acesso de ADLS**. No próximo painel, **Selecione as permissões de arquivo** já está selecionada por padrão e todas as contas do Data Lake Store na sua assinatura são listadas. Clique na conta do Data Lake Store que quer associar ao cluster para listar os arquivos e as pastas dessa conta. Você pode atribuir permissões no nível de arquivo ou pasta. Se quiser associar as permissões no nível raiz da conta, marque a caixa de seleção ao lado do nome da conta.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Add service principal to HDInsight cluster")

    > [!NOTE]
    > Se estiver usando a conta do Data Lake Store como o armazenamento padrão para um cluster, você **deverá** atribuir as permissões à entidade de serviço no nível raiz da conta do Data Lake Store.

7. Se quiser atribuir permissões para arquivos ou pastas em uma conta, selecione a conta do Data Lake Store para ver os arquivos/pastas no próximo painel. Selecione os arquivos/pastas, selecione as permissões (LER/GRAVAR/EXECUTAR) que quer atribuir a eles, especifique se as permissões se aplicam recursivamente aos itens filho também e clique em **Selecionar**.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Add service principal to HDInsight cluster")

8. Na próxima tela, clique em **Executar** para atribuir as permissões para a entidade de serviço do Azure Active Directory na conta, no arquivo, na pasta selecionada.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Add service principal to HDInsight cluster")

9. Depois que as permissões forem atribuídas com êxito, clique em **Concluído** em todas as folhas até que esteja de volta à folha **Identidade AAD do Cluster**.

4. Clique em **Selecionar** na **Identidade AAD do Cluster** e continue com a criação de cluster, conforme descrito em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

10. Depois que o cluster for provisionado, você pode verificar que a Entidade de Serviço está associada com o cluster HDInsight. Para fazer isso, na folha do cluster, clique em **Identidade AAD do Cluster** para ver a Entidade de Serviço associada.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="show-me-some-examples"></a>Mostre-me alguns exemplos

Depois de ter provisionado o cluster com o Data Lake Store como armazenamento, veja alguns exemplos de como usar o cluster HDInsight para analisar os dados armazenados no Data Lake Store.

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store"></a>Executar uma consulta de Hive em dados armazenados no Data Lake Store

Para executar uma consulta de Hive, você pode usar a interface Modos de Exibição do Hive disponível no portal do Ambari. Para obter instruções sobre como usar os modos de exibição do Ambari Hive, veja [Usar o modo de exibição do Hive com o Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md). Há alguns itens que você precisará alterar ao trabalhar com dados no Data Lake Store.

* Se usar o exemplo do cluster que criamos acima, o caminho para os dados será `adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file`. Uma consulta do Hive para criar uma tabela com dados de exemplo armazenados na conta do Data Lake Store se parecerá com esta:

        CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Na consulta acima,

* `adl://hdiadlstorage.azuredatalakestore.net` é a raiz da conta do Data Lake Store.
* `/clusters/myhdiadlcluster` é a raiz para os dados do cluster que você especificou ao criar o cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é o local do arquivo de exemplo que você usa na consulta


### <a name="use-data-lake-store-with-spark-cluster"></a>Usar o Repositório Data Lake com o cluster Spark
Você pode usar um cluster Spark para executar trabalhos do Spark nos dados que são armazenados no Data Lake Store. Para obter instruções sobre isso, veja [Use HDInsight Spark cluster to analyze data in Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md) (Usar cluster HDInsight Spark para analisar dados no Data Lake Store).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Usar o Repositório Data Lake em uma topologia do Storm
Você pode usar o Repositório Data Lake para gravar os dados de uma topologia do Storm. Para obter instruções sobre como reproduzir esse cenário, confira [Usar o Azure Data Lake Store com o Apache Storm com HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Confira também
* [PowerShell: Criar um cluster HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx



<!--HONumber=Nov16_HO4-->


