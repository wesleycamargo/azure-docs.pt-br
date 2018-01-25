---
title: Transformar dados usando o Spark no Azure Data Factory | Microsoft Docs
description: "Este tutorial fornece instruções passo a passo para transformar dados usando a Atividade Spark no Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: c2ec6706c92f229bb05ad9a19246c6ffe5f615c9
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformar os dados na nuvem usando a atividade Spark no Azure Data Factory
Neste tutorial, você usa o portal do Azure para criar um pipeline do Data Factory que transforma dados usando a Atividade Spark e um serviço vinculado HDInsight sob demanda. Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar um pipeline com uma atividade Spark
> * Disparar uma execução de pipeline
> * Monitore a execução de pipeline.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>pré-requisitos
* **Conta de Armazenamento do Azure**. Você cria um script Python e um arquivo de entrada e carrega-os no Armazenamento do Azure. A saída do programa Spark é armazenada nessa conta de armazenamento. O cluster do Spark sob demanda usa a mesma conta de armazenamento que o respectivo armazenamento primário.  
* **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Carregar o script Python em sua conta de Armazenamento de Blobs
1. Crie um arquivo de Python chamado **WordCount_Spark.py** com o seguinte conteúdo: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Substitua **&lt;storageAccountName&gt;** pelo nome da sua conta de Armazenamento do Azure. Em seguida, salve o arquivo. 
3. No seu Armazenamento de Blobs do Azure, crie um contêiner denominado **adftutorial** se ele não existir. 
4. Crie uma pasta chamada **spark**.
5. Criar uma subpasta chamada **script** na pasta **spark**. 
6. Carregue o arquivo **WordCount_Spark.py** na subpasta **script**. 


