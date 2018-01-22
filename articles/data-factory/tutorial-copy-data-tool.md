---
title: Como copiar dados usando a ferramenta Copy Data do Azure | Microsoft Docs
description: Criar um Azure Data Factory e usar a ferramenta de Copy Data para copiar dados de armazenamento de Blobs do Azure para o banco de dados SQL do Azure.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>Como copiar dados do Blob do Azure para o Banco de Dados SQL do Azure usando a ferramenta Copy Data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 – Versão prévia](tutorial-copy-data-tool.md)

Neste tutorial, você usa o Portal do Azure para criar um data factory. Em seguida, você usa a ferramenta Copy Data para criar um pipeline que copia dados de um armazenamento de Blobs do Azure para um banco de dados SQL do Azure. 

> [!NOTE]
> - Se estiver conhecendo o Azure Data Factory agora, confira a [Introdução ao Azure Data Factory](introduction.md) para obter uma visão geral.
>
> - Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Use a ferramenta Copy Data para criar um pipeline
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>pré-requisitos

* **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o Armazenamento de Blobs como um armazenamento de dados de **origem**. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para conhecer as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de **coletor**. Se você não tiver um Banco de Dados SQL do Azure, veja o artigo [Criar um Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para conhecer as etapas para criar um.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu Blob do Azure e Banco de Dados SQL do Azure para o tutorial, executando as seguintes etapas:

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **inputEmp.txt** no disco.

    ```
    John|Doe
    Jane|Doe
    ```

2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) para criar o contêiner **adfv2tutorial** e carregar o arquivo **inputEmp.txt** no contêiner.

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

