---
title: Copiar dados do SQL Server para o Armazenamento de Blobs usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de um armazenamento de dados local para a nuvem usando o tempo de execução integrado e auto-hospedado no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: f408d24a5957061bf03d340a555b87bdc6b2aacc
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304128"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Copie os dados de um banco de dados do SQL Server local para um Armazenamento de Blobs do Azure
Neste tutorial, você usa a interface do usuário (IU) do Azure Data Factory para criar um pipeline de data factory que copia dados de um banco de dados do SQL Server local para o Armazenamento de Blobs do Azure. Você cria e usa um tempo de execução de integração auto-hospedado, o qual movimenta os dados entre armazenamentos de dados locais e da nuvem.

> [!NOTE]
> Este artigo não fornece uma introdução detalhada ao Data Factory. Para saber mais, confira [Introdução ao Data Factory](introduction.md). 

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um tempo de execução de integração auto-hospedado.
> * Criar serviços vinculados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e de Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorar a execução de pipeline.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Assinatura do Azure
Antes de começar, se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de data factory, a conta de usuário usada para entrar no Azure deve ter uma função de *Colaborador* ou *Proprietário* atribuída, ou deve ser de um *administrador* da assinatura do Azure. 

Para exibir as permissões que você tem na assinatura, acesse o portal do Azure. Selecione seu nome de usuário no canto superior direito e depois selecione **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, confira [Gerenciar o acesso usando o portal do Azure e o RBAC](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, você usa um banco de dados do SQL Server local como um armazenamento de dados de *fonte*. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server local (origem) para um Armazenamento de blobs (coletor). Depois você cria uma tabela chamada **emp** no seu banco de dados do SQL Server e insere algumas entradas de exemplo na tabela. 

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado em seu computador, vá para [Baixar o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Conecte-se à sua instância do SQL Server usando suas credenciais. 

1. Crie um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Novo Banco de Dados**. 
1. Na janela **Novo Banco de Dados**, digite um nome para o banco de dados e selecione **OK**. 

1. Para criar a tabela **emp** e inserir alguns dados de exemplo nela, execute o seguinte script de consulta no banco de dados:

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

1. No modo de exibição de árvore, clique com o botão direito do mouse no banco de dados que você criou e selecione **Nova Consulta**.

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Neste tutorial, você usa uma conta de armazenamento do Azure para fins gerais (especificamente o Armazenamento de blobs) como armazenamento de dados de destino/coletor. Se você não tiver uma conta de armazenamento de uso geral do Azure, consulte [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md). O pipeline no data factory que você cria neste tutorial copia dados do banco de dados do SQL Server local (origem) para este armazenamento de Blobs do Azure (coletor). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Use o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, realize as etapas a seguir: 

1. Entre no [Portal do Azure](https://portal.azure.com) com seu nome de usuário e senha do Azure. 

1. No painel esquerdo, selecione **Mais serviços**. Filtre usando a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisa na conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento, se necessário. Em seguida, selecione sua conta de armazenamento. 

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

    ![Chaves de acesso](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e depois cole-os no Bloco de Notas ou outro editor para uso posterior neste tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial 
Nesta seção, você cria um contêiner de blobs chamado **adftutorial** no seu Armazenamento de blobs. 

1. Na janela **Conta de armazenamento**, vá para **Visão geral** e depois selecione **Blobs**. 

    ![Selecione a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Na janela **Serviço Blob**, selecione **Contêiner**. 

    ![Botão Contêiner](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. Na janela **Novo contêiner**, em **Nome**, digite **adftutorial**. Depois, selecione **OK**. 

    ![Janela Novo contêiner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Na lista de contêineres, selecione **adftutorial**.

    ![Seleção de contêiner](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

1. Mantenha aberta a janela **contêiner** para **adftutorial**. Use-a para verificar a saída no final do tutorial. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma.

    ![Janela Contêiner](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você cria um data factory e inicia a interface do usuário do Data Factory para criar um pipeline no data factory. 

1. Abra o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. No menu à esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**.
   
   ![Criação do novo data factory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
1. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**. 
   
     ![Página de novo data factory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)

O nome do data factory deve ser *globalmente exclusivo*. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).

   ![Novo nome do data factory](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
1. Selecione a **assinatura** do Azure na qual deseja criar o data factory.
1. Em **Grupo de Recursos**, use uma das seguintes etapas:
   
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

      - Selecione **Criar novo**e insira o nome de um grupo de recursos.
        
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).
1. Em **Versão**, selecione **V2**.
1. Em **Local**, selecione o local para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo Data Factory podem estar em outras regiões.
1. Selecione **Fixar no painel**. 
1. Selecione **Criar**.
1. No painel, o bloco com o seguinte status é exibido: **Implantando o Data Factory**:

    ![Bloco Como implantar o Data Factory](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
1. Depois que a criação estiver concluída, a página **Data Factory** será exibida conforme mostrado na imagem:
   
    ![Página inicial do data factory](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
1. Selecione o bloco **Criar e Monitorar** para iniciar a interface do usuário do Data Factory em uma guia separada. 


## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página **Introdução**, selecione **Criar pipeline**. Um pipeline é criado automaticamente para você. Você vê o pipeline no modo de exibição de árvore e o editor é aberto. 

   ![Página Introdução](./media/tutorial-hybrid-copy-portal/get-started-page.png)

1. Na guia **Geral** na parte inferior da janela **Propriedades**, insira **SQLServerToBlobPipeline** em **Nome**.

   ![Nome do pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. Na caixa de ferramentas **Atividades**, expanda **DataFlow**. Arraste e solte a atividade de **Cópia** na superfície de design do pipeline. Defina o nome da atividade como **CopySqlServerToAzureBlobActivity**.

   ![Nome da atividade](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)

1. Na janela **Propriedades**, vá para a guia **Origem** e selecione **+ Novo**.

   ![Guia Origem](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)

1. Na janela **Novo Conjunto de Dados**, procure **SQL Server**. Selecione **SQL Server**e selecione **Concluir**. Você verá uma nova guia intitulada **SqlServerTable1**. Você também pode ver o conjunto de dados **SqlServerTable1** no modo de exibição de árvore à esquerda. 

   ![Seleção do SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)

1. Na guia **Geral** na parte inferior da janela **Propriedades**, insira **SqlServerDataset** em **Nome**.

   ![Nome do conjunto de dados de origem](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)

1. Vá para a guia **Conexão** e selecione **+ Novo**. Você pode criar uma conexão com o repositório de dados de origem (banco de dados do SQL Server) nesta etapa. 

   ![Conexão com o conjunto de dados de origem](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)

1. Na janela **Novo Serviço Vinculado**, adicione **Nome** como **SqlServerLinkedService**. Selecione **Novo** em **Conectar por meio do tempo de execução de integração**. Nesta seção, você cria um Integration Runtime auto-hospedado e o associa a um computador local com o banco de dados do SQL Server. O tempo de execução de integração auto-hospedado é o componente que copia dados do banco de dados do SQL Server em seu computador para o armazenamento de Blobs. 

   ![Novo tempo de execução de integração](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)

1. Na janela **Instalação do Tempo de Execução de Integração**, selecione **Rede Privada** e clique em **Avançar**. 

   ![Seleção de rede privada](./media/tutorial-hybrid-copy-portal/select-private-network.png)

1. Insira um nome para o tempo de execução de integração e selecione **Avançar**.

    ![Nome do tempo de execução de integração](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)

1. Selecione **Clique aqui para iniciar a instalação expressa para este computador** em **Opção 1: Instalação expressa**. 

    ![Clique no link Instalação expressa](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)

1. Na janela **Instalação Expressa do Tempo de Execução de Integração (auto-hospedado)**, selecione **Fechar**. 

    ![Instalação expressa do tempo de execução de integração (auto-hospedado)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Na janela **Novo Serviço Vinculado**, verifique se o **Tempo de Execução de Integração** criado acima foi selecionado em **Conectar por meio do tempo de execução de integração**. 

    ![](./media/tutorial-hybrid-copy-portal/select-integration-runtime.png)

1. Na janela **Novo Serviço Vinculado**, siga as etapas a seguir:

    a. Em **Nome**, insira **SqlServerLinkedService**.

    b. Confirme que o tempo de execução de integração auto-hospedado criado anteriormente é mostrado em **Conectar por meio do tempo de execução de integração**.

    c. Em **Nome do servidor**, insira o nome da instância do SQL Server. 

    d. Em **Nome do banco de dados**, insira o nome do banco de dados com a tabela **emp**.

    e. Em **tipo de autenticação**, selecione o tipo de autenticação adequado que o Data Factory deve usar para se conectar ao seu banco de dados do SQL Server.

    f. Em **Nome de usuário** e **Senha**, digite o nome de usuário e a senha. Se precisar usar um caractere de barra invertida (\\) em sua conta de usuário e nome de servidor, use o caractere de escape à frente (\\). Por exemplo, use *mydomain\\\\myuser*.

    g. Selecione **Testar conexão**. Siga esta etapa para confirmar que o Data Factory pode se conectar ao banco de dados do SQL Server usando o tempo de execução de integração auto-hospedado criado por você.

    h. Para salvar o serviço vinculado, selecione **Concluir**.

       

1. Você deve ter voltado para a janela com o conjunto de dados de origem aberto. Na guia **Conexão** da janela **Propriedades**, siga as etapas a seguir: 

    a. Em **Serviço vinculado**, confirme que você vê **SqlServerLinkedService** como Serviço vinculado.

    b. Em **Tabela**, selecione **[dbo].[emp]**.

    ![Conjunto de dados de origem - informações de conexão](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)

1. Vá para a guia com **SQLServerToBlobPipeline**, ou selecione **SQLServerToBlobPipeline** no modo de exibição de árvore. 

    ![Guia Pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)

1. Vá para a guia **Coletor** na parte inferior da janela **Propriedades** e selecione **+ Novo**. 

    ![Guia Coletor](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)

1. Na janela **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure**. Em seguida, selecione **Concluir**. Você verá uma nova guia aberta para o conjunto de dados. Você também verá o conjunto de dados no modo de exibição de árvore. 

    ![Seleção de armazenamento de blobs](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)

1. Em **Nome**, insira **AzureBlobDataset**.

    ![Conjunto de dados do coletor - nome](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)

1. Vá para a guia **Conexão** na parte inferior da janela **Propriedades**. Ao lado de **Serviço vinculado**, selecione **+ Novo**. 

    ![Botão Novo serviço vinculado](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)

1. Na janela **Novo Serviço Vinculado**, siga as etapas a seguir:

    a. Em **Nome**, insira **AzureStorageLinkedService**.

    b. Em **Nome da conta de armazenamento**, selecione sua conta de armazenamento.

    c. Para testar a conexão com a conta de armazenamento, selecione **Testar conectividade**.

    d. Selecione **Salvar**.

    ![Configurações do serviço vinculado do armazenamento](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 

1. Você deve ter voltado para a janela com o conjunto de dados do coletor aberto. Na guia **Conexão**, siga estas etapas: 

    a. Confirme se **AzureStorageLinkedService** está selecionado como **Serviço vinculado**.

    b. Para a parte da **pasta**/ **Diretório** do **Caminho do arquivo**, insira **adftutorial/fromonprem**. Se a pasta de saída não existir no contêiner adftutorial, o serviço Data Factory criará essa pasta automaticamente.

    c. Para a parte **nome de arquivo** do **caminho do arquivo**, selecione **Adicionar conteúdo dinâmico**.   

    ![valor de nome de arquivo dinâmico](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Adicione `@CONCAT(pipeline().RunId, '.txt')`, selecione **Concluir**. Isso irá renomear o arquivo para PipelineRunID.txt. 

    ![expressão dinâmica para resolução de nomes de arquivo](./media/tutorial-hybrid-copy-portal/add-dynamic-file-name.png)

    ![Conexão com o conjunto de dados do coletor](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)

1. Alterne para a guia com o pipeline aberto ou selecione o pipeline no modo de exibição de árvore. Confirme se **AzureBlobDataset** está selecionado no **Conjunto de Dados do Coletor**. 

    ![Conjunto de dados do coletor selecionado](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)

1. Para validar as configurações de pipeline, selecione **Validar** na barra de ferramentas do pipeline. Para fechar o **Relatório de Validação de Pipe**, selecione **Fechar**. 

    ![Validar o pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)

1. Para publicar as entidades criadas no Data Factory, selecione **Publicar Tudo**.

    ![Botão Publicar](./media/tutorial-hybrid-copy-portal/publish-button.png)

1. Aguarde até que você veja o pop-up **Publicado com êxito**. Para verificar o status da publicação, selecione o link **Mostrar Notificações** à esquerda. Para fechar a janela de notificação, selecione **Fechar**. 

    ![Publicado com êxito](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)


## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline
Selecione **Gatilho** na barra de ferramentas do pipeline e selecione **Disparar Agora**.

![Disparar uma execução de pipeline](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Vá para a guia **Monitorar**. Você vê o pipeline que disparou manualmente na etapa anterior. 

    ![Monitorar execuções de pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. Para exibir as execuções de atividade associadas à execução do pipeline, clique no link **Exibir Execuções de Atividade** na coluna **Ações**. Você vê somente as execuções de atividade, já que há apenas uma atividade no pipeline. Para ver detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. Para voltar para a exibição de **Execuções de pipeline**, selecione **Pipelines** na parte superior.

    ![Monitorar execuções de atividade](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída chamada *fromonprem* no contêiner de blobs `adftutorial`. Confirme que você vê o arquivo *[pipeline().RunId].txt* na pasta de saída. 

![Confirmar nome do arquivo de saída](./media/tutorial-hybrid-copy-portal/sink-output.png)


## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de um local para outro no Armazenamento de blobs. Você aprendeu como:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um tempo de execução de integração auto-hospedado.
> * Criar serviços vinculados de armazenamento e do SQL Server. 
> * Criar conjuntos de dados do SQL Server e de armazenamento de blobs.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorar a execução de pipeline.

Para obter uma lista dos armazenamentos de dados com suporte do Data Factory, confira [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Para aprender a copiar dados em massa de uma origem para um destino, consulte o seguinte tutorial:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)
