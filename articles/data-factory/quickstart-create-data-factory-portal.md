---
title: "Criar um Azure Data Factory usando a interface do usuário do Azure Data Factory | Microsoft Docs"
description: Este tutorial mostra como criar um data factory com um pipeline que copia dados de uma pasta para outra no Armazenamento de Blobs do Azure.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: ebce4e0d137d2e56cc914efad357593af7abb255
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-data-factory-using-the-azure-data-factory-ui"></a>Criar um data factory usando a interface do usuário do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 – Versão prévia](quickstart-create-data-factory-portal.md)

Este guia de início rápido descreve como usar a interface do usuário do Azure Data Factory para criar e monitorar um data factory. O pipeline que você criar nesse data factory **copia** dados de uma pasta para outra em um Armazenamento de Blobs do Azure. Para obter um tutorial sobre como **transformar** dados usando o Azure Data Factory, consulte [Tutorial: transformar dados usando Spark](tutorial-transform-data-spark-portal.md). 


> [!NOTE]
> Se você é novo no Azure Data Factory, consulte [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de passar por este guia de início rápido. 
>
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do Data Factory, que está com disponibilidade geral (GA), consulte a [Data Factory versão 1 – tutorial](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Assistir a este vídeo o ajuda a entender a interface do usuário do Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Navegue até o [Portal do Azure](https://portal.azure.com). 
2. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** como o **nome**. 
      
     ![Página de novo data factory](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você se deparar com o seguinte erro para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Consulte o artigo [Data Factory – regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Nome não disponível – erro](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
    Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Apenas os locais com suporte do Data Factory são mostrados na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outros locais.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. Depois que a criação estiver concluída, você verá a página **Data Factory**, conforme mostrado na imagem.
   
    ![Página inicial da data factory](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário (IU) do Azure Data Factory em uma guia separada. 
11. Na página de introdução, alterne para a guia **Editar** no painel esquerdo, conforme mostrado na imagem a seguir: 

    ![Página Introdução](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-azure-storage-linked-service"></a>Criar o serviço vinculado do armazenamento do Azure
Nesta etapa, você criará um serviço vinculado para vincular sua Conta de Armazenamento do Azure ao data factory. O serviço vinculado tem as informações de conexão que o serviço do Data Factory usa no tempo de execução para se conectar a ele.

2. Clique em **Conexões**, depois clique no botão **Novo** na barra de ferramentas. 

    ![Nova conexão](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
3. Na página **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 

    ![Selecione Armazenamento do Azure](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
4. Na página **Novo Serviço Vinculado**, execute estas etapas: 

    1. Insira **AzureStorageLinkedService** para o **Nome**.
    2. Insira o nome da sua Conta de Armazenamento do Azure em **nome da conta de armazenamento**.
    3. Clique em **Testar conectividade** para confirmar se o serviço do Data Factory pode se conectar à conta de armazenamento. 
    4. Clique em **Salvar** para salvar o serviço vinculado. 

        ![Configurações do Serviço Vinculado do Armazenamento do Azure](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
5. Confirme que você vê **AzureStorageLinkedService** na lista de serviços vinculados. 

    ![Serviço vinculado do Armazenamento do Azure](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você criará dois conjuntos de dados: **InputDataset** e **OutputDataset**. Esses conjuntos de dados são do tipo **AzureBlob**. Eles se referem ao **Serviço vinculado do Armazenamento do Azure** que você criou na etapa anterior. 

O conjunto de dados de entrada representa os dados de origem na pasta de entrada. Na definição de conjunto de dados de entrada, especifique o contêiner de blob (**adftutorial**), a pasta (**entrada**) e o arquivo (**emp.txt**) que contém os dados de origem. 

Esse conjunto de dados de saída representa os dados que são copiados para o destino. Na definição de conjunto de dados de saída, especifique o contêiner de blob (**adftutorial**), a pasta (**saída**) e o arquivo para o qual os dados são copiados. Cada execução de um pipeline tem uma ID exclusiva associada a ele, que pode ser acessada usando a variável do sistema **RunId**. O nome do arquivo de saída é avaliado dinamicamente com base na ID de execução do pipeline.   

Nas configurações de serviço vinculado, você especificou a conta de armazenamento do Azure que contém os dados de origem. Nas configurações do conjunto de dados de origem, especifique onde exatamente os dados de origem residem (contêiner de blob, pasta e arquivo). Nas configurações de conjunto de dados do coletor, especifique para onde os dados são copiados (contêiner de blob, pasta e arquivo). 
 
1. Clique no botão **+ (adição)** e selecione **Conjunto de Dados**.

    ![Menu Novo conjunto de dados](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. Na página **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e clique em **Concluir**. 

    ![Selecionar Armazenamento de Blobs do Azure](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. Na janela **Propriedades** para o conjunto de dados, insira **InputDataset** para o **Nome**. 

    ![Configurações gerais do conjunto de dados](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. Alterne para a guia **Conexão** e execute as etapas a seguir: 

    1. Selecione **AzureStorageLinkedService** para o serviço vinculado. 
    2. Clique no botão **Procurar** para o **Caminho do arquivo**. 
        ![Procure o arquivo de entrada](./media/quickstart-create-data-factory-portal/file-path-browse-button.png)
    3. Na janela **Escolher um arquivo ou uma pasta**, navegue até a pasta **entrada** no contêiner **adftutorial**, selecione o arquivo **emp.txt** e clique em **Concluir**.

        ![Procure o arquivo de entrada](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    4. (opcional) Clique em **Visualizar dados** para visualizar os dados no arquivo emp.txt.     
5. Repita as etapas para criar o conjunto de dados de saída.  

    1. Clique no botão **+ (adição)** no painel esquerdo e selecione **Conjunto de Dados**.
    2. Na página **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e clique em **Concluir**.
    3. Especifique **OutputDataset** para o nome.
    4. Digite **adftutorial/saída** para a pasta. A Atividade de cópia cria a pasta de saída se ela não existir. 
    5. Insira `@CONCAT(pipeline().RunId, '.txt')` como o nome do arquivo. Cada vez que você executa um pipeline, essa execução tem uma ID exclusiva associada a ela. A expressão concatena a ID de execução do pipeline com **.txt** para avaliar o nome do arquivo de saída. Para obter a lista de expressões e variáveis do sistema com suporte, consulte [Variáveis de sistema](control-flow-system-variables.md) e [Linguagem de expressão](control-flow-expression-language-functions.md).

        ![Configurações conjunto de dados de saída](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>Criar uma pipeline 
Nesta etapa, você criará e validará um pipeline com uma atividade **Copiar** que usa o conjunto de dados de entrada e saída. A Atividade de cópia copia os dados do arquivo especificado nas configurações do conjunto de dados de entrada para o arquivo especificado nas configurações do conjunto de dados de saída. Se o conjunto de dados de entrada especifica apenas uma pasta (não o nome de arquivo), a Atividade de cópia copia todos os arquivos da pasta de origem para o destino. 

1. Clique no botão **+ (adição)** e selecione **Pipeline**. 

    ![Menu do novo pipeline](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. Especifique **CopyPipeline** para o **Nome** na janela **Propriedades**. 

    ![Configurações gerais do pipeline](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. Na caixa de ferramentas **Atividades**, expanda o **Fluxo de dados** e arraste e solte a atividade **Copiar** da caixa de ferramentas **Atividades** para a superfície de designer do pipeline. Você também pode pesquisar atividades na caixa de ferramentas **Atividades**. Especifique **CopyFromBlobToBlob** para o **Nome**.

    ![Configurações gerais da atividade de cópia](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. Alterne para a guia **Fonte** nas configurações da Atividade de cópia e selecione **InputDataset** para o **conjunto de dados de origem**.

    ![Configurações de origem da atividade de cópia](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. Alterne para a guia **Coletor** nas configurações da atividade de cópia e selecione **OutputDataset** para o **conjunto de dados do coletor**.

    ![Configurações do coletor de atividade](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. Clique em **Validar** para validar as configurações de pipeline. Confirme se esse pipeline foi validado com êxito. Para fechar a saída de validação, clique no botão **seta para a direita** (>>). 

    ![Validar o pipeline](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>Executar teste do pipeline
Nesta etapa, execute um teste do pipeline antes de implantar o Data Factory. 

1. Na barra de ferramentas do pipeline, clique em **Execução de teste**. 
    
    ![Execuções de teste do pipeline](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. Confirme se você vê o status da execução do pipeline na guia **Saída** das configurações do pipeline. 

    ![Saída da execução de teste](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. Confirme que você vê um arquivo de saída na pasta **saída** do contêiner **adftutorial**. Se a pasta de saída não existir, o serviço do Data Factory a cria automaticamente. 
    
    ![Verificar a saída](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente
Nesta etapa, implante entidades (serviços vinculados, conjuntos de dados, pipelines) ao Azure Data Factory. Depois, dispare manualmente a execução do pipeline. Você também pode publicar entidades em seu próprio repositório GIT do VSTS, que é abordado [outro tutorial](tutorial-copy-data-portal.md?#configure-code-repository).

1. Antes de disparar um pipeline, você deve publicar as entidades no Data Factory. Para publicar, clique em **Publicar** no painel esquerdo. 

    ![Botão Publicar](./media/quickstart-create-data-factory-portal/publish-button.png)
2. Para disparar o pipeline manualmente, clique em **Gatilho** na barra de ferramentas e selecione **Disparar Agora**. 
    
    ![Disparar agora](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Use o botão **Atualizar** para atualizar a lista.

    ![Monitorar o Pipeline](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Clique no link **Exibir Execuções de Atividade** em **Ações**. Você vê o status de execução da atividade de cópia nesta página. 

    ![Execuções de atividade de pipeline](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. Para exibir detalhes sobre a operação de cópia, clique no link **Detalhes** (imagem de óculos) na coluna **Ações**. Para obter detalhes sobre as propriedades, consulte [Visão geral da atividade de cópia](copy-activity-overview.md). 

    ![Detalhes da operação de cópia](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Confirme que você vê um arquivo novo na pasta de **saída**. 
5. Você pode alternar de volta para o modo de exibição **Execuções do pipeline** no modo de exibição **Execuções de Atividade** clicando no link **Pipelines**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Disparar o pipeline em um cronograma
Essa etapa é opcional neste tutorial. Você pode criar um **agendador de gatilho** para agendar a execução periódica do pipeline (por hora, diariamente, etc.). Nesta etapa, você cria um gatilho para ser executado a cada minuto até o datetime especificado como a data de término. 

1. Alterne para a guia **Editar**. 

    ![Mude para a guia Editar](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. Clique em **Gatilho** no menu e clique em **Novo/Editar**. 

    ![Novo menu de gatilho](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. Na página **Adicionar gatilhos**, clique em **Escolher gatilho...** e depois em **Novo**. 

    ![Adicionar gatilhos – novo gatilho](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. Na página **Novo gatilho**, no campo **Final**, selecione **Na Data**, especifique como hora de término alguns minutos após a hora atual e clique em **Aplicar**. Há um custo associado a cada execução de pipeline, então, especifique o a hora de término como apenas alguns minutos após a hora de início. Verifique se está como o mesmo dia. No entanto, verifique se há tempo suficiente para a execução do pipeline entre a hora da publicação e a hora de término. O gatilho só entra em vigor depois de você publicar a solução no Data Factory, e não ao salvar o gatilho na interface do usuário. 

    ![Configurações do gatilho](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. Marque a opção **Ativado** na página **Novo Gatilho** e clique em **Avançar** 

    ![Configurações de gatilho – botão Avançar](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. Na página **Novo Gatilho**, analise a mensagem de aviso e clique em **Concluir**.

    ![Configurações de gatilho – botão Concluir](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Clique em **Publicar** para publicar as alterações no Data Factory. 

    ![Botão Publicar](./media/quickstart-create-data-factory-portal/publish-2.png)
8. Alterne para a guia **Monitorar** à esquerda. Clique em **Atualizar** para atualizar a lista. Você verá que o pipeline é executado uma vez por minuto desde o momento da publicação até hora de término. Observe os valores na coluna **Disparado Por**. A execução do gatilho manual foi feita em uma etapa anterior (**Disparar agora**). 

    ![Monitorar execuções disparadas](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Clique na seta para baixo ao lado de **Execuções de Pipeline** para alternar para o modo de exibição **Execuções de Gatilho**. 

    ![Monitorar execuções de gatilho](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Confirme que um **arquivo de saída** é criado para cada execução de pipeline até o datetime especificado na pasta **saída**. 

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-portal.md) para saber mais sobre o uso do Data Factory em mais cenários. 