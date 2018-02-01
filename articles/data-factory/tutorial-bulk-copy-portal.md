---
title: Copiar dados em massa usando o Azure Data Factory | Microsoft Docs
description: "Saiba como usar o Azure Data Factory e atividade de cópia para copiar dados em massa de um armazenamento de dados de origem para um armazenamento de dados de destino."
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 6aa5d4aa032ef4dc3583bf76b9c451874b74f9a6
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copiar várias tabelas em massa usando o Azure Data Factory
Este tutorial demonstra como **copiar uma série de tabelas do Banco de Dados SQL do Azure para o SQL Data Warehouse do Azure**. Você também pode aplicar o mesmo padrão em outros cenários de cópia. Por exemplo, copiando tabelas do SQL Server/Oracle para o Banco de Dados SQL do Azure/Data Warehouse/Blob do Azure, copiando diferentes caminhos do Blob para tabelas do Banco de Dados SQL do Azure.

> [!NOTE]
> - Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).
> - Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

De forma mais abrangente, este tutorial envolve as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar serviços vinculados do Banco de Dados SQL do Azure, do SQL Data Warehouse do Azure e do Armazenamento do Azure.
> * Criar conjuntos de dados do Banco de Dados SQL do Azure e do SQL Data Warehouse do Azure.
> * Crie um pipeline para consultar as tabelas a serem copiadas e outro pipeline para executar a operação de cópia propriamente dita. 
> * Inicie uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Este tutorial usa o portal do Azure. Para obter informações sobre como usar outras ferramentas/SDKs para criar um data factory, consulte [Guias de início rápido](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos
Nesse cenário, você tem um número de tabelas no Banco de Dados SQL do Azure que deseja copiar para o SQL Data Warehouse. Aqui está a sequência lógica de etapas no fluxo de trabalho que ocorre em pipelines:

![Fluxo de trabalho](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* O primeiro pipeline verifica a lista de tabelas que precisam ser copiadas nos armazenamentos de dados do coletor.  Alternativamente, você pode manter uma tabela de metadados que lista todas as tabelas a serem copiadas para o armazenamento de dados de coletor. Em seguida, o pipeline dispara outro pipeline, que faz iteração por cada tabela no banco de dados e executa a operação de cópia de dados.
* O segundo pipeline realiza a cópia propriamente dita. Ele usa a lista de tabelas como um parâmetro. Para cada tabela na lista, copie a tabela específica no Banco de Dados SQL do Azure para a tabela correspondente no SQL Data Warehouse usando a [cópia preparada via Armazenamento de Blobs e PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter o melhor desempenho. Neste exemplo, o primeiro pipeline envia a lista de tabelas como um valor para o parâmetro. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>pré-requisitos
* **Conta de Armazenamento do Azure**. A conta de Armazenamento do Azure é usada como Armazenamento de Blobs de preparo na operação de cópia em massa. 
* **Banco de dados SQL do Azure**. Este banco de dados contém os dados de origem. 
* **SQL Data Warehouse do Azure**. Esse data warehouse contém os dados copiados do Banco de Dados SQL. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Preparar Banco de Dados SQL e SQL Data Warehouse

**Prepare o Banco de Dados SQL do Azure de origem**:

Crie um Banco de Dados SQL do Azure contendo dados de exemplo do Adventure Works LT, seguindo o artigo [Criar um Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md). Esse tutorial copia todas as tabelas dessa amostra de banco de dados para um SQL data warehouse.

**Preparar o SQL Data Warehouse do Azure de coletor**:

1. Se você não tiver um SQL Data Warehouse do Azure, veja o artigo [Criar um SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) para conhecer as etapas para criar um.

2. Crie os esquemas de tabela correspondentes no SQL Data Warehouse. Você pode usar o [Utilitário de Migração](https://www.microsoft.com/download/details.aspx?id=49100) para **migrar o esquema** do Banco de Dados SQL do Azure para o SQL Data Warehouse do Azure. Você usa o Azure Data Factory para migrar/copiar dados em uma etapa posterior.

## <a name="azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure acessem o SQL Server

Para o Banco de Dados SQL e o SQL Data Warehouse, permita que os serviços do Azure acessem o SQL Server. Verifique se a configuração **Permitir acesso aos serviços do Azure** está **ATIVADA** para o Azure SQL Server. Essa configuração permite que o serviço Data Factory leia dados do Banco de Dados SQL do Azure e grave dados no SQL Data Warehouse do Azure. Para verificar e ativar essa configuração, faça as seguintes etapas:

1. Clique no hub **Mais serviços** à esquerda e clique em **Servidores SQL**.
2. Selecione seu servidor e clique em **Firewall** em **CONFIGURAÇÕES**.
3. Na página **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.

## <a name="create-a-data-factory"></a>Criar uma data factory
1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialBulkCopyDF** como o **nome**. 
      
     ![Página de novo data factory](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você se deparar com o seguinte erro para o campo nome, altere o nome do data factory (por exemplo, seunomeADFTutorialBulkCopyDF). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Atualmente, o Data Factory V2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
    ![Página inicial da data factory](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
10. Clique no bloco **Autor & Monitor** para iniciar o aplicativo IU do Data Factory em uma guia separada.
11. Na página de **introdução**, alterne para a guia **Editar** no painel esquerdo, conforme mostrado na imagem a seguir:  

    ![Página Introdução](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Criar serviços vinculados
Você cria os serviços vinculados para vincular seus armazenamentos de dados e serviços de computação ao data factory. Um serviço vinculado possui as informações de conexão que o serviço do Data Factory usa para conectar-se ao armazenamento de dados no tempo de execução. 

Neste tutorial, você vincula seu Banco de Dados SQL do Azure, o SQL Data Warehouse do Azure e os armazenamentos de dados do Armazenamento de Blobs do Azure para o seu data factory. O Banco de Dados SQL do Azure é o armazenamento de dados de origem. O SQL Data Warehouse do Azure é o armazenamento de dados do coletor/destino. O Armazenamento de BLOBs do Azure serve para preparar os dados antes dos dados serem carregados no SQL Data Warehouse usando PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Criar o serviço vinculado do Banco de Dados SQL do Azure de origem
Nesta etapa, você criará um serviço vinculado para vincular seu banco de dados SQL do Azure ao data factory. 

1. Clique em **Conexões** na parte inferior da janela e clique em **+ Novo** na barra de ferramentas. 

    ![Botão Novo serviço vinculado](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
2. Na janela **Novo Serviço Vinculado**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 

    ![Selecione um Banco de Dados SQL do Azure](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
3. Na janela **Novo Serviço Vinculado**, execute estas etapas: 

    1. Insira **AzureSqlDatabaseLinkedService** para o **Nome**. 
    2. Selecione o seu SQL Server do Azure para o **Nome do servidor**
    3. Selecione o seu banco de dados SQL do Azure para o **Nome do banco de dados**. 
    4. Digite **nome do usuário** para se conectar ao banco de dados SQL Azure. 
    5. Insira a **senha** do usuário. 
    6. Para testar a conexão ao banco de dados SQL do Azure usando as informações especificadas, clique em **Testar conectividade**.
    7. Clique em **Salvar**.

        ![Configurações do Banco de Dados SQL do Azure](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Criar o serviço vinculado do SQL Data Warehouse do Azure de coletor

1. Na guia **Conexões**, clique em **+ Novo** na barra de ferramentas novamente. 
2. Na janela **Novo serviço vinculado**, selecione **SQL Data Warehouse do Azure** e clique em **Continuar**. 
3. Na janela **Novo Serviço Vinculado**, execute estas etapas: 

    1. Insira **AzureSqlDWLinkedService** para o **Nome**. 
    2. Selecione o seu SQL Server do Azure para o **Nome do servidor**
    3. Selecione o seu banco de dados SQL do Azure para o **Nome do banco de dados**. 
    4. Digite **nome do usuário** para se conectar ao banco de dados SQL Azure. 
    5. Insira a **senha** do usuário. 
    6. Para testar a conexão ao banco de dados SQL do Azure usando as informações especificadas, clique em **Testar conectividade**.
    7. Clique em **Salvar**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Criar o serviço vinculado do Armazenamento do Azure de preparo
Neste tutorial, você usa o Armazenamento de Blobs do Azure como uma área de preparação intermediária para habilitar o PolyBase para um melhor desempenho de cópia.

1. Na guia **Conexões**, clique em **+ Novo** na barra de ferramentas novamente. 
2. Na janela **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 
3. Na janela **Novo Serviço Vinculado**, execute estas etapas: 

    1. Insira **AzureStorageLinkedService** como o **Nome**. 
    2. Selecione sua **conta de Armazenamento do Azure** como o **Nome da conta de armazenamento**.
    4. Clique em **Salvar**.


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste tutorial você cria os conjuntos de dados de origem e do coletor, que especificam o local onde os dados são armazenados. 

O conjunto de dados de entrada AzureSqlDatabaseDataset refere-se a AzureSqlDatabaseLinkedService. O serviço vinculado especifica a cadeia de conexão para se conectar ao banco de dados. O conjunto de dados especifica o nome do banco de dados e da tabela que contêm os dados de origem. 

O conjunto de dados de saída AzureSqlDWDataset refere-se a AzureSqlDWLinkedService. O serviço vinculado especifica a cadeia de conexão para se conectar ao data warehouse. O conjunto de dados especifica o banco de dados e a tabela para a qual os dados são copiados. 

Neste tutorial, as tabelas SQL de origem e de destino não são embutidas nas definições de conjunto de dados. Em vez disso, a atividade ForEach passa o nome da tabela em tempo de execução para a atividade de Cópia. 

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL de origem

1. Clique em **+ (adição)** no painel esquerdo e clique em **Conjunto de dados**. 

    ![Menu Novo conjunto de dados](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
2. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Concluir**. Você deve ver uma nova guia chamada **AzureSqlTable1**. 
    
    ![Selecione um Banco de Dados SQL do Azure](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
3. Na janela Propriedades, na parte inferior, digite **AzureSqlDatabaseDataset** para o **Nome**.

    ![Nome do conjunto de dados de origem](./media/tutorial-bulk-copy-portal/source-dataset-general.png)
4. Alterne para a guia **Conexão** e siga estas etapas: 

    1. Selecione **AzureSqlDatabaseLinkedService** para **Serviço vinculado**.
    2. Selecione qualquer tabela para **Tabela**. Esta é uma tabela fictícia. Você pode especificar uma consulta no conjunto de dados de origem ao criar um pipeline. A consulta é usada para extrair dados do Banco de Dados SQL do Azure. Como alternativa, você pode clicar na caixa de seleção **Editar** e inserir **dummyName** como o nome da tabela. 

    ![Página de conexão do conjunto de dados de origem](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Criar um conjunto de dados para o SQL Data Warehouse do Azure de coletor

1. Clique em **+ (adição)** no painel esquerdo e clique em **Conjunto de dados**. 
2. Na janela **Novo Conjunto de dados**, selecione **SQL Data Warehouse do Azure** e clique em **Concluir**. Você deve ver uma nova guia chamada **AzureSqlDWTable1**. 
3. Na janela Propriedades, na parte inferior, digite **AzureSqlDWDataset** para o **Nome**.
4. Alterne para a guia **Conexão** e selecione **AzureSqlDatabaseLinkedService** para o **Serviço vinculado**.
5. Alterne para a guia **Parâmetros** e clique em **+ Novo**

    ![Página de conexão do conjunto de dados de origem](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter-button.png)
6. Digite **DWTableName** para o nome do parâmetro. Se você copiar/colar esse nome da página, verifique se não há algum **caractere de espaço à direita** no final de **DWTableName**. 
7. Na seção **Propriedades parametrizadas**, digite `@{dataset().DWTableName}` para a propriedade **tableName**. A propriedade **tableName** do conjunto de dados é definida como o valor que é passado como um argumento para o parâmetro **DWTableName**. A atividade ForEach itera por meio de uma lista de tabelas e passa uma por uma para a atividade de Cópia. 
   
    ![Nome do parâmetro](./media/tutorial-bulk-copy-portal/dwtablename-tablename.png)

## <a name="create-pipelines"></a>Criar pipelines
Neste tutorial, você criará dois pipelines: **IterateAndCopySQLTables** e **GetTableListAndTriggerCopyData**. 

O pipeline **GetTableListAndTriggerCopyData** pipeline executa duas etapas:

* Pesquisa a tabela do sistema do Banco de Dados SQL do Azure para obter a lista de tabelas a serem copiadas.
* Dispara o pipeline **IterateAndCopySQLTables** para fazer a cópia de dados propriamente dita.

O **GetTableListAndTriggerCopyData** usa uma lista de tabelas, como um parâmetro. Para cada tabela na lista, ele copia dados da tabela no Banco de Dados SQL do Azure para o SQL Data Warehouse do Azure usando cópia preparada e o PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Criar o pipeline IterateAndCopySQLTables

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.

    ![Menu do novo pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. Na janela Propriedades, altere o nome do pipeline para **IterateAndCopySQLTables**. 

    ![Nome do pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-name.png)
3. Alterne para a guia **Parâmetros** e faça o seguinte: 

    1. Clique em **+ Novo**. 
    2. Digite **tableList** para o parâmetro **nome**.
    3. Selecione **Objeto** para o **Tipo**.

        ![Parâmetro do pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
4. Na caixa de ferramentas **Atividades**, expanda **Iteração e condições** e arraste e solte a atividade **ForEach** para a superfície de designer do pipeline. Você também pode pesquisar atividades na caixa de ferramentas **Atividades**. Na janela **Propriedades**, na parte inferior, digite **IterateSQLTables** para o **Nome**. 

    ![Nome da atividade ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-name.png)
5. Alterne para a guia **Configurações** e digite `@pipeline().parameters.tableList` para **Itens**.

    ![Configurações da atividade ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)
6. Para adicionar uma atividade filha para a atividade **ForEach** atividade, **clique duas vezes** na atividade ForEach (ou) clique em **Editar (ícone de lápis)**. Você verá os links de ação para uma atividade apenas quando você selecioná-la. 

    ![Nome da atividade ForEach](./media/tutorial-bulk-copy-portal/edit-for-each-activity.png)
7. Na caixa de ferramentas **Atividades**, expanda **Fluxo de dados** e arraste e solte a atividade de **Cópia** para a superfície de designer do pipeline e altere o nome na janela Propriedades para  **CopyData**. Observe o menu de navegação estrutural na parte superior. IterateAndCopySQLTable é o nome do pipeline e IterateSQLTables é o nome da atividade ForEach. O designer está no escopo da atividade. Para voltar ao editor do pipeline a partir do editor de ForEach, clique no link no menu de navegação estrutural. 

    ![Cópia no ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
8. Alterne para a guia **Origem** e siga estas etapas:

    1. Selecione **AzureSqlDatabaseDataset** para **Conjunto de dados de origem**. 
    2. Selecione a opção de **Consulta** para **Consulta do usuário**. 
    3. Insira a seguinte consulta SQL para **Consulta**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Configurações de Origem da cópia](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
9. Alterne para a guia **Coletor** e siga estas etapas: 

    1. Selecione **AzureSqlDWDataset** para **Conjunto de dados do coletor**.
    2. Expanda **Configurações do Polybase** e selecione **Permitir Polybase**. 
    3. Apague a opção **Usar Tipo padrão**. 
    4. Insira o seguinte script SQL para o **Script de limpeza**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Copiar as configurações do coletor](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
10. Alterne para a guia **Parâmetros**, role para baixo, se necessário, para ver a seção **Conjunto de dados do coletor** com o parâmetro **DWTableName**. Defina o valor desse parâmetro para `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]`.

    ![Copiar parâmetros do coletor](./media/tutorial-bulk-copy-portal/copy-sink-parameters.png)
11. Alterne para a guia **Configurações** e siga estas etapas: 

    1. Selecione **True** para **Habilitar preparo**.
    2. Selecione **AzureStorageLinkedService** como **Armazenar serviço vinculado da conta**.

        ![Habilitar preparo](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)
12. Para validar as configurações de pipeline, clique em **Validar**. Confirme se não houver nenhum erro de validação. Para fechar o **Relatório de validação do pipeline** clique em **>>**.

    ![Relatório de validação do pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-validation-report.png)

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Criar o pipeline GetTableListAndTriggerCopyData

Esse pipeline realiza duas etapas:

* Pesquisa a tabela do sistema do Banco de Dados SQL do Azure para obter a lista de tabelas a serem copiadas.
* Dispara o pipeline "IterateAndCopySQLTables" para fazer a cópia de dados propriamente dita.

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.

    ![Menu do novo pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. Na janela Propriedades, altere o nome do pipeline para **GetTableListAndTriggerCopyData**. 

    ![Nome do pipeline](./media/tutorial-bulk-copy-portal/second-pipeline-name.png)
3. Na caixa de ferramentas **Atividades**, expanda **Banco de Dados SQL** e arraste e solte a atividade de **Pesquisa** para a superfície do designer de pipeline e execute as seguintes etapas:

    1. Digite **LookupTableList** para **Nome**. 
    2. Digite **Recuperar a lista de tabelas do banco de dados SQL do Azure** para **Descrição**.

        ![Atividade de pesquisa - página geral](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
4. Alterne para a página **Configurações** e siga estas etapas:

    1. Selecione **AzureSqlDatabaseDataset** para **Conjunto de dados de origem**. 
    2. Selecione **Consulta** para **Usar consulta**. 
    3. Insira a seguinte consulta SQL para **Consulta**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    4. Desmarque a caixa de seleção para o campo **Somente primeira linha**.

        ![Atividade de pesquisa - página configurações](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
5. Arraste e solte a atividade **Executar pipeline** da caixa de ferramentas Atividades para a superfície do designer do pipeline e defina o nome como **TriggerCopy**.

    ![Atividade Executar pipeline - página geral](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
6. Alterne para a página **Configurações** e siga estas etapas: 

    1. Selecione **IterateAndCopySQLTables** para **Pipeline invocado**. 
    2. Expanda a seção **Avançado**. 
    3. Clique em **+ Novo** na seção **Parâmetros**. 
    4. Digite **tableList** para o parâmetro **nome**.
    5. Digite `@activity('LookupTableList').output.value` para o **valor** de parâmetro. Você está configurando a lista de resultados da atividade de Pesquisa como uma entrada para o segundo pipeline. A lista de resultados contém a lista de tabelas cujos dados precisam ser copiados para o destino. 

        ![Atividade Executar pipeline - página de configurações](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
7. **Conecte** a atividade de **Pesquisa** à atividade **Executar pipeline** arrastando a **caixa verde** associada à atividade de Pesquisa à esquerda da atividade Executar pipeline.

    ![Conecte as atividades de Pesquisa e Executar pipeline](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
8. Para validar o pipeline, clique em **Validar** na barra de ferramentas. Confirme se não houver nenhum erro de validação. Para fechar o **Relatório de validação do pipeline** clique em **>>**.

    ![Segundo pipeline - relatório de validação](./media/tutorial-bulk-copy-portal/second-pipeline-validation-report.png)
9. Para publicar as entidades (conjuntos de dados, pipelines, etc.) no serviço Data Factory, clique em **Publicar**. Aguarde até que a publicação seja bem-sucedida. 

    ![Botão Publicar](./media/tutorial-bulk-copy-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline

1. Confirme se a guia **GetTableListAndTriggerCopyData** está ativa. 
2. Clique em **Gatilho** e clique em **Disparar agora**. 

    ![Disparar agora](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Alterne para a guia **Monitorar**. Clique em **Atualizar** até que você veja as execuções para ambos os pipelines em sua solução. Continue atualizando a lista até que você veja o status **Bem-sucedido**. 

    ![Execuções de pipeline](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
2. Para exibir execuções de atividade associadas ao pipeline de GetTableListAndTriggerCopyData, clique no primeiro link no link de Ações para esse pipeline. Você deve ver duas execuções de atividade para essa execução de pipeline. 

    ![Execuções de atividade](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
3. Para exibir a saída da atividade de **Pesquisa**, clique no link da coluna **Saída** para essa atividade. Você pode maximizar e restaurar a janela **Saída**. Depois de revisar, clique em **X** para fechar a janela **Saída**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
4. Para alternar novamente para a exibição de **Execuções de pipeline**, clique no link **Pipelines** na parte superior. Clique no link **Visualizar execuções de atividade** (primeiro link na coluna **Ações**) para o pipeline **IterateAndCopySQLTables**. Você deve ver a saída conforme mostrado na imagem a seguir: Observe que há uma execução de atividade de **Cópia** para cada tabela na saída da atividade de **Pesquisa**. 

    ![Execuções de atividade](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
5. Confirme que os dados foram copiados para o SQL Data Warehouse de destino usado neste tutorial. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizou as seguintes etapas: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar serviços vinculados do Banco de Dados SQL do Azure, do SQL Data Warehouse do Azure e do Armazenamento do Azure.
> * Criar conjuntos de dados do Banco de Dados SQL do Azure e do SQL Data Warehouse do Azure.
> * Crie um pipeline para consultar as tabelas a serem copiadas e outro pipeline para executar a operação de cópia propriamente dita. 
> * Inicie uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Avance para o tutorial a seguir para saber mais sobre como copiar dados incrementalmente de uma origem para um destino:
> [!div class="nextstepaction"]
>[Copiar dados incrementalmente](tutorial-incremental-copy-portal.md)