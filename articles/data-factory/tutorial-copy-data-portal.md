---
title: Usar o portal do Azure para criar um pipeline do Data Factory | Microsoft Docs
description: "Este tutorial fornece instruções passo a passo para usar um portal do Azure para criar um data factory com um pipeline. O pipeline usa a atividade de cópia para copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure. "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 8b5211e9c932221c6b6134e7e0627f4d7f964123
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copiar dados do Blob do Azure para o Banco de Dados SQL do Azure usando o Azure Data Factory
Neste tutorial, você criará um data factory ao usar a interface do usuário do Azure Data Factory. O pipeline neste data factory copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure. O padrão de configuração neste tutorial aplica-se a cópia de um armazenamento de dados baseado em arquivo para um armazenamento de dados relacional. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, consulte a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).
>
> - Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma Atividade de cópia.
> * Executar teste do pipeline
> * Disparar o pipeline manualmente
> * Disparar o pipeline em um cronograma
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>pré-requisitos
* **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o Armazenamento de Blobs como um armazenamento de dados de **origem**. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para conhecer as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de **coletor**. Se você não tiver um Banco de Dados SQL do Azure, veja o artigo [Criar um Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para conhecer as etapas para criar um.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu Blob do Azure e Banco de Dados SQL do Azure para o tutorial, executando as seguintes etapas:

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **emp.txt** no disco.

    ```
    John,Doe
    Jane,Doe
    ```

2. Crie um contêiner chamado **adftutorial** no seu Armazenamento de Blobs do Azure. Crie uma pasta chamada **input** nesse contêiner. Em seguida, carregue o arquivo **emp.txt** na pasta **input**. Use as ferramentas ou o portal do Azure, como [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) para realizar essas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

1. Use o script SQL a seguir para criar a tabela **dbo.emp** no seu Banco de Dados SQL do Azure.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Permita que os serviços do Azure acessem o SQL Server. Certifique-se de a configuração **Permitir acesso aos serviços do Azure** esteja definida como **ATIVADA** para o Azure SQL Server, permitindo que nele sejam gravados dados pelo serviço Data Factory. Para verificar e ativar essa configuração, faça as seguintes etapas:

    1. Clique no hub **Mais serviços** à esquerda e clique em **Servidores SQL**.
    2. Selecione seu servidor e clique em **Firewall** em **CONFIGURAÇÕES**.
    3. Na página **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.

## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você criará um data factory e iniciará a interface do usuário do Azure Data Factory para criar um pipeline no data factory. 

1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Página de novo data factory](./media/tutorial-copy-data-portal/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
        Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.      
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada.

## <a name="create-a-pipeline"></a>Criar uma pipeline
Nesta etapa, você criará um pipeline com a Atividade de cópia no data factory. A Atividade de cópia copia dados do Armazenamento de Blobs do Azure para o Banco de dados SQL do Azure. No [Tutorial de início rápido](quickstart-create-data-factory-portal.md), você criou um pipeline seguindo estas etapas:

1. Criar o serviço vinculado. 
2. Criar conjuntos de dados de entrada e saída.
3. Depois, criar um pipeline.

Neste tutorial, você iniciará com a criação do pipeline e depois criará serviços vinculados e conjuntos de dados quando precisar deles para configurar o pipeline. 

1. Na página de introdução, clique no bloco **Criar Pipeline**. 

   ![Bloco Criar pipeline](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. Na janela **Propriedades** do pipeline, defina o **nome** do pipeline como **CopyPipeline**.

    ![Nome do pipeline](./media/tutorial-copy-data-portal/pipeline-name.png)
4. Na caixa de ferramentas de **Atividades**, expanda a categoria **DataFlow** e arraste e solte a atividade **Copiar** da caixa de ferramentas para a superfície do designer do pipeline. 

    ![Arrastar e soltar a atividade de cópia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. Na guia **Geral** da janela **Propriedades**, especifique **CopyFromBlobToSql** como o nome da atividade.

    ![Nome da atividade](./media/tutorial-copy-data-portal/activity-name.png)
6. Alterne para a guia **Origem**. Clique em **+ Novo** para criar um conjunto de dados de origem. 

    ![Novo menu do conjunto de dados de origem](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. Na janela **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e clique em **Concluir**. Os dados de origem estão em um armazenamento de blobs do Azure. Assim, você deve selecionar o armazenamento de blobs do Azure para o conjunto de dados de origem. 

    ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. Uma nova **guia** será aberta no aplicativo com o título **AzureBlob1**.

    ![Guia Blob1 do Azure ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. Na guia **Geral**, na janela **Propriedades** , na parte inferior, especifique **SourceBlobDataset** como o **nome**.

    ![Nome do conjunto de dados](./media/tutorial-copy-data-portal/dataset-name.png)
10. Alterne para a guia **Conexão** na janela Propriedades. Clique em **+ Novo**, ao lado da caixa de texto **Serviço vinculado**. Um serviço vinculado vincula um armazenamento de dados ou uma computação ao data factory. Nesse caso, você criou um serviço vinculado do armazenamento do Azure para vincular sua conta de armazenamento do Azure para o armazenamento de dados. O serviço vinculado tem as informações de conexão que os serviços do Data Factory usam para se conectar ao seu armazenamento de Blobs em tempo de execução. O conjunto de dados especifica o contêiner, a pasta e o arquivo (opcional) que contêm os dados de origem. 

    ![Botão Novo serviço vinculado](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. Na janela **Novo Serviço Vinculado**, execute estas etapas: 

    1. Especifique **AzureStorageLinkedService** no campo **Nome**. 
    2. Selecione sua conta de armazenamento do Azure no campo **Nome da conta de armazenamento**.
    3. Clique em **Testar conexão** para testar a conexão à Conta de Armazenamento do Azure.  
    4. Clique em **Salvar** para salvar o serviço vinculado.

        ![Novo Serviço Vinculado de Armazenamento do Azure](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. Clique em **Procurar** para o campo **Caminho do arquivo**.  

    ![Botão Procurar do arquivo](./media/tutorial-copy-data-portal/file-browse-button.png)
14. Navegue até a pasta **adftutorial/input**, selecione o arquivo **emp.txt** e clique em **Concluir**. Como alternativa, clique duas vezes em emp.txt. 

    ![Selecione o arquivo de entrada](./media/tutorial-copy-data-portal/select-input-file.png)
15. Confirme se o **Formato de arquivo** está definido como **Formato de texto** e se o **delimitador de coluna** está definido como **Vírgulas (`,`)**. Se o arquivo de origem usa delimitadores de coluna e de linha diferentes, você pode clicar em **Detectar Formato de Texto** para o campo **Formato de arquivo**. A ferramenta Copiar dados detecta o formato de arquivo e os delimitadores automaticamente para você. Ainda é possível substituir esses valores. Você pode visualizar os dados nessa página clicando em **Visualizar dados**.

    ![Detectar formato de texto](./media/tutorial-copy-data-portal/detect-text-format.png)
17. Alterne para a guia **Esquema** na janela Propriedades e clique em **Importar Esquema**. Observe que o aplicativo detectou duas colunas no arquivo de origem. Você está importando o esquema aqui para poder mapear colunas do armazenamento de dados de origem para o armazenamento de dados do coletor. Se você não precisa mapear as colunas, pode ignorar esta etapa. Para este tutorial, importe o esquema.

    ![Detectar esquema de origem](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. Agora, alterne para a **guia com o pipeline** ou clique no pipeline no **modo de exibição de árvore** à esquerda.  

    ![Guia pipeline](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. Confirme se o **SourceBlobDataset** está selecionado para o campo Conjunto de Dados de Origem na janela Propriedades. Você pode visualizar os dados nessa página clicando em **Visualizar dados**. 
    
    ![Conjunto de dados de origem](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. Alterne para a guia **Coletor** e clique em **Novo** para criar um conjunto de dados do coletor. 

    ![Menu do novo conjunto de dados do coletor](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. Na janela **Novo Conjunto de Dados**, selecione **Banco de Dados SQL do Azure** e clique em **Concluir**. Você está copiando dados para um banco de dados SQL do Azure neste tutorial. 

    ![Selecione um Banco de Dados SQL do Azure](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. Na guia **Geral** da janela Propriedades, defina o nome como **OutputSqlDataset**. 
    
    ![Nome do conjunto de dados de saída](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. Alterne para a guia **Conexão** e clique em **Novo** para o **Serviço vinculado**. Um conjunto de dados deve ser associado a um serviço vinculado. O serviço vinculado tem a cadeia de conexão usada pelo serviço do Data Factory para se conectar ao banco de dados SQL do Azure no tempo de execução. O conjunto de dados especifica o contêiner, a pasta e o arquivo (opcional) para os quais os dados são copiados. 
    
    ![Botão Novo serviço vinculado](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. Na janela **Novo Serviço Vinculado**, execute estas etapas: 

    1. Insira **AzureSqlDatabaseLinkedService** para o campo **Nome**. 
    2. Selecione o servidor SQL do Azure no campo **Nome do servidor**.
    4. Selecione o seu banco de dados SQL do Azure no campo **Nome do banco de dados**. 
    5. Insira o nome do usuário no campo **Nome de usuário**. 
    6. Insira a senha do usuário no campo **Senha**. 
    7. Clique em **Testar conectividade** para testar a conexão.
    8. Clique em **Salvar** para salvar o serviço vinculado. 
    
        ![Novo serviço vinculado do Banco de Dados SQL do Azure](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. Selecione **[dbo].[emp]** para **Tabela**. 

    ![Selecione a tabela emp](./media/tutorial-copy-data-portal/select-emp-table.png)
27. Alterne para a guia **Esquema** e clique em Importar Esquema. 

    ![Importar esquema de destino](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. Selecione a coluna **ID** e clique em **Excluir**. A coluna ID é uma coluna de identidade no banco de dados SQL, então a atividade de cópia não precisa inserir dados nesta coluna.

    ![Excluir coluna de ID](./media/tutorial-copy-data-portal/delete-id-column.png)
30. Alterne para a guia com o **pipeline** e confirme se **OutputSqlDataset** está selecionado para o **Conjunto de Dados do Coletor**.

    ![Guia pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. Alterne para a guia **Mapeamento** na janela Propriedades na parte inferior e clique em **Importar Esquemas**. Observe que a primeira e segunda colunas no arquivo de origem são mapeadas para os campos **FirstName** e **LastName** no banco de dados SQL.

    ![Mapear esquemas](./media/tutorial-copy-data-portal/map-schemas.png)
33. Valide o pipeline clicando no botão **Validar**. Clique na **seta para a direita** para fechar a janela Validação.

    ![Saída de validação do pipeline](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. Clique no botão **Código** no canto direito. Você verá o código JSON associado ao pipeline. 

    ![Botão Código](./media/tutorial-copy-data-portal/code-button.png)
35. Você verá o código JSON semelhante ao seguinte trecho:  

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>Executar teste do pipeline
Você pode fazer a execução de teste de um pipeline antes de publicar os artefatos (serviços vinculados, conjuntos de dados e pipeline) no Data Factory (ou) seu próprio repositório GIT do VSTS. 

1. Para fazer a execução de teste do pipeline, clique em **Execução de teste** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** da janela na parte inferior. 

    ![Botão Execução de teste](./media/tutorial-copy-data-portal/test-run-output.png)
2. Verifique se os dados do arquivo de origem estão inseridos no banco de dados SQL de destino. 

    ![Verifique a saída SQL](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. Clique em **Publicar Tudo** no painel esquerdo. Esta ação publica as entidades (serviços vinculados, conjuntos de dados e pipelines) criadas por você anteriormente no Azure Data Factory.

    ![Botão Publicar](./media/tutorial-copy-data-portal/publish-button.png)
4. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, clique na guia **Mostrar Notificações** na barra lateral esquerda. Feche a janela de notificações clicando em **X**.

    ![Mostrar notificações](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Configurar repositório de código
Você pode publicar o código associado com seus artefatos do data factory a um repositório de código do Visual Studio Team System (VSTS). Nesta etapa, você criará o repositório de código. 

Se você não quiser trabalhar com o repositório de código do VSTS, pode ignorar esta etapa e continuar publicando no Data Factory como você fez na etapa anterior. 

1. Clique no **Data Factory** no canto esquerdo (ou) na seta para baixo ao lado dele e clique em **Configurar Repositório de Código**. 

    ![Botão Código](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. Na página **Configurações de Repositório**, execute as seguintes etapas: 
    1. Selecione **Git do Visual Studio Team Services** no campo **Tipo de Repositório**.
    2. Selecione sua conta do VSTS no campo **Conta do Visual Studio Team Services**.
    3. Selecione um projeto na sua conta do VSTS no campo **Nome do Projeto**.
    4. Insira **Tutorial2** no **nome do repositório Git** a ser associado ao seu data factory. 
    5. Confirme se a opção **Importar recursos existentes do Data Factory para o repositório** está selecionada. 
    6. Clique em **Salvar** para salvar as configurações. 

        ![Configurações do repositório](./media/tutorial-copy-data-portal/repository-settings.png)
3. Confirme se **GIT do VSTS** está selecionado para o repositório.

    ![GIT do VSTS selecionado](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. Em uma guia separada do navegador da Web, navegue até o repositório do **Tutorial2** e você verá dois branches: **mestre** e **adf_publish**.

    ![Branches mestre e adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Verifique se os **arquivos JSON** das entidades do Data Factory estão no branch **mestre**.

    ![Arquivos no branch mestre](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Verifique se os **arquivos JSON** ainda não estão no branch **adf_publish**. 

    ![Arquivos no branch adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Adicione uma **descrição** para o **pipeline** e clique no botão **Salvar** na barra de ferramentas. 

    ![Adicione uma descrição para o pipeline](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Agora, você deve ver um **branch** com seu nome de usuário no repositório do **Tutorial2**. A alteração feita por você está no seu branch, não no branch mestre. Você só pode publicar entidades a partir do branch mestre.

    ![Sua ramificação](./media/tutorial-copy-data-portal/your-branch.png)
9. Mova até o botão **Sincronizar** (não clique ainda), selecione a opção **Confirmar Alterações** e clique no botão **Sincronizar** para sincronizar suas alterações com o branch **mestre**. 

    ![Confirmar e sincronizar suas alterações](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. Na janela Sincronizar suas alterações, execute as seguintes ações: 

    1. Confirme se **CopyPipeline** é exibido na lista atualizada de pipelines.
    2. Confirme se a opção **Publicar alterações após a sincronização** está selecionada. Ao desmarcar essa opção, você só consegue sincronizar suas alterações em seu branch com o branch mestre, mas não as publica no serviço do Data Factory. Você pode publicá-las posteriormente usando o botão **Publicar**. Ao marcar essa opção, as alterações são sincronizadas primeiro com o mestre, e depois são publicadas no serviço do Data Factory.
    3. Clique em **Sincronizar**. 

    ![Janela Sincronizar suas alterações](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. Agora você pode ver os arquivos no branch **adf_publish** do repositório **Tutorial2** . Você também pode encontrar o modelo do Azure Resource Manager para sua solução do Data Factory nesse branch.  

    ![Arquivos no branch adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente
Nesta etapa, você aciona manualmente o pipeline publicado na etapa anterior. 

1. Clique em **Gatilho** na barra de ferramentas e, depois, em **Gatilho agora**. Na página **Executar Pipeline**, clique em **Concluir**.  

    ![Menu Disparar agora](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Alterne para a guia **Monitor** à esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar os links na coluna Ações para exibir detalhes da atividade e executar o pipeline novamente.

    ![Monitorar execução do pipeline](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Para ver as execuções de atividade associadas com a execução de pipeline, clique no link **Exibir as Execuções de Atividade** na coluna **Ações**. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, clique no link **Detalhes** (ícone de óculos) na coluna **Ações**. Você pode clicar em **Pipelines** na parte superior para alternar novamente para a exibição de **Execuções de Pipeline**. Para atualizar a exibição, clique em **Atualizar**.

    ![Exibir execuções de atividade](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Verifique se mais duas linhas são adicionadas à tabela **emp** no banco de dados SQL do Azure. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Disparar o pipeline em um cronograma
Nesse cronograma, você criará um agendador de gatilho para o pipeline. O gatilho dispara o pipeline conforme a agenda especificada (por hora, por dia, etc.). Neste exemplo, você definirá o gatilho para executar a cada minuto até o datetime de término especificado. 

1. Alterne para a guia **Editar** à esquerda. 

    ![Editar tabela](./media/tutorial-copy-data-portal/edit-tab.png)
2. Clique em **Gatilho** e selecione **Novo/Editar**. Se o pipeline não estiver ativo, alterne para ele. 

    ![Menu novo/editar gatilho](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. Na janela **Adicionar Gatilhos**, clique em **Escolher gatilho...** e depois em **+ Novo**. 

    ![Adicionar gatilhos – novo gatilho](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Na janela **Novo Gatilho**, execute as seguintes etapas: 

    1. Defina o **nome** como **RunEveryMinute**.
    2. Selecione **Na Data** como **Final**. 
    3. Clique na lista suspensa de **Final Em**.
    4. Selecione o **dia atual**. Por padrão, o dia final é definido para o próximo dia. 
    5. Atualize a parte de **minutos** para alguns minutos após o datetime atual. O gatilho só é ativado depois de você publicar as alterações. Portanto, se você defini-lo com apenas alguns minutos de diferença e não publicá-lo até lá, não verá uma execução do gatilho.  
    6. Clique em **Aplicar**. 

        ![Definir propriedades do gatilho](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. Verifique a opção **Ativado**. Você pode desativá-la e ativá-la mais tarde usando esta caixa de seleção.
    8. Clique em **Próximo**.

        ![Gatilho ativado – próximo](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Há um custo associado a cada execução do pipeline. Portanto, defina a data de término adequadamente. 
6. Na página **Parâmetros de Execução do Gatilho**, leia o aviso e clique em **Concluir**. O pipeline neste exemplo não tem parâmetros. 

    ![Parâmetros de pipeline](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. Clique em **Sincronizar** para sincronizar as alterações em seu branch com o branch mestre. Por padrão, a opção **Publicar alterações após a sincronização** está selecionada. Portanto, quando você seleciona **Sincronizar**, também são publicadas as entidades atualizadas para o serviço do Azure Data Factory a partir do branch mestre. O gatilho não está realmente ativado até que a publicação seja bem-sucedida.

    ![Publicar um gatilho](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
9. Alterne para a guia **Monitor** à esquerda para ver as execuções de pipeline disparadas. 

    ![Execuções de pipeline disparadas](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. Para alternar do modo de exibição de execuções de pipeline para o modo de exibição de execuções do gatilho, clique em Execuções de Pipeline e selecione Execuções de Gatilho.
    
    ![Menu de execuções de gatilho](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. As execuções de gatilho serão exibidas em uma lista. 

    ![Lista de execuções de gatilho](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. Verifique se duas linhas por minuto (para cada execução de pipeline) são inseridas na tabela **emp** até a hora de término especificada. 

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma Atividade de cópia.
> * Executar teste do pipeline
> * Disparar o pipeline manualmente
> * Disparar o pipeline em um cronograma
> * Monitore as execuções de pipeline e de atividade.


Avance para o tutorial a seguir para saber mais sobre a cópia de dados local para a nuvem: 

> [!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-portal.md)