2. Permita que os serviços do Azure acessem o SQL Server. Confira se a configuração **Permitir acesso aos serviços do Azure** esteja definida como habilitado para o Azure SQL Server, permitindo que nele sejam gravados dados pelo serviço Data Factory. Para verificar e ativar essa configuração, faça as seguintes etapas:

    1. Clique no hub **Mais serviços** à esquerda e clique em **Servidores SQL**.
    2. Selecione seu servidor e clique em **Firewall** em **CONFIGURAÇÕES**.
    3. Na página **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Página de novo data factory](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outros locais/regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. Clique no bloco **Autor & Monitor** para iniciar a interface de usuário (IU) do Azure Data Factory em uma guia separada. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página de Introdução, clique no bloco **Copiar dados** para iniciar a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. Na página **Propriedades** da ferramenta Copy Data, especifique **CopyFromBlobToSqlPipeline** para o **Nome da tarefa** e clique em **Avançar**. A IU do Data Factory cria um pipeline com o nome especificado para o nome da tarefa. 

    ![Ferramenta Copy Data - página de propriedades](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **Armazenamento de Blobs do Azure** e clique em **Avançar**. Os dados de origem estão em um armazenamento de Blobs do Azure. 

    ![Página Armazenamento de dados de origem](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de Blobs do Azure**, execute as seguintes etapas: 
    1. Informe **AzureStorageLinkedService** no campo **nome da conexão**.
    2. Escolha seu **nome da conta de armazenamento** na lista suspensa.
    3. Clique em **Próximo**. 

        ![Especifique a conta de armazenamento de Blobs](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        Um serviço vinculado vincula um armazenamento de dados ou uma computação a um data factory. Nesse caso, você criou um serviço vinculado do armazenamento do Azure para vincular sua conta de armazenamento do Azure para o armazenamento de dados. O serviço vinculado tem as informações de conexão que os serviços do Data Factory usam para se conectar ao seu armazenamento de Blobs em tempo de execução. O conjunto de dados especifica o contêiner, pasta e arquivo (opcional) que contém os dados de origem. 
5. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:
    
    1. Navegue até a pasta **adfv2tutorial/input**.
    2. Escolha **inputEmp.txt**
    3. Clique em **Escolher**. Como alternativa, clique duas vezes em **inputEmp.txt**. 
    4. Clique em **Próximo**. 

    ![Escolha a pasta ou arquivo de entrada](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. Na página **Configurações de formato de arquivo**, observe que a ferramenta detecta automaticamente os delimitadores de linha e coluna e clique em **Avançar**. Você também pode visualizar os dados e exibir o esquema dos dados de entrada nesta página. 

    ![Página Configurações de formato de arquivo](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. Na página **Armazenamento de dados de destino**, selecione **Banco de Dados SQL do Azure** e clique em **Avançar**. 

    ![Página Armazenamento de dados de destino](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. Na página **Especificar o banco de dados SQL do Azure**, execute as seguintes etapas: 

    1. Especifique **AzureSqlDatabaseLinkedService** para o **Nome de conexão**. 
    2. Escolha seu SQL Server do Azure para o **Nome do servidor**.
    3. Escolha seu banco de dados SQL do Azure para o **Nome do banco de dados**.
    4. Especifique o nome do usuário para **Nome de usuário**.
    5. Especifique a senha do usuário para **Senha**.
    6. Clique em **Próximo**. 

        ![Especifique o banco de dados SQL do Azure](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        Um conjunto de dados deve ser associado um serviço vinculado. O serviço vinculado tem a cadeia de conexão que o serviço do Data Factory usa para se conectar ao seu banco de dados SQL do Azure em tempo de execução. O conjunto de dados especifica o contêiner, pasta e arquivo (opcional) para o qual os dados são copiados.
9.  Na página **Mapeamento de tabela**, selecione **[dbo].[emp]** e clique em **Avançar**. 

    ![Página Mapeamento de tabela](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. Na página **Mapeamento de esquema**, observe que a primeira e segunda colunas no arquivo de entrada são mapeadas para as colunas **FirstName** e **LastName** da tabela **emp**. 

    ![Página Mapeamento de esquema](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. Na página **Configurações**, clique em **Avançar**. 

    ![Página Configurações](./media/tutorial-copy-data-tool/settings-page.png)
12. Na página **Resumo** examine as configurações e clique em **Avançar**.

    ![Página Resumo](./media/tutorial-copy-data-tool/summary-page.png)
13. Na **página Implantação**, clique em **Monitor** para monitorar o pipeline (tarefa).

    ![Página Implantação](./media/tutorial-copy-data-tool/deployment-page.png)
14. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. Confira os links para exibir detalhes de execução da atividade e executar novamente o pipeline da coluna **Ações**. Clique em **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de pipeline](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Para ver as atividades em execução associadas com o pipeline de execução, clique em **Exibir atividades em execução** na coluna **Ações**. Há apenas uma atividade (atividade de cópia) no pipeline, então você vê apenas uma entrada. Para obter detalhes sobre a operação de cópia, clique em **Detalhes** (ícone de óculos) na coluna **Ações**. Para alternar novamente para a exibição de pipeline em execução, Clique em **Pipelines** na parte superior. Para atualizar o modo de exibição, clique em **Atualizar**. 

    ![Monitorar execuções de atividade](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Clique na guia **Editar** à esquerda para alternar para o modo de edição. Você pode atualizar os serviços vinculados, conjuntos de dados e pipelines criados pela ferramenta usando o editor. Clique em **Código** para exibir o código JSON associado à entidade aberta no editor. Para obter detalhes sobre essas entidades na IU do Data Factory, confira em [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

    ![Guia Editor](./media/tutorial-copy-data-tool/edit-tab.png)
17. Verifique se que os dados são inseridos na tabela **emp** no seu banco de dados SQL do Azure. 

    ![Verifique a saída SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, o pipeline copia os dados de um armazenamento de Blobs do Azure para um banco de dados SQL do Azure. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Use a ferramenta Copy Data para criar um pipeline
> * Monitore as execuções de pipeline e de atividade.

Avance para o tutorial a seguir para saber mais sobre a cópia de dados local para a nuvem: 

> [!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-data-tool.md)