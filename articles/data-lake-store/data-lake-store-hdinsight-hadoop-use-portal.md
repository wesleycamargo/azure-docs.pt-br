---
title: Usar o portal do Azure para criar clusters Azure HDInsight com o Data Lake Store | Microsoft Docs
description: Usar o portal do Azure para criar e usar clusters HDInsight com o Azure Data Lake Store
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
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: d1d780eb2c635f60409396804c0b8b706e59127b
ms.lasthandoff: 03/07/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Criar clusters HDInsight com o Data Lake Store usando o portal do Azure
> [!div class="op_single_selector"]
> * [Usar o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usar o PowerShell (para armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usar o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Este artigo aborda o uso do portal do Azure para criar clusters HDInsight com acesso ao Azure Data Lake Store. Para tipos de cluster com suporte, é possível usar o Data Lake Store como armazenamento padrão ou como uma conta de armazenamento adicional. 

Ao usar o Data Lake Store como armazenamento adicional, a conta de armazenamento padrão dos clusters continua sendo o armazenamento de Blobs do Azure e os arquivos relacionados ao cluster (como logs) ainda são gravados no armazenamento padrão. No entanto, os dados que você deseja processar podem ser armazenados em uma conta do Data Lake Store. O uso do Data Lake Store como uma conta de armazenamento adicional não afeta o desempenho nem a capacidade de ler ou gravar do cluster para o armazenamento.

Aqui estão algumas considerações importantes para usar o HDInsight com o Data Lake Store:

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento padrão está disponível para o HDInsight versão 3.5.

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento padrão *não está disponível* para clusters HDInsight Premium.

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento adicional está disponível para o HDInsight versões 3.2, 3.4 e 3.5.

* Para clusters HBase (Windows e Linux), *não há suporte* para o Data Lake Store como uma opção de armazenamento para o armazenamento padrão ou para o adicional.

* Para clusters Storm (Windows e Linux), é possível usar o Data Lake Store para gravar dados de uma topologia do Storm. Também é possível usar o Data Lake Store para dados de referência que, em seguida, podem ser lidos por uma topologia do Storm. Para saber mais, confira [Usar o Repositório Data Lake em uma topologia do Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, verifique se você atendeu aos seguintes requisitos:

* **Uma assinatura do Azure**. Acesse [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Uma conta do repositório Azure Data Lake**. Siga as instruções em [Introdução ao Azure Data Lake Store usando o portal do Azure](data-lake-store-get-started-portal.md).

* **Uma entidade de serviço do Azure Active Directory**. Este tutorial fornece instruções sobre como criar uma entidade de serviço no Azure AD (Azure Active Directory). No entanto, para criar uma entidade de serviço, você deve ser um administrador do Azure AD. Se você for um administrador, ignore esse pré-requisito e continue com o tutorial.

 >[!NOTE]
 >Você poderá criar uma entidade de serviço somente se for um administrador do Azure AD. O administrador do Azure AD deverá criar uma entidade de serviço antes que você possa criar um cluster HDInsight com o Data Lake Store. Além disso, a entidade de serviço deve ser criada com um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>Criar um cluster HDInsight com acesso a um Data Lake Store
Nesta seção, você cria um cluster Hadoop HDInsight que usa o Data Lake Store como armazenamento adicional. Nesta versão, para um cluster Hadoop, é possível usar o Data Lake Store apenas como armazenamento adicional para o cluster. O armazenamento padrão continua sendo o armazenamento de Blobs do Azure. Portanto, primeiro você cria a conta de armazenamento e os contêineres de armazenamento necessários para o cluster.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Para começar a provisionar um cluster HDInsight, siga as instruções em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-provision-clusters.md).

3. Na folha **Armazenamento**, especifique se deseja usar o armazenamento de Blobs ou o Data Lake Store como o armazenamento padrão e, depois, siga um destes procedimentos:

 * Para usar o Data Lake Store como armazenamento padrão, vá para a etapa 4.

 * Para usar o armazenamento de Blobs como armazenamento padrão:

    a. Em **Tipo de armazenamento primário**, selecione **Armazenamento do Azure**.

    b. Em **Método de seleção**, siga um destes procedimentos:      * Para especificar uma conta de armazenamento que faz parte de sua assinatura do Azure, selecione **Minhas assinaturas** e, depois, selecione a conta de armazenamento.      * Para especificar uma conta de armazenamento que está fora de sua assinatura do Azure, selecione **Tecla de acesso** e, em seguida, forneça as informações da conta de armazenamento externa.

    c. Em **Contêiner padrão**, insira o nome do contêiner padrão sugerido pelo portal ou especifique seu próprio.

 Ao usar o armazenamento de Blobs como armazenamento padrão, ainda é possível usar o Data Lake Store como armazenamento adicional para o cluster. Para fazer isso, clique em **Acesso ao Data Lake Store** e vá para a etapa 5.

 ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Adicionar entidade de serviço ao cluster do HDInsight")

4. Para usar o Data Lake Store como armazenamento padrão:

 a. Em **Tipo de armazenamento primário**, clique em **Data Lake Store**.

 b. Selecione uma conta existente do Data Lake Store, insira um caminho de pasta raiz no qual os arquivos específicos ao cluster devem ser armazenados, especifique **Localização** como **Leste dos EUA 2** e clique em **Acesso ao Data Lake Store**.

 >[!NOTE]
 >Você somente pode usar essa opção com clusters do HDInsight 3.5 (Standard Edition). Em clusters HDInsight 3.5, essa opção não está disponível para o tipo de cluster HBase.

 Na captura de tela abaixo, o caminho da pasta raiz é /clusters/myhdiadlcluster, em que *myhdiadlcluster* é o nome do cluster que está sendo criado. Nesse caso, verifique se a pasta */clusters* existe na conta do Data Lake Store. A pasta *myhdiadlcluster* é criada durante a criação do cluster. De modo semelhante, se o caminho raiz foi definido como */hdinsight/clusters/data/myhdiadlcluster*, verifique se */hdinsight/clusters/data/* existe na conta do Data Lake Store.

 ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Adicionar entidade de serviço ao cluster do HDInsight")

5. Na folha **Acesso ao Data Lake Store**, siga um destes procedimentos:

 * Para usar uma entidade de serviço existente, vá para a etapa 6.
 * Para criar uma entidade de serviço:

    a. Na folha **Acesso ao Data Lake Store**, clique em **Criar novo** e, em seguida, em **Entidade de Serviço**.

    b. Na folha **Criar uma Entidade de Serviço**, insira os valores necessários.  

    c. Clique em **Criar**.  
    Um certificado e um aplicativo do Azure AD são criados automaticamente.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Adicionar entidade de serviço ao cluster do HDInsight")

    Você também pode clicar em **Baixar Certificado** para baixar o certificado associado à entidade de serviço que criou. É útil baixar o certificado se você desejar usar a mesma entidade de serviço ao criar clusters HDInsight adicionais. Clique em **Selecionar**.

6. Para usar uma entidade de serviço existente:

 a. Na folha **Acesso ao Data Lake Store**, clique em **Usar existente** e, em seguida, em **Entidade de Serviço**.

 b. Na folha **Selecionar uma Entidade de Serviço**, pesquise uma entidade de serviço existente. Clique na entidade de serviço que você deseja usar e, em seguida, clique em **Selecionar**.

 c. Na folha **Acesso ao Data Lake Store**, carregue o certificado (arquivo .pfx) associado à entidade de serviço selecionada e insira a senha do certificado.

 ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Adicionar entidade de serviço ao cluster do HDInsight")

7. Na folha **Acesso do Data Lake Store**, clique em **Acessar**.  
A folha **Selecionar permissões de arquivo** é aberta por padrão. Ela lista todas as contas do Data Lake Store na assinatura.

8. Selecione a conta do Data Lake Store que você deseja associar ao cluster.
 Todos os arquivos e todas as pastas nessa conta são listados. Você pode atribuir permissões no nível de arquivo ou pasta. Para atribuir permissões no nível raiz da conta, marque a caixa de seleção ao lado do nome da conta.

 ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Adicionar entidade de serviço ao cluster do HDInsight")

 > [!NOTE]
 > Se estiver usando a conta do Data Lake Store como armazenamento padrão para um cluster, atribua permissões à entidade de serviço no nível raiz da conta do Data Lake Store.

9. Para atribuir permissões para arquivos ou pastas em uma conta, na janela **Selecionar permissões de arquivo**, selecione a conta do Data Lake Store.

10. No painel direito, selecione os arquivos ou as pastas aos quais você deseja atribuir permissões, selecione as permissões (Ler, Gravar ou Executar), especifique se as permissões se aplicam recursivamente aos itens filho também e clique em **Selecionar**.

    ![Atribuir permissões da entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Atribuir permissões da entidade de serviço ao cluster HDInsight")

11. Na janela **Atribuir permissões selecionadas**, para atribuir as permissões da entidade de serviço do Azure Active Directory na conta, nos arquivos ou nas pastas selecionadas, clique em **Executar**.

    ![Atribuir permissões da entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Atribuir permissões da entidade de serviço ao cluster HDInsight")

12. Depois de atribuir as permissões com êxito, clique em **Concluído** em cada folha aberta para retornar à folha **Acesso ao Data Lake Store**.

13. Em **Acesso ao Data Lake Store**, clique em **Selecionar** e continue com a criação do cluster, conforme descrito em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

14. Após a conclusão da configuração do cluster, na folha do cluster, verifique os resultados seguindo um ou ambos os procedimentos:

 * Para verificar se o armazenamento associado do cluster é a conta do Data Lake Store especificada, clique em **Contas de armazenamento** no painel esquerdo.

        ![Add service principal to HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Add service principal to HDInsight cluster")

 * Para verificar se a entidade de serviço está associada ao cluster HDInsight corretamente, clique em **Acesso ao Data Lake Store** no painel esquerdo.

       ![Add service principal to HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="examples"></a>Exemplos

Depois de configurar o cluster com o Data Lake Store como o armazenamento, consulte estes exemplos de como usar o cluster HDInsight para analisar os dados armazenados no Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Executar uma consulta do Hive nos dados de um Data Lake Store (como armazenamento primário)

Para executar uma consulta do Hive, use a interface de exibições do Hive no portal do Ambari. Para obter instruções sobre como usar as exibições do Hive no Ambari, consulte [Usar a exibição do Hive com o Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md).

Ao trabalhar com os dados em um Data Lake Store, há algumas cadeias de caracteres a serem alteradas.

Se você usar, por exemplo, o cluster criado com o Data Lake Store como armazenamento primário, o caminho para os dados será: *adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file*. Uma consulta do Hive para criar uma tabela com base em dados de exemplo armazenados na conta do Data Lake Store é parecida com esta:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrições:
* `adl://hdiadlstorage.azuredatalakestore.net/` é a raiz da conta do Data Lake Store.
* `/clusters/myhdiadlcluster` é a raiz dos dados do cluster especificada durante a criação do cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é a localização do arquivo de exemplo usado na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Executar uma consulta do Hive nos dados de um Data Lake Store (como armazenamento adicional)

Se o cluster criado usar o armazenamento de Blobs como armazenamento padrão, os dados de exemplo não estarão contidos na conta do Azure Data Lake Store usada como armazenamento adicional. Nesse caso, primeiro transfira os dados do armazenamento de Blobs para o Data Lake Store e, em seguida, execute as consultas, conforme mostrado no exemplo anterior.

Para obter informações sobre como copiar dados do armazenamento de Blobs para um Data Lake Store, consulte os seguintes artigos:

* [Usar o Distcp para copiar dados entre os Azure Storage blobs e o Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Usar o AdlCopy para copiar dados de Azure Storage blobs para o Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Usar o Data Lake Store com o cluster Spark
É possível usar um cluster Spark para executar trabalhos do Spark nos dados armazenados em um Data Lake Store. Para obter mais informações, consulte [Usar um cluster Spark HDInsight para analisar dados no Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Usar o Repositório Data Lake em uma topologia do Storm
Você pode usar o Repositório Data Lake para gravar os dados de uma topologia do Storm. Para obter instruções sobre como reproduzir esse cenário, confira [Usar o Azure Data Lake Store com o Apache Storm com HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Confira também
* [PowerShell: Criar um cluster HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

