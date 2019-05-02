---
title: Copiar incrementalmente os arquivos novos e alterados com base em LastModifiedDate usando a ferramenta Copy Data | Microsoft Docs
description: Crie um Azure data factory e, em seguida, use a ferramenta Copy Data para carregar incrementalmente os novos arquivos com base em LastModifiedDate.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: 8308190e0e68365343fb50ca33f9bea75c3e4e66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61098293"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copiar incrementalmente os arquivos novos e alterados com base em LastModifiedDate usando a ferramenta Copy Data

Neste tutorial, você usará o portal do Azure para criar um data factory. Em seguida, você usará a ferramenta Copy Data para criar um pipeline que incrementalmente copia arquivos novos e alterados apenas, com base em suas **LastModifiedDate** do armazenamento de BLOBs do Azure para armazenamento de BLOBs do Azure.

> [!NOTE]
> Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executará as seguintes tarefas:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**: Usar o armazenamento de Blob como o _fonte_ e _coletor_ armazenamento de dados. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Crie dois contêineres no armazenamento de Blob

Prepare seu armazenamento de BLOBs para o tutorial executando as seguintes etapas.

1. Crie um contêiner denominado **fonte**. Você pode usar várias ferramentas para executar essa tarefa, como [Azure Storage Explorer](https://storageexplorer.com/).

2. Crie um contêiner denominado **destino**. 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**: 
   
   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**. 
      
     ![Novo data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   O nome do seu data factory deve ser _globalmente exclusivo_. Você deve ver a seguinte mensagem de erro:
   
   ![Mensagem de erro do novo data factory](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione o Azure **assinatura** no qual você criará o novo data factory. 
4. Em **Grupo de Recursos**, use uma das seguintes etapas:
     
    * Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    * Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).

5. Sob **versão**, selecione **V2**.
6. Em **local**, selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, o armazenamento do Azure e o banco de dados SQL) e serviços de computação (por exemplo, o Azure HDInsight) que usa seu data factory podem ser em outros locais e regiões.
7. Selecione **Fixar no painel**. 
8. Selecione **Criar**.
9. No painel, consulte o **Implantando o Data Factory** lado a lado para ver o status do processo.

    ![Implantando bloco Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Depois de finalizada a criação, a home page do **Data Factory** é exibida.
   
    ![Página inicial do data factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Para abrir a interface do usuário (IU) do Azure Data Factory em uma guia separada, selecione o **autor & Monitor** lado a lado. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Sobre o **vamos começar** página, selecione o **copiar dados** título para abrir a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Sobre o **propriedades** página, execute as seguintes etapas:

     a. Sob **nome da tarefa**, insira **DeltaCopyFromBlobPipeline**.

    b. Sob **cadência de tarefa** ou **agendamento de tarefa**, selecione **executados regularmente em programação**.

    c. Sob **tipo de gatilho**, selecione **janela em cascata**.
    
    d. Sob **recorrência**, insira **minuto (s 15)**. 
    
    e. Selecione **Avançar**. 
    
    A interface do usuário do Data Factory cria um pipeline com o nome especificado da tarefa. 

    ![Página Propriedades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

     a. Selecione **+ criar nova conexão**, para adicionar uma conexão.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selecione **armazenamento de BLOBs do Azure** na galeria e, em seguida, selecione **continuar**.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Sobre o **novo serviço vinculado** , selecione sua conta de armazenamento do **nome da conta de armazenamento** lista e, em seguida, selecione **concluir**.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selecione o serviço vinculado criado recentemente e, em seguida, selecione **próxima**. 
    
   ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:
    
     a. Procure e selecione o **fonte** pasta e, em seguida, selecione **escolha**.
    
    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Sob **comportamento de carregamento de arquivo**, selecione **carga Incremental: LastModifiedDate**.
    
    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Verifique **cópia binária** e selecione **próxima**.
    
     ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Sobre o **armazenamento de dados de destino** página, selecione **AzureBlobStorage**. Isso é a mesma conta de armazenamento como o armazenamento de dados de origem. Em seguida, selecione **Avançar**.

    ![Página Armazenamento de dados de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na página **Escolha o arquivo de saída ou a pasta**, execute as seguintes etapas:
    
     a. Procure e selecione o **destino** pasta e, em seguida, selecione **escolha**.
    
    ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Selecione **Avançar**.
    
     ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na página **Configurações**, selecione **Avançar**. 

    ![Página Configurações](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Sobre o **resumo** página, examine as configurações e, em seguida, selecione **próxima**.

    ![Página Resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa).

    ![Página Implantação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente. Selecione **Refresh** para atualizar a lista e, em seguida, selecione o **exibir execuções de atividade** link no **ações** coluna. 

    ![Atualize a lista e selecione Exibir execuções de atividade](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (atividade de cópia) no pipeline, então você vê apenas uma entrada. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. 

    ![Atividade de cópia está no pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Porque não há nenhum arquivo na **fonte** contêiner na sua conta de armazenamento de Blob, você não verá nenhum arquivo copiado para o **destino** contêiner na sua conta de armazenamento de Blob.
    
    ![Nenhum arquivo no contêiner de origem ou destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Crie um arquivo de texto vazio e denomine **file1.txt**. Carregue esse arquivo de texto para o **origem** contêiner em sua conta de armazenamento. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Criar file1.txt e carregar no contêiner de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Para voltar para o **as execuções de Pipeline** exibição, selecione **todas as execuções de Pipeline**e aguarde até que o mesmo canal a ser disparada novamente automaticamente.  

    ![Selecione todas as execuções de Pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Selecione **modo de exibição de execução da atividade** para o segundo pipeline executado quando você vê-lo. Em seguida, examine os detalhes da mesma forma que você fez para a primeira execução do pipeline.  

    ![Selecione o modo de exibição de execução da atividade e analisar os detalhes](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Você irá consulte um arquivo (file1.txt) tiver sido copiado do **fonte** contêiner para o **destino** recipiente da sua conta de armazenamento de BLOBs.
    
    ![File1.txt foram copiados do contêiner de origem para o contêiner de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Crie outro arquivo de texto vazio e denomine **file2.txt**. Carregue esse arquivo de texto para o **origem** contêiner na sua conta de armazenamento de Blob.   
    
16. Repita as etapas 13 e 14 para esse segundo arquivo de texto. Você verá que somente o novo arquivo (file2.txt) foi copiado do **fonte** contêiner para o **destino** contêiner de sua conta de armazenamento na próxima execução do pipeline.  
    
    ![File2.txt foram copiados do contêiner de origem para o contêiner de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Você também pode verificar isso usando [Azure Storage Explorer](https://storageexplorer.com/) para examinar os arquivos.
    
    ![Verificar os arquivos usando o Gerenciador de armazenamento do Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Próximas etapas
Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Apache Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados na nuvem usando um cluster Apache Spark](tutorial-transform-data-spark-portal.md)