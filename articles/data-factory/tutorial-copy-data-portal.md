---
title: Usar o portal do Azure para criar um pipeline do Data Factory | Microsoft Docs
description: "Este tutorial fornece instruções passo a passo para usar o portal do Azure a fim de criar um data factory com um pipeline. O pipeline usa a atividade de cópia para copiar dados do Armazenamento de blobs do Azure para um banco de dados SQL do Azure."
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
ms.openlocfilehash: 116832175a4b7e4497c9005be7841cb56c1d235b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Copiar dados do Armazenamento de Blobs do Azure para um banco de dados SQL usando o Azure Data Factory
Neste tutorial, você criará um data factory ao usar a interface do usuário do Azure Data Factory. O pipeline neste data factory copia dados do Armazenamento de Blobs do Azure para um banco de dados SQL. O padrão de configuração neste tutorial aplica-se a cópia de um armazenamento de dados baseado em arquivo para um armazenamento de dados relacional. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Se estiver se familiarizando com o Data Factory, confira a [Introdução ao Azure Data Factory](introduction.md).
>
> - Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do Data Factory, que está geralmente disponível, confira [Introdução à versão 1 do Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma atividade de cópia.
> * Executar teste do pipeline.
> * Disparar o pipeline manualmente.
> * Disparar o pipeline em um cronograma.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>pré-requisitos
* **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de *origem*. Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) a fim de conhecer as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de *coletor*. Se você não tiver um banco de dados SQL, confira [Criar um banco de dados SQL](../sql-database/sql-database-get-started-portal.md) a fim de conhecer as etapas para criar um.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu armazenamento de blobs e o banco de dados SQL para o tutorial executando as etapas a seguir.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **emp.txt** no disco:

    ```
    John,Doe
    Jane,Doe
    ```

2. Crie um contêiner chamado **adftutorial** no seu Armazenamento de blobs. Crie uma pasta chamada **input** nesse contêiner. Em seguida, carregue o arquivo **emp.txt** na pasta **input**. Use as ferramentas ou o portal do Azure, como [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) para realizar essas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

1. Use o script SQL a seguir para criar a tabela **dbo.emp** em seu banco de dados SQL:

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

2. Permita que os serviços do Azure acessem o SQL Server. Verifique se a opção **Permitir acesso aos serviços do Azure** está **ATIVADA** para o SQL Server, para que o Data Factory possa gravar dados no SQL Server. Para verificar e ativar a configuração, siga estas etapas:

    a. À esquerda, selecione **Mais serviços** > **SQL Servers**.

    b. Selecione o servidor e, em **CONFIGURAÇÕES**, selecione **Firewall**.

    c. Na página **Configurações de Firewall**, selecione **ATIVADO** para **Permitir acesso aos serviços do Azure**.

## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você cria um data factory e inicia a interface do usuário do Data Factory para criar um pipeline no data factory. 