### <a name="upload-the-input-file"></a>Carregue o arquivo de entrada
1. Crie um arquivo chamado **minecraftstory.txt** com um pouco de texto. O programa Spark conta o número de palavras no texto. 
2. Criar uma subpasta chamada `inputfiles` na pasta `spark`. 
3. Carregue o `minecraftstory.txt` na subpasta `inputfiles`. 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Erro - nome não disponível](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Algumas das etapas neste guia de início rápido supõem que você usa o nome **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Atualmente, o Data Factory V2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
    ![Página inicial da data factory](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. Clique no bloco **Autor & Monitor** para iniciar o aplicativo IU do Data Factory em uma guia separada.

## <a name="create-linked-services"></a>Criar serviços vinculados
Você cria dois serviços vinculados nesta seção: 
    
- Um **serviço vinculado do Armazenamento do Azure** que vincula uma conta de Armazenamento do Azure ao data factory. Esse armazenamento é usado pelo cluster HDInsight sob demanda. Ele também contém o script Spark a ser executado. 
- Um **serviço vinculado do HDInsight sob demanda**. O Azure Data Factory cria automaticamente um cluster HDInsight, executa o programa Spark e então exclui o cluster HDInsight depois de ele ficar ocioso por um tempo pré-configurado. 

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento do Azure

1. Na página **introdução**, acesse a guia **Editar** no painel esquerdo, conforme mostrado na imagem a seguir: 

    ![Bloco Criar um pipeline](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Clique em **Conexões** na parte inferior da janela e, depois, clique em **+ Novo**. 

    ![Botão Nova conexão](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. Na janela **Novo serviço vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 

    ![Selecione Armazenamento de Blobs do Azure](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Selecione o **nome de conta de Armazenamento do Azure** e clique em **Salvar**. 

    ![Especifique a conta de armazenamento de Blobs](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Criar um serviço vinculado do HDInsight sob demanda

1. Clique no botão **+ Novo** novamente para criar outro serviço vinculado. 
2. Na janela **Novo serviço vinculado**, selecione **Azure HDInsight** e clique em **Continuar**. 

    ![Selecione Azure HDInsight](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. Na janela **Novo serviço vinculado** execute as seguintes etapas: 

    1. Insira **AzureHDInsightLinkedService** no **Nome**.
    2. Confirme se está selecionado o **HDInsight sob demanda** para o **Tipo**.
    3. Escolha **AzureStorage1** para o campo **Serviço Vinculado do Armazenamento do Azure**. Você criou esse serviço vinculado anteriormente. Se você usou um nome diferente, especifique o nome correto para esse campo. 
    4. Selecione **spark** para o campo **Tipo de cluster**.
    5. Insira o **ID da entidade de serviço** que tem permissão para criar um cluster do HDInsight. Essa entidade de serviço precisa ser um membro da função de Colaborador de assinatura ou o grupo de recursos em que o cluster é criado. Veja [criar o aplicativo do Azure Active Directory e a entidade de serviço](../azure-resource-manager/resource-group-create-service-principal-portal.md) para obter detalhes.
    6. Especificar a **Chave da entidade de serviço**. 
    7. Selecione o mesmo grupo de recursos que você usou ao criar o data factory para o campo **Grupo de recursos**. O cluster Spark é criado nesse grupo de recursos. 
    8. Expandir o **Tipo de sistema operacional**.
    9. Insira um **nome** para o **usuário** do cluster. 
    10. Insira a **senha** para o usuário. 
    11. Clique em **Salvar**. 

        ![Configurações de serviço vinculado ao HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> O Azure HDInsight tem uma limitação para o número total de núcleos que você pode usar em cada região do Azure a que ele dá suporte. Para o serviço vinculado do HDInsight sob demanda, o cluster HDInsight será criado na mesma localização do Armazenamento do Azure usado como o armazenamento primário desse serviço vinculado. Verifique se você tem cotas de núcleo suficientes para que o cluster seja criado com êxito. Para obter mais informações, consulte [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Criar uma pipeline

2. Clique no botão + (mais) e, depois, clique em **Pipeline** no menu.

    ![Menu de novo pipeline](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. Na caixa de ferramentas **Atividades**, expanda o **HDInsight** e arraste e solte a atividade **Spark** da caixa de ferramentas **Atividades** para a superfície de designer do pipeline. 

    ![Arraste e solte a atividade Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. Na parte inferior da janela de propriedades para a atividade **Spark**, execute as seguintes etapas: 

    1. acesse a guia **HDI Cluster**.
    2. Selecione **AzureHDInsightLinkedService** criado na etapa anterior. 
        
    ![Especifique um serviço vinculado HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. Acesse a guia **Script/Jar** e siga as seguintes etapas: 

    1. Selecione **AzureStorage1** no campo **Serviço Vinculado do Trabalho**.
    2. Clique em **Procurar armazenamento**. 
    3. Acesse a **pasta adftutorial/spark/script**, selecione **WordCount_Spark.py** e clique em **Concluir**.      

    ![Especifique o script Spark](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. Para validar o pipeline, clique em **Validar** na barra de ferramentas. Clique na **seta para a direita** (>>) para fechar a janela de validação. 
    
    ![Botão Validar](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. Clique em **Publicar**. A IU de Data Factory publica entidades (serviços vinculados e pipeline) para o serviço Azure Data Factory. 
    
    ![Botão Publicar](./media/tutorial-transform-data-spark-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline
Clique em **Gatilho** na barra de ferramentas e, depois, em **Gatilho agora**. 

![Disparar agora](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Alterne para a guia **Monitorar**. Verifique se o pipeline está sendo executado. Leva aproximadamente 20 minutos para criar um cluster Spark. 

    ![Monitorar execuções de pipeline](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. Clique em **Atualizar** periodicamente para verificar o status da execução do pipeline. 

    ![Status da execução do pipeline](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. Para ver as atividades em execução associadas com o pipeline de execução, clique em **Exibir atividades em execução** na coluna de ações. Você pode alternar novamente para o modo de execução do pipeline clicando no link **Pipelines** na parte superior.

    ![Exibir atividades em execução](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificar a saída
Verifique se o arquivo de saída é criado na pasta spark/outputfiles/wordcount no contêiner do adftutorial. 

![Verificar a saída](./media/tutorial-transform-data-spark-portal/verity-output.png)

O arquivo deve ter cada palavra do texto do arquivo de entrada e o número de vezes que a palavra apareceu no arquivo. Por exemplo:  

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, o pipeline transforma dados usando a atividade Spark e um serviço vinculado do HDInsight sob demanda. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar um pipeline com uma atividade Spark
> * Disparar uma execução de pipeline
> * Monitore a execução de pipeline.

Avance para o próximo tutorial para aprender como transformar dados executando o script Hive em um cluster do Azure HDInsight que está em uma rede virtual. 

> [!div class="nextstepaction"]
> [Tutorial: transformar dados usando o Hive na Rede Virtual do Azure](tutorial-transform-data-hive-virtual-network-portal.md).





