---
title: Apenas usando o Azure Data Factory para copiar os arquivos novos e alterados de maneira incremental com base em LastModifiedDate | Microsoft Docs
description: Crie um Azure data factory e, em seguida, use a ferramenta Copy Data para carregar incrementalmente os novos arquivos apenas com base em LastModifiedDate.
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
ms.openlocfilehash: e5e184e6dadfa993e0b758ad32b54ea6ccfcb272
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546527"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copiar incrementalmente os arquivos novos e alterados com base em LastModifiedDate usando a ferramenta Copy Data

Neste tutorial, você pode usar o portal do Azure para criar um Data Factory. Em seguida, você deve usar a ferramenta Copy Data para criar um pipeline que incrementalmente copia arquivos novos e alterados somente com base em sua "LastModifiedDate" do armazenamento de BLOBs do Azure para o armazenamento de BLOBs do Azure. 

> [!NOTE]
> Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**: Usar o armazenamento de Blob como o _fonte_ e _coletor_ armazenamento de dados. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Crie dois contêineres no armazenamento de Blob

Prepare seu armazenamento de BLOBs para o tutorial executando as seguintes etapas.

1. Crie um contêiner denominado **fonte**. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).

2. Crie um contêiner denominado **destino**. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **+ Novo** > **Dados + Análise** > **Data Factory**: 
   
   ![Criação do novo data factory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**. 
      
     ![Novo data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   O nome do seu data factory deve ser _globalmente exclusivo_. Você deve ver a seguinte mensagem de erro:
   
   ![Mensagem de erro do novo data factory](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione a **assinatura** do Azure na qual deseja criar o novo data factory. 
4. Em **Grupo de Recursos**, use uma das seguintes etapas:
     
     a. Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).

5. Em **versão**, selecione **V2** para a versão.
6. Em **local**, selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo seu data factory podem estar em outros locais e regiões.
7. Selecione **Fixar no painel**. 
8. Selecione **Criar**.
9. No painel, o bloco **Implantando o Data Factory** mostra o status do processo.

    ![Bloco Como implantar o Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Depois de finalizada a criação, a home page do **Data Factory** é exibida.
   
    ![Página inicial do data factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Para iniciar a interface do usuário do Azure Data Factory em uma guia separada, selecione o bloco **Criar e Monitorar**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Sobre o **vamos começar** página, selecione o **copiar dados** title para iniciar a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Sobre o **propriedades** página, execute as seguintes etapas:

     a. Sob **nome da tarefa**, insira **DeltaCopyFromBlobPipeline**.

    b. Sob **cadência de tarefa ou agendamento de tarefa**, selecione **executados regularmente em programação**.

    c. Sob **o tipo de disparador**, selecione **janela em cascata**.
    
    d. Sob **recorrência**, insira **minuto (s 15)**. 
    
    e. Selecione **Avançar**. 
    
    A interface do usuário do Data Factory cria um pipeline com o nome especificado da tarefa. 

    ![Página Propriedades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

     a. Clique em **+ criar nova conexão**, para adicionar uma conexão.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selecione **armazenamento de BLOBs do Azure** na galeria e, em seguida, clique **continuar**.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Sobre o **novo serviço vinculado** , selecione sua conta de armazenamento do **nome da conta de armazenamento** e, em seguida, clique **concluir**.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selecione o serviço vinculado criado recentemente, clique **próxima**. 
    
   ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolher o arquivo ou a pasta de entrada**, execute as etapas a seguir:
    
     a. Procure e selecione o **fonte** pasta, em seguida, clique em **escolha**.
    
    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Sob **comportamento de carregamento de arquivo**, selecione **carga Incremental: LastModifiedDate**.
    
    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Verifique **cópia binária** e clique em **próxima**.
    
     ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Sobre o **armazenamento de dados de destino** página, selecione o **AzureBlobStorage** que é o mesmo armazenamento de conta como o armazenamento de dados de origem e, em seguida, clique em **próxima**.

    ![Página Armazenamento de dados de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Sobre o **escolha o arquivo de saída ou a pasta** página, faça as seguintes etapas:
    
     a. Procure e selecione o **destino** pasta, em seguida, clique em **escolha**.
    
    ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Clique em **Avançar**.
    
     ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na página **Configurações**, selecione **Avançar**. 

    ![Página Configurações](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Na página **Resumo**, analise as configurações e selecione **Avançar**.

    ![Página Resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa).

    ![Página Implantação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente. Selecione **Refresh** para atualizar a lista e, em seguida, selecione o **exibir execuções de atividade** link no **ações** coluna. 

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (atividade de cópia) no pipeline. Assim, você vê apenas uma entrada. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. 

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Considerando que não há nenhum arquivo na **fonte** contêiner na sua conta de armazenamento de blob, portanto, você não verá qualquer arquivo que foi copiado para **destino** contêiner na sua conta de armazenamento de blob.
    
    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Crie um arquivo de texto vazio e nomeie-o como file1.txt. Carregue o arquivo file1.txt para o **origem** contêiner em sua conta de armazenamento. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Para voltar para o **as execuções de Pipeline** exibição, selecione **todas as execuções de Pipelines**e aguarde até que o mesmo canal que está sendo disparado novamente automaticamente.  

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Selecione **modo de exibição de execução da atividade** para a segunda se execução do pipeline verá ele vem e fazer o mesmo para examinar os detalhes.  

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Você verá um arquivo (file1.txt) tiver sido copiado do **fonte** contêiner para o **destino** contêiner de sua conta de armazenamento.
    
    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Crie outro arquivo de texto vazio e nomeie-o como file2.txt. Carregue o arquivo file2.txt para o **origem** contêiner em sua conta de armazenamento. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).  
    
16. Fazer o mesmo que a etapa 13 e 14, e você verá somente o novo arquivo (file2.txt) tiver sido copiado do **fonte** contêiner para o **destino** contêiner de sua conta de armazenamento na próxima execução do pipeline.  
    
    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Você também pode verificar o mesmo usando o Gerenciador de armazenamento do Azure (https://storageexplorer.com/) para examinar os arquivos.
    
    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Próximas etapas
Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados usando o cluster Spark na nuvem](tutorial-transform-data-spark-portal.md)