1. Abra o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
2. No menu à esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**. 
  
   ![Criação do novo data factory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**. 
      
     ![Novo data factory](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).
  
   ![Mensagem de erro](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. Selecione a **assinatura** do Azure na qual deseja criar o data factory. 
5. Em **Grupo de Recursos**, use uma das seguintes etapas:
     
    a. Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md). 
6. Em **Versão**, selecione **V2 (Versão prévia)**.
7. Em **Local**, informe uma localização para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo data factory podem estar em outras regiões.
8. Selecione **Fixar no painel**. 
9. Selecione **Criar**. 
10. No painel, o bloco com o seguinte status é exibido: **Implantando o Data Factory**: 

    ![Bloco Como implantar o Data Factory](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. Depois de finalizada a criação, a página **Data Factory** será exibida conforme mostrado na imagem.
   
    ![Página inicial da data factory](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. Clique em **Criar e Monitorar** para iniciar a IU do Azure Data Factory em uma guia separada.

## <a name="create-a-pipeline"></a>Criar uma pipeline
Nesta etapa, você criará um pipeline com a atividade de cópia no data factory. A Atividade de cópia copia dados do Armazenamento de blobs para o Banco de Dados SQL. No [Tutorial de início rápido](quickstart-create-data-factory-portal.md), você criou um pipeline seguindo estas etapas:

1. Criar o serviço vinculado. 
2. Criar conjuntos de dados de entrada e saída.
3. Crie um pipeline.

Neste tutorial, inicie com a criação do pipeline. Em seguida, crie conjuntos de dados e serviços vinculados quando forem necessários para configurar o pipeline. 

1. Na página **Introdução**, selecione **Criar pipeline**. 

   ![Criar um pipeline](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. Na janela **Propriedades** do pipeline, em **Nome**, insira **CopyPipeline** como nome do pipeline.

    ![Nome do pipeline](./media/tutorial-copy-data-portal/pipeline-name.png)
3. Na caixa de ferramentas de **Atividades**, expanda a categoria **DataFlow** e arraste e solte a atividade **Copiar** da caixa de ferramentas para a superfície do designer do pipeline. 

    ![Atividade de cópia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
4. Na guia **Geral** da janela **Propriedades**, insira **CopyFromBlobToSql** como o nome da atividade.

    ![Nome da atividade](./media/tutorial-copy-data-portal/activity-name.png)
5. Vá para a guia **Origem**. Selecione **+ Novo** para criar um conjunto de dados de origem. 

    ![Guia Origem](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
6. Na janela **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e selecione **Concluir**. Os dados de origem estão em um Armazenamento de blobs e, portanto, você deve selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem. 

    ![Seleção de armazenamento](./media/tutorial-copy-data-portal/select-azure-storage.png)
7. Uma nova guia será aberta no aplicativo com o título **AzureBlob1**.

    ![Guia AzureBlob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
8. Na guia **Geral** na parte inferior da janela **Propriedades**, insira **SourceBlobDataset** em **Nome**.

    ![Nome do conjunto de dados](./media/tutorial-copy-data-portal/dataset-name.png)
9. Alterne para a guia **Conexão** na janela **Propriedades**. Selecione **+ Novo** ao lado da caixa de texto **Serviço vinculado**. 

    Um serviço vinculado vincula um armazenamento de dados ou uma computação a um data factory. Nesse caso, crie um Serviço de armazenamento vinculado para vincular sua conta de armazenamento para o armazenamento de dados. O serviço vinculado tem as informações de conexão que o Data Factory usa para se conectar ao seu Armazenamento de blobs em tempo de execução. O conjunto de dados especifica o contêiner, pasta e arquivo (opcional) que contém os dados de origem. 

    ![Botão Novo serviço vinculado](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
10. Na janela **Novo Serviço Vinculado**, siga as etapas a seguir: 

    a. Em **Nome**, insira **AzureStorageLinkedService**. 

    b. Em **Nome da conta de armazenamento**, selecione sua conta de armazenamento.

    c. Selecione **Testar conexão** para testar a conexão com a conta de armazenamento.

    d. Selecione **Salvar** para salvar o serviço vinculado.

    ![Novo serviço vinculado](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
11. Ao lado de **Caminho do arquivo**, selecione **Procurar**.

    ![Botão Procurar para o caminho do arquivo](./media/tutorial-copy-data-portal/file-browse-button.png)
12. Vá para a pasta **adftutorial/entrada**, selecione o arquivo **emp.txt** e selecione **Concluir**. Como alternativa, clique duas vezes em **emp.txt**. 

    ![Selecione o arquivo de entrada](./media/tutorial-copy-data-portal/select-input-file.png)
13. Confirme se o **Formato de arquivo** está definido como **Formato de texto** e se o **Delimitador de coluna** está definido como **Vírgulas (`,`)**. Se o arquivo de origem usa delimitadores de coluna e de linha diferentes, você pode selecionar **Detectar Formato de Texto** como **Formato de arquivo**. A ferramenta Copiar dados detecta o formato de arquivo e os delimitadores automaticamente para você. Ainda é possível substituir esses valores. Para visualizar os dados da página, selecione **Visualizar dados**.

    ![Detectar formato de texto](./media/tutorial-copy-data-portal/detect-text-format.png)
14. Vá para a guia **Esquema** da janela **Propriedades** e selecione **Importar Esquema**. Observe que o aplicativo detectou duas colunas no arquivo de origem. Você importa o esquema aqui para poder mapear colunas do armazenamento de dados de origem para o armazenamento de dados do coletor. Se você não precisa mapear as colunas, pode ignorar esta etapa. Para este tutorial, importe o esquema.

    ![Detectar esquema de origem](./media/tutorial-copy-data-portal/detect-source-schema.png)  
15. Agora, vá para a guia com o pipeline ou selecione o pipeline à esquerda.

    ![Guia Pipeline](./media/tutorial-copy-data-portal/pipeline-tab.png)
16. Em **Conjunto de Dados de Origem** na janela **Propriedades**, confirme se **SourceBlobDataset** está selecionado. Para visualizar os dados da página, selecione **Visualizar dados**. 
    
    ![Conjunto de dados de origem](./media/tutorial-copy-data-portal/source-dataset-selected.png)
17. Alterne para a guia **Coletor** e selecione **+ Novo** para criar um conjunto de dados do coletor. 

    ![Conjunto de dados do coletor](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
18. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e selecione **Concluir**. Neste tutorial, você copia dados para um banco de dados SQL. 

    ![Seleção de banco de dados SQL](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
19. Na guia **Geral** da janela **Propriedades**, defina o **Nome** como **OutputSqlDataset**. 
    
    ![Nome do conjunto de dados de saída](./media/tutorial-copy-data-portal/output-dataset-name.png)
20. Vá para a guia **Conexão** e, ao lado de **Serviço vinculado**, selecione **+ Novo**. Um conjunto de dados deve ser associado um serviço vinculado. O serviço vinculado tem a cadeia de conexão que o Data Factory usa para se conectar ao banco de dados SQL no tempo de execução. O conjunto de dados especifica o contêiner, pasta e arquivo (opcional) para o qual os dados são copiados. 
    
    ![Serviço vinculado](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
21. Na janela **Novo Serviço Vinculado**, siga as etapas a seguir: 

    a. Em **Nome**, insira **AzureSqlDatabaseLinkedService**.

    b. Em **Nome do servidor**, selecione sua instância do SQL Server.

    c. Em **Nome do banco de dados**, selecione seu banco de dados SQL.

    d. Em **Nome de usuário**, insira o nome do usuário.

    e. Em **Senha**, insira a senha do usuário.

    f. Selecione **Testar conectividade** para testar a conexão.

    g. Selecione **Salvar** para salvar o serviço vinculado. 
    
    ![Salvar o novo serviço vinculado](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

22. Em **Tabela**, selecione **[dbo].[emp]**. 

    ![Tabela](./media/tutorial-copy-data-portal/select-emp-table.png)
23. Vá para a guia **Esquema** e selecione **Importar Esquema**. 

    ![Selecionar Importar esquema](./media/tutorial-copy-data-portal/import-destination-schema.png)
24. Selecione a coluna **ID** e selecione **Excluir**. A coluna **ID** é uma coluna de identidade no banco de dados SQL e, portanto, a atividade de cópia não precisa inserir dados nesta coluna.

    ![Excluir coluna de ID](./media/tutorial-copy-data-portal/delete-id-column.png)
25. Alterne para a guia com o pipeline e, em **Conjunto de Dados do Coletor**, confirme se **OutputSqlDataset** está selecionado.

    ![Guia Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
26. Vá para a guia **Mapeamento** na parte inferior da janela **Propriedades** e selecione **Importar Esquemas**. Observe que a primeira e segunda colunas no arquivo de origem são mapeadas para **FirstName** e **LastName** no banco de dados SQL.

    ![Mapear esquemas](./media/tutorial-copy-data-portal/map-schemas.png)
27. Selecione **Validar** para validar as configurações de pipeline. No canto superior direito, selecione a seta para a direita a fim de fechar a janela de validação.

    ![Saída de validação do pipeline](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
28. No canto superior direito, selecione **Código**. Você verá o código JSON associado ao pipeline. 

    ![Botão Código](./media/tutorial-copy-data-portal/code-button.png)
29. Você verá um código JSON semelhante ao seguinte trecho: 

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
Você pode fazer a execução de teste de um pipeline antes de publicar os artefatos (serviços vinculados, conjuntos de dados e pipeline) no Data Factory ou em seu próprio repositório Git do Visual Studio Team Services. 

1. Para fazer a execução de teste do pipeline, selecione **Execução de Teste** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela. 

    ![Pipeline de teste](./media/tutorial-copy-data-portal/test-run-output.png)
2. Verifique se os dados do arquivo de origem estão inseridos no banco de dados SQL de destino. 

    ![Verifique a saída SQL](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. No painel esquerdo, selecione **Publicar Tudo**. Esta ação publica as entidades (serviços vinculados, conjuntos de dados e pipelines) criadas por você anteriormente no Data Factory.

    ![Publicar](./media/tutorial-copy-data-portal/publish-button.png)
4. Aguarde até que você veja a mensagem **Publicado com êxito**. Selecione a guia **Mostrar Notificações** na barra lateral esquerda para ver as mensagens de notificação. Para fechar a janela de notificações, selecione **Fechar**.

    ![Mostrar notificações](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Configurar repositório de código
Você pode publicar o código associado com seus artefatos do data factory a um repositório de código do Visual Studio Team Services. Nesta etapa, você criará o repositório de código. 

Se você não quiser trabalhar com o repositório de código do Visual Studio Team Services, ignore esta etapa. Você pode continuar publicando no Data Factory como fez na etapa anterior. 

1. No canto superior esquerdo, selecione **Data Factory** ou use a seta para baixo ao lado dele e selecione **Configurar Repositório de Código**. 

    ![Configurar repositório de código](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. Na página **Configurações de Repositório**, siga estas etapas:

    a. Selecione **Git do Visual Studio Team Services** em **Tipo de Repositório**.

    b. Em **Conta do Visual Studio Team Services**, selecione sua conta do Visual Studio Team Services.

    c. Em **Nome do Projeto**, selecione um projeto na sua conta do Visual Studio Team Services.

    d. Em **Nome do repositório Git**, Insira **Tutorial2** no nome do repositório Git a ser associado ao seu data factory.

    e. Confirme se a caixa de seleção **Importar recursos existentes do Data Factory para o repositório** está marcada.

    f. Clique em **Salvar** para salvar as configurações. 

    ![Configurações do repositório](./media/tutorial-copy-data-portal/repository-settings.png)
3. Confirme se **GIT do VSTS** está selecionado para o repositório.

    ![Selecionar GIT VSTS](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. Em uma guia separada do navegador da Web, vá para o repositório **Tutorial2**. Você verá dois branches: **adf_publish** e **mestre**.

    ![Branches mestre e adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Verifique se os arquivos JSON das entidades do Data Factory estão no branch **mestre**.

    ![Arquivos no branch mestre](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Verifique se os arquivos JSON ainda não estão no branch **adf_publish**. 

    ![Arquivos no branch adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Em **Descrição**, adicione uma descrição para o pipeline e selecione **Salvar** na barra de ferramentas. 

    ![Descrição de pipeline](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Agora, você vê um **branch** com seu nome de usuário no repositório Tutorial2. A alteração feita por você está no seu branch, não no branch mestre. Você só pode publicar entidades a partir do branch mestre.

    ![Sua ramificação](./media/tutorial-copy-data-portal/your-branch.png)
9. Mova o mouse sobre o botão **Sincronizar** (não selecione ainda), marque a caixa de seleção **Confirmar Alterações** e selecione **Sincronizar** para sincronizar suas alterações com o branch mestre. 

    ![Confirmar e sincronizar alterações](./media/tutorial-copy-data-portal/commit-and-sync.png)
10. Na janela **Sincronizar suas alterações**, execute as seguintes ações: 

    a. Confirme se **CopyPipeline** é exibido na lista atualizada **Pipelines**.

    b. Confirme se a opção **Publicar alterações após a sincronização** está selecionada. Se você desmarcar essa caixa de seleção, sincronizará somente as alterações no seu branch com o branch mestre. Elas não serão publicadas no Data Factory. Você pode publicá-las posteriormente usando o botão **Publicar**. Se você marcar essa caixa de seleção, as alterações serão sincronizadas com o mestre primeiro e, em seguida, publicadas no Data Factory.

    c. Selecione **Sincronizar**. 

    ![Sincronizar as alterações](./media/tutorial-copy-data-portal/sync-your-changes.png)
11. Agora você pode ver os arquivos no branch **adf_publish** do repositório **Tutorial2** . Você também pode encontrar o modelo do Azure Resource Manager para sua solução do Data Factory nesse branch. 

    ![Lista de arquivos no branch adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente
Nesta etapa, você aciona manualmente o pipeline publicado na etapa anterior. 

1. Selecione **Gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página **Execução de Pipeline**, selecione **Concluir**.  

    ![Disparar pipeline](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Vá para a guia **Monitorar** à esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar os links na coluna **Ações** para exibir detalhes da atividade e executar o pipeline novamente.

    ![Monitorar execuções de pipeline](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Selecione o link **Exibir atividades em execução** na coluna **Ações** para ver a atividade em execução associada à execução do pipeline. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. Para voltar para o modo de exibição **Execuções de Pipeline**, selecione **Pipelines** na parte superior. Para atualizar a exibição, selecione **Atualizar**.

    ![Monitorar execuções de atividade](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Verifique se mais duas linhas são adicionadas à tabela **emp** no banco de dados SQL. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Disparar o pipeline em um cronograma
Nesse cronograma, você criará um gatilho de agenda para o pipeline. O gatilho dispara o pipeline conforme a agenda especificada, como por hora ou por dia. Neste exemplo, você definirá o gatilho para executar a cada minuto até o datetime de término especificado. 

1. Vá para a guia **Editar** à esquerda. 

    ![Guia Editar](./media/tutorial-copy-data-portal/edit-tab.png)
2. Selecione **Gatilho** e selecione **Novo/Editar**. Se o pipeline não estiver ativo, vá até ele. 

    ![Opção de gatilho](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. Na janela **Adicionar Gatilhos**, selecione **Escolher gatilho** e selecione **+Novo**. 

    ![Novo botão](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Na janela **Novo Gatilho**, siga estas etapas: 

    a. Em **Nome**, digite **RunEveryMinute**.

    b. Em **Término**, selecione **Na Data**.

    c. Em **Terminar Em**, selecione a lista suspensa.

    d. Selecione a opção do **dia atual**. Por padrão, o dia final é definido para o próximo dia.

    e. Atualize a parte de **minutos** para alguns minutos após o datetime atual. O gatilho só é ativado depois de você publicar as alterações. Se você defini-lo com apenas alguns minutos de diferença e não publicá-lo até lá, não verá uma execução do gatilho.

    f. Escolha **Aplicar**. 

    ![Propriedades do gatilho](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. Selecione a opção **Ativado**. Você pode desativá-la e ativá-la mais tarde usando esta caixa de seleção.

    h. Selecione **Avançar**.

    ![Botão Ativado](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Há um custo associado a cada execução de pipeline e, portanto, defina a data de término adequadamente. 
5. Na página **Parâmetros da Execução de Gatilho**, revise o aviso e selecione **Concluir**. O pipeline neste exemplo não usa parâmetros. 

    ![Parâmetros de execução do gatilho](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
6. Selecione **Sincronizar** para sincronizar as alterações em seu branch com o branch mestre. Por padrão, **Publicar alterações após a sincronização** está selecionada. Quando você seleciona **Sincronizar**, também são publicadas as entidades atualizadas para o serviço do Data Factory a partir do branch mestre. O gatilho não é realmente ativado até que a publicação seja bem-sucedida.

    ![Sincronizar as alterações](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
7. Vá para a guia **Monitor** à esquerda para ver as execuções de pipeline disparadas. 

    ![Execuções de pipeline disparadas](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. Para alternar do modo de exibição **Execuções de Pipeline** para o modo de exibição **Execuções de Gatilho**, selecione **Execuções de Pipeline** e selecione **Execuções de Gatilho**.
    
    ![Execuções de gatilho](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. As execuções de gatilho serão exibidas em uma lista. 

    ![Lista de execuções de gatilho](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. Verifique se duas linhas por minuto (para cada execução de pipeline) são inseridas na tabela **emp** até a hora de término especificada. 

## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de uma localização para outra em um Armazenamento de Blobs. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma atividade de cópia.
> * Executar teste do pipeline.
> * Disparar o pipeline manualmente.
> * Disparar o pipeline em um cronograma.
> * Monitore as execuções de pipeline e de atividade.


Avance para o tutorial a seguir para saber mais sobre como copiar dados do local para a nuvem: 

> [!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-portal.md)
