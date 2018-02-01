---
title: "Copiar várias tabelas incrementalmente usando o Azure Data Factory | Microsoft Docs"
description: "Neste tutorial, você deve criar um pipeline do Azure Data Factory que copie incrementalmente os dados delta de várias tabelas em um banco de dados do SQL Server local para um banco de dados SQL do Azure."
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
ms.date: 01/20/2018
ms.author: jingwang
ms.openlocfilehash: c79bce401b0f1d67d7955f4c97a5dfac5008be0d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Carregar incrementalmente os dados de várias tabelas no SQL Server para um banco de dados SQL do Azure
Neste tutorial, você pode criar um Azure Data Factory com um pipeline que carrega dados delta de várias tabelas do SQL Server local para um banco de dados SQL do Azure.    

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Prepare os armazenamentos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Crie um Integration Runtime auto-hospedado.
> * Instalar o Integration Runtime. 
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Revise os resultados.
> * Adicionar ou atualizar dados nas tabelas de origem.
> * Executar novamente e monitorar o pipeline.
> * Examinar os resultados finais.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Azure Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível, consulte a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Visão geral
Aqui estão as etapas importantes ao criar essa solução: 

1. **Selecione a coluna de marca-d'água**.
    
    Selecione uma coluna para cada tabela no armazenamento de dados de origem, que pode ser usada para identificar os registros novos ou atualizados para cada execução. Normalmente, os dados nessa coluna selecionada (por exemplo, ID ou last_modify_time) seguem crescendo quando linhas são criadas ou atualizadas. O valor máximo dessa coluna é usado como uma marca-d'água.

2. **Prepare um armazenamento de dados para armazenar o valor de marca-d'água**.   
    
    Neste tutorial, você deve armazenar o valor de marca-d'água em um banco de dados SQL.

3. **Crie um pipeline com as seguintes atividades**: 
    
    a. Crie uma atividade ForEach que itere em uma lista de nomes de tabela de origem passada como um parâmetro para o pipeline. Para cada tabela de origem, ele chama as seguintes atividades para executar o carregamento delta para essa tabela.

    b. Crie duas atividades de pesquisa. Use a primeira atividade de Pesquisa para recuperar o último valor de marca-d'água. Use a segunda atividade de Pesquisa para recuperar o novo valor de marca-d'água. Esses valores de marca-d'água são passados para a atividade de Cópia.

    c. Crie uma atividade de cópia que copie linhas do armazenamento de dados de origem com o valor da coluna de marca-d'água maior do que o antigo valor de marca-d'água e menor que o novo valor de marca-d'água. Em seguida, ela copia os dados delta do armazenamento de dados de origem para um Armazenamento de Blobs do Azure como um novo arquivo.

    d. Crie uma atividade de StoredProcedure que atualize o valor de marca-d'água para o pipeline que for executado da próxima vez. 

    A seguir está diagrama da solução de alto nível: 

    ![Carregar dados incrementalmente](media\tutorial-incremental-copy-multiple-tables-portal\high-level-solution-diagram.png)


Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>pré-requisitos
* **SQL Server**. Neste tutorial, você utiliza um banco de dados do SQL Server local como o armazenamento de dados de origem. 
* **Banco de dados SQL do Azure**. Use um banco de dados SQL do Azure como o armazenamento de dados do coletor. Se você não tiver um banco de dados SQL, consulte [Criar um banco de dados SQL](../sql-database/sql-database-get-started-portal.md) para saber as etapas para criar um. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Criar tabelas de origem no banco de dados do SQL Server

1. Abra o SQL Server Management Studio e conecte-se ao banco de dados do SQL Server local.

2. No **Gerenciador de Servidores**, clique com o botão direito do mouse no banco de dados e escolha **Nova consulta**.

3. Execute o seguinte comando SQL no banco de dados para criar as tabelas `customer_table` e `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Criar tabelas de destino no banco de dados SQL do Azure
1. Abra o SQL Server Management Studio e conecte-se ao seu banco de dados SQL do Azure.

2. No **Gerenciador de Servidores**, clique com o botão direito do mouse no banco de dados e escolha **Nova consulta**.

3. Execute o seguinte comando SQL no banco de dados SQL para criar as tabelas `customer_table` e `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-sql-database-to-store-the-high-watermark-value"></a>Crie outra tabela no banco de dados SQL para armazenar o valor de marca d'água alta
1. Execute o comando SQL a seguir no banco de dados SQL para criar uma tabela chamada `watermarktable` para armazenar o valor de marca-d'água: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Insira valores de marca d'água iniciais para ambas as tabelas de origem na tabela de marca d'água.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-sql-database"></a>Criar um procedimento armazenado no banco de dados SQL 

Execute o comando a seguir para criar um procedimento armazenado no banco de dados SQL. Esse procedimento armazenado atualiza o valor de marca d'água após cada execução de pipeline. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures"></a>Criar tipos de dados e procedimentos armazenados adicionais
Execute a consulta a seguir para criar dois tipos de dados e dois procedimentos armazenados no banco de dados SQL. Eles são usados para mesclar os dados das tabelas de origem nas tabelas de destino.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFMultiIncCopyTutorialDF** como o **nome**. 
      
     ![Página de novo data factory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o seguinte erro, altere o nome de data factory (por exemplo, yournameADFMultiIncCopyTutorialDF) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
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

    ![implantando bloco data factory](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada.
11. Na página de introdução da interface do usuário do Azure Data Factory, clique em **Criar pipeline** (ou) alterne para a guia **Editar**. 

   ![Página Introdução](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado
Conforme você move dados de um armazenamento de dados em uma rede privada (local) para um armazenamento de dados do Azure, instale um RI (tempo de execução de integração) auto-hospedado em seu ambiente local. O IR auto-hospedado move dados entre sua rede privada e o Azure. 

1. Clique em **Conexões** na parte inferior do painel esquerdo e alterne para **Integration Runtimes** na janela **Conexões**. 

   ![Guia Conexões](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
2. Em **Integration Runtimes** , clique em **+ Novo**. 

   ![Novo tempo de execução de integração - botão](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
3. Na janela **Instalação do Integration Runtime**, selecione **Executar atividades de movimentação e distribuição de dados para cálculos externos** e clique em **Avançar**. 

   ![Selecionar tipo de tempo de execução de integração](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
4. Selecione **Rede Privada** e clique em **Avançar**. 

   ![Selecionar rede privada](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
5. Insira **MySelfHostedIR** como **Nome** e clique em **Avançar**. 

   ![Nome do IR auto-hospedado](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
10. Clique em **Clique aqui para iniciar a instalação expressa para este computador** na seção **Opção 1: instalação Expressa**. 

   ![Clique no link de instalação Expressa](./media/tutorial-incremental-copy-multiple-tables-portal/click-exress-setup.png)
11. Na janela **Instalação Expressa do Microsoft Integration Runtime (auto-hospedado)**, clique em **Fechar**. 

   ![Instalação do tempo de execução de integração - êxito](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
12. No navegador da Web, na janela **Configuração de Tempo de Execução de Integração**, clique em **Concluir**. 

   ![Configuração do tempo de execução de integração - conclusão](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
17. Confirme que você vê **MySelfHostedIR** na lista de tempos de execução de integração.

       ![Tempos de execução de integração - lista](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Nesta seção, você cria serviços vinculados para seu banco de dados do SQL Server local e banco de dados SQL. 

### <a name="create-the-sql-server-linked-service"></a>Criar um serviço vinculado do SQL Server
Nesta etapa, você vincula seu banco de dados do SQL Server local ao data factory.

1. Na janela **Conexões**, alterne da guia **Integration Runtime** para a guia **Serviços Associados** e clique em **+ Novo**.

    ![Botão Novo Serviço Vinculado](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. Na janela **Novo Serviço Vinculado**, selecione **SQL Server** e clique em **Continuar**. 

    ![Selecionar SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
3. Na janela **Novo serviço vinculado** execute as seguintes etapas:

    1. Insira **SqlServerLinkedService** como **Nome**. 
    2. Selecione **MySelfHostedIR** para **Conectar por meio do tempo de execução de integração**. Esta é uma etapa **importante**. O tempo de execução de integração padrão não pode se conectar a um armazenamento de dados local. Use o tempo de execução de integração auto-hospedado criado anteriormente. 
    3. Como **Nome do servidor**, digite o nome do computador que tem o banco de dados do SQL Server.
    4. Como **Nome do banco de dados**, digite o nome do banco de dados no SQL Server que contém os dados de origem. Você criou uma tabela e dados inseridos neste banco de dados como parte dos pré-requisitos. 
    5. Como **Tipo de autenticação**, selecione o **tipo de autenticação** que você deseja usar para se conectar ao banco de dados. 
    6. Como **Nome de usuário**, digite o nome do usuário que tem acesso ao banco de dados do SQL Server. Se precisar usar um caractere de barra (`\`) em sua conta de usuário e nome de servidor, use o caractere de escape (`\`). Um exemplo é `mydomain\\myuser`.
    7. Para **Senha**, insira a **senha** do usuário. 
    8. Para testar se o data factory pode se conectar ao banco de dados do SQL Server, clique em **Testar conexão**. Corrija os erros até que a conexão seja bem-sucedida. 
    9. Para salvar um serviço vinculado, clique em **Salvar**.

        ![Serviço vinculado do SQL Server - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Criar o serviço vinculado do Banco de Dados SQL do Azure
Na última etapa, você deve criar um serviço vinculado para vincular o banco de dados do SQL Server de origem ao data factory. Nesta etapa, você vincula o banco de dados SQL do Azure de destino/coletor ao data factory. 

1. Na janela **Conexões**, alterne da guia **Integration Runtime** para a guia **Serviços Associados** e clique em **+ Novo**.

    ![Botão Novo Serviço Vinculado](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. Na janela **Novo Serviço Vinculado**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 
3. Na janela **Novo serviço vinculado** execute as seguintes etapas:

    1. Insira **AzureSqlDatabaseLinkedService** para o **Nome**. 
    3. Como **Nome do servidor**, selecione o nome do servidor SQL do Azure na lista suspensa. 
    4. Como **Nome do banco de dados**, selecione o banco de dados SQL do Azure em que você criou customer_table e project_table como parte dos pré-requisitos. 
    6. Como **Nome de usuário**, digite o nome do usuário que tem acesso ao banco de dados SQL do Azure. 
    7. Para **Senha**, insira a **senha** do usuário. 
    8. Para testar se o data factory pode se conectar ao banco de dados do SQL Server, clique em **Testar conexão**. Corrija os erros até que a conexão seja bem-sucedida. 
    9. Para salvar um serviço vinculado, clique em **Salvar**.

        ![Serviço vinculado do SQL do Azur - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
10. Confirme que você vê os dois serviços vinculados na lista. 
   
    ![Dois serviços vinculados](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar a fonte de dados, o destino de dados e o local para armazenar a marca d'água.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

   ![Menu Novo conjunto de dados](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. Na janela **Novo Conjunto de Dados**, selecione **SQL Server** e clique em **Concluir**. 

   ![Selecionar SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
3. Você vê uma nova guia aberta no navegador da Web para configurar o conjunto de dados. Você também vê um conjunto de dados no modo de exibição de árvore. Na guia **Geral** da janela Propriedades, na parte inferior, insira **SourceDataset** como **Nome**. 

   ![Conjunto de dados de origem - nome](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
4. Alterne para a guia **Conexão** na janela Propriedades e selecione **SqlServerLinkedService** como **Serviço vinculado**. Você não seleciona uma tabela aqui. A atividade de Cópia no pipeline usa uma consulta SQL para carregar os dados em vez de carregar a tabela inteira.

   ![Conjunto de Dados de Origem - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de coletor
1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

   ![Menu Novo conjunto de dados](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Concluir**. 

   ![Selecione um Banco de Dados SQL do Azure](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. Você vê uma nova guia aberta no navegador da Web para configurar o conjunto de dados. Você também vê um conjunto de dados no modo de exibição de árvore. Na guia **Geral** da janela Propriedades para o conjunto de dados, na parte inferior, insira **SinkDataset** como **Nome**.

   ![Conjunto de Dados do Coletor - geral](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
4. Alterne para a guia **Conexão** na janela Propriedades e selecione **AzureSqlLinkedService** como **Serviço vinculado**. 

   ![Conjunto de Dados do Coletor - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
5. Alterne para a guia **Parâmetros** na janela Propriedades e execute as seguintes etapas: 

    1. Clique em **Novo** na seção **Criar/atualizar parâmetros**. 
    2. Digite **SinkTableName** como **nome** e **Cadeia de Caracteres** como **tipo**. Este conjunto de dados usa **SinkTableName** como um parâmetro. O parâmetro SinkTableName é definido pelo pipeline dinamicamente no tempo de execução. A atividade ForEach no pipeline itera por meio de uma lista de nomes de tabela e passa o nome da tabela para esse conjunto de dados em cada iteração.
    3. Digite `@{dataset().SinkTableName}` para a propriedade **tableName** na seção **Propriedades parametrizadas**. Use o valor passado para parâmetro **SinkTableName**a fim de inicializar a propriedade **tableName** do conjunto de dados. 

       ![Conjunto de Dados do Coletor - propriedades](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)

### <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para uma marca-d'água
Nesta etapa, você deve criar um conjunto de dados para armazenar um valor de marca d'água alta. 

1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

   ![Menu Novo conjunto de dados](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Concluir**. 

   ![Selecione um Banco de Dados SQL do Azure](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. Na guia **Geral** da janela Propriedades para o conjunto de dados, na parte inferior, insira **WatermarkDataset** como **Nome**.
4. Alterne para a guia **Conexão** e siga estas etapas: 

    1. Selecione **AzureSqlDatabaseLinkedService** para **Serviço vinculado**.
    2. Selecione **[dbo].[watermarktable]** para **Tabela**.

       ![Conjunto de Dados de Marca d'Água - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Criar uma pipeline
O pipeline usa uma lista de nomes de tabela como um parâmetro. A atividade ForEach itera na lista de nomes de tabela e executa as seguintes operações: 

1. Use a atividade de Pesquisa para recuperar o valor de marca d'água antigo (o valor inicial ou o que foi usado na última iteração).

2. Use a atividade de Pesquisa para recuperar o novo valor de marca d'água (o valor máximo da coluna da marca d'água na tabela de origem).

3. Use a atividade de Cópia para copiar os dados entre esses dois valores de marca d'água do banco de dados de origem para o banco de dados de destino.

4. Use a atividade StoredProcedure para atualizar o valor da marca d'água antiga a ser usado na primeira etapa da próxima iteração. 

### <a name="create-the-pipeline"></a>Criar o pipeline

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.

    ![Novo Pipeline - menu](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
2. Na guia **Geral** da janela **Propriedades**, digite **IncrementalCopyPipeline** como **Nome**. 

    ![Nome do pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
3. Na janela **Propriedades**, siga estas etapas: 

    1. Clique em **+ Novo**. 
    2. Digite **tableList** para o parâmetro **nome**. 
    3. Selecione **Objeto** como o parâmetro **Tipo**.

    ![Parâmetros de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
4. Arraste e solte a atividade de **ForEach** da caixa de ferramentas **Atividades** para a superfície do designer de pipeline. Na guia **Geral** da janela **Propriedades**, insira **IterateSQLTables**. 

    ![Atividade ForEach - nome](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
5. Alterne para a guia **Configurações** na janela **Propriedades** e digite `@pipeline().parameters.tableList` como **Itens**. A atividade ForEach itera por meio de uma lista de tabelas e executa a operação de cópia incremental. 

    ![Atividade ForEach - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
6. Selecione a atividade **ForEach** no pipeline, se já não estiver selecionada. Clique no botão **Editar (ícone de lápis)**.

    ![Atividade ForEach - edição](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
7. Arraste e solte a atividade **Pesquisar** na caixa de ferramentas **Atividades** e digite **LookupOldWaterMarkActivity** como **Nome**.

    ![Primeira Atividade de Pesquisa - nome](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
8. Alterne para a guia **Configurações** na janela **Propriedades** e execute as seguintes etapas: 

    1. Selecione **WatermarkDataset** como **Conjunto de Dados de Origem**.
    2. Selecione **Consulta** para **Usar consulta**. 
    3. Insira a seguinte consulta SQL para **Consulta**. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Primeira Atividade de Pesquisa - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
9. Arraste e solte a atividade **Pesquisar** na caixa de ferramentas **Atividades** e digite **LookupNewWaterMarkActivity** como **Nome**.
        
    ![Segunda Atividade de Pesquisa - nome](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
10. Alterne para a guia **Configurações** .

    1. Selecione **SourceDataset** para o **Conjunto de dados de origem**. 
    2. Selecione **Consulta** para **Usar consulta**.
    3. Insira a seguinte consulta SQL para **Consulta**.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Segunda Atividade de Pesquisa - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
11. Arraste e solte a atividade de **Cópia** da caixa de ferramentas **Atividades** e digite **IncrementalCopyActivity** como **Nome**. 

    ![Atividade de cópia - nome](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
12. Conecte as atividades de **Pesquisa** à atividade de **Cópia**, uma por uma. Para se conectar, comece arrastando a caixa **verde** anexada à atividade de **Pesquisa** e solte-a na atividade de **Cópia**. Solte o botão do mouse quando visualizar a cor da borda da atividade de Cópia ficar **azul**.

    ![Conetar atividades de Pesquisa à atividade de Cópia](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
13. Selecione a atividade de **Cópia** no pipeline. Alterne para a guia **Origem** na janela **Propriedades**. 

    1. Selecione **SourceDataset** para o **Conjunto de dados de origem**. 
    2. Selecione **Consulta** para **Usar consulta**. 
    3. Insira a seguinte consulta SQL para **Consulta**.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Atividade de cópia - configurações da fonte](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
14. Alterne para a guia **Coletor** e selecione **SinkDataset** para o campo **Conjunto de Dados do Coletor**. 
        
    ![Atividade de cópia - configurações do coletor](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
15. Alterne para a guia **Parâmetros** e execute as seguintes etapas:

    1. Na propriedade **Nome do Procedimento Armazenado no Coletor**, digite `@{item().StoredProcedureNameForMergeOperation}`.
    2. Na propriedade **do Tipo de Tabela do Coletor**, digite `@{item().TableType}`.
    3. Na seção **Conjunto de Dados do Coletor**, no parâmetro **SinkTableName**, digite `@{item().TABLE_NAME}`.

        ![Atividade de cópia - parâmetros](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
16. Arraste e solte a atividade de **Procedimento armazenado** da caixa de ferramentas **Atividades** para a superfície do designer de pipeline. Conecte a atividade de **Cópia** à atividade de **Procedimento Armazenado**. 

    ![Atividade de cópia - parâmetros](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
17. Selecione a atividade **Procedimento Armazenado** no pipeline e digite **StoredProceduretoWriteWatermarkActivity** como **Nome** na guia **Geral** da janela **Propriedades**. 

    ![Atividade de procedimento armazenado - nome](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
18. Alterne para a guia **Conta SQL** e selecione **AzureSqlDatabaseLinkedService** para o **Serviço Vinculado**.

    ![Atividade de procedimento armazenado - Conta SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
19. Alterne para a guia **Procedimento armazenado** e execute as etapas a seguir:

    1. Digite `sp_write_watermark` como **Nome do procedimento armazenado**. 
    2. Use o botão **Novo** para adicionar os seguintes parâmetros: 

        | NOME | type | Valor | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Cadeia de caracteres | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Atividade de Procedimento armazenado - configurações de procedimento armazenado](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
20. No painel esquerdo, clique em **Publicar**. Essa ação publica as entidades que você criou para o serviço Data Factory. 

    ![Botão Publicar](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
21. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as notificações, clique no link **Mostrar Notificações**. Feche a janela de notificações clicando em **X**.

    ![Mostrar notificações](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Na barra de ferramentas do pipeline, clique em **Gatilho** e clique em **Disparar Agora**.     

    ![Disparar agora](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
2. Na janela **Execução de Pipeline**, digite o valor a seguir para o parâmetro **tableList** e clique em **Concluir**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

    ![Argumentos de Execução do Pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você pode ver a execução do pipeline disparado pelo **gatilho manual**. Clique no botão **Atualizar** para atualizar a lista. Os links na coluna **Ação** permitem a exibição de atividades em execução associadas ao pipeline de execução e executar novamente o pipeline. 

    ![Execuções de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Clique no link **Exibir Execuções da atividade** na coluna **Ações**. Você vê todas as execuções de atividade associadas à execução do pipeline selecionada. 

    ![Execuções de atividade](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Revise os resultados
No SQL Server Management Studio, execute as seguintes consultas no banco de dados SQL de destino para verificar se os dados foram copiados das tabelas de origem para as tabelas de destino: 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados. 

## <a name="add-more-data-to-the-source-tables"></a>Adicionar mais dados às tabelas de origem

Execute a consulta a seguir feita no banco de dados do SQL Server de origem para atualizar uma linha existente na customer_table. Insera uma nova linha em project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Executar novamente o pipeline
1. Na janela do navegador da Web, alterne para a guia **Editar** à esquerda. 
2. Na barra de ferramentas do pipeline, clique em **Gatilho** e clique em **Disparar Agora**.   

    ![Disparar agora](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. Na janela **Execução de Pipeline**, digite o valor a seguir para o parâmetro **tableList** e clique em **Concluir**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você pode ver a execução do pipeline disparado pelo **gatilho manual**. Clique no botão **Atualizar** para atualizar a lista. Os links na coluna **Ação** permitem a exibição de atividades em execução associadas ao pipeline de execução e executar novamente o pipeline. 

    ![Execuções de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Clique no link **Exibir Execuções da atividade** na coluna **Ações**. Você vê todas as execuções de atividade associadas à execução do pipeline selecionada. 

    ![Execuções de atividade](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Examine os resultados finais
No SQL Server Management Studio, execute as seguintes consultas no banco de dados de destino para verificar se os dados atualizados/novos foram copiados das tabelas de origem para as tabelas de destino. 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Observe os novos valores de **Name** e **LastModifytime** para **PersonID** para o número 3. 

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Observe que a entrada **NewProject** foi adicionada a project_table. 

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados.
     
## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizou as seguintes etapas: 

> [!div class="checklist"]
> * Prepare os armazenamentos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Criar um Integration Runtime (IR) auto-hospedado.
> * Instalar o Integration Runtime.
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Revise os resultados.
> * Adicionar ou atualizar dados nas tabelas de origem.
> * Executar novamente e monitorar o pipeline.
> * Examinar os resultados finais.

Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Carregar incrementalmente os dados do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure usando a tecnologia Controle de Alterações](tutorial-incremental-copy-change-tracking-feature-portal.md)


