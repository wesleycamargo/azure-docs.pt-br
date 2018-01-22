---
title: Copiar dados do SQL Server para o Armazenamento de Blobs usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de um armazenamento de dados local para a nuvem do Azure usando o tempo de execução integrado e auto-hospedado no Azure Data Factory."
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 7b734a76545dbcbddac3c7ad7beae60d662a9129
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Tutorial: Copiar os dados de um banco de dados de SQL Server local para um Armazenamento de Blobs do Azure
Neste tutorial, você usa a interface do usuário (IU) do Azure Data Factory para criar um pipeline de data factory que copia dados de um banco de dados do SQL Server local para o Armazenamento de Blobs do Azure. Você cria e usa um tempo de execução de integração auto-hospedado, o qual move dados entre locais e armazenamentos de dados da nuvem. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Azure Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Este artigo não fornece uma introdução detalhada do serviço de Data Factory. Para obter mais informações, consulte [Introdução ao Azure Data Factory](introduction.md). 

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um Integration Runtime auto-hospedado.
> * Criar serviços vinculados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e de Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Inicie uma execução de pipeline.
> * Monitore a execução de pipeline.

## <a name="prerequisites"></a>pré-requisitos
### <a name="azure-subscription"></a>Assinatura do Azure
Antes de começar, se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de data factory, a conta de usuário usada para fazer logon no Azure deve ter uma função de *colaborador* ou *proprietário* atribuída ou deve ser um *administrador* da assinatura do Azure. 

Para exibir as permissões que você tem com a assinatura, vá até o portal do Azure, selecione seu nome de usuário no canto superior direito e depois **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, consulte o artigo [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, você usa um banco de dados do SQL Server local como um armazenamento de dados de *fonte*. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server local (fonte) para um armazenamento de Blobs do Azure (coletor). Depois, crie uma tabela chamada **emp** no seu banco de dados do SQL Server e insira algumas entradas de exemplo na tabela. 

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado em seu computador, vá para [Baixar o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Conecte-se à sua instância do SQL Server usando suas credenciais. 

3. Criar um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Novo Banco de Dados**. 
 
4. Na janela **Novo Banco de Dados**, digite um nome para o banco de dados e selecione **OK**. 

5. Para criar a tabela **emp** e inserir alguns dados de exemplo nela, execute o seguinte script de consulta no banco de dados:

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

6. No modo de exibição de árvore, clique com o botão direito do mouse no banco de dados que você criou e selecione **Nova Consulta**.

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Neste tutorial, use uma conta de armazenamento do Azure de uso geral (especificamente o Armazenamento de Blobs do Azure) como um armazenamento de dados de destino/coletor. Se você não tiver uma conta de armazenamento de uso geral do Azure, consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account). O pipeline no data factory criado neste tutorial copia dados do banco de dados do SQL Server local (fonte) para este armazenamento de Blobs do Azure (coletor). 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Você usa o nome e a chave da sua conta de armazenamento do Azure neste tutorial. Obtenha o nome e a chave da sua conta de armazenamento fazendo o seguinte: 

1. Entre no [portal do Azure](https://portal.azure.com) com sua conta e senha do Azure. 

2. No painel esquerdo, selecione **Mais serviços**, filtre usando a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisar conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento (se necessário) e, em seguida, selecione a sua conta de armazenamento. 

4. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

    ![Obter nome e chave da conta de armazenamento](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e depois cole-os no Bloco de Notas ou outro editor para uso posterior neste tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial 
Nesta seção, você cria um contêiner de blobs chamado **adftutorial** no seu armazenamento de Blobs do Azure. 

1. Na janela **Conta de armazenamento**, alterne para **Visão geral** e depois selecione **Blobs**. 

    ![Selecione a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Na janela **Serviço Blob**, selecione **Contêiner**. 

    ![Botão Adicionar contêiner](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Na janela **Novo contêiner**, na caixa **Nome**, insira **adftutorial** e depois selecione **OK**. 

    ![Inserir o nome do contêiner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Na lista de contêineres, selecione **adftutorial**.  

    ![Selecione o contêiner](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Mantenha aberta a janela **contêiner** para **adftutorial**. Você o usa para verificar a saída no final do tutorial. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma.

    ![Janela Contêiner](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você criará um data factory e iniciará a interface do usuário do Azure Data Factory para criar um pipeline no data factory. 

1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Página de novo data factory](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
        Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Apenas os locais com suporte são mostrados na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.      
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada. 


## <a name="create-a-pipeline"></a>Criar uma pipeline

1. Na página **Introdução**, clique em **Criar pipeline**. Um pipeline é criado automaticamente para você. Você vê o pipeline na exibição de árvore e abre o editor. 

   ![Página Introdução](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. Na guia **Geral** da janela **Propriedades** na parte inferior, insira **SQLServerToBlobPipeline** em **Nome**.

   ![Nome do pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. Na caixa de ferramentas **Atividades**, expanda **Fluxo de Dados**e arraste e solte a atividade **Cópia** para a superfície de design do pipeline. Defina o nome da atividade como **CopySqlServerToAzureBlobActivity**.

   ![Nome da atividade](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. Na janela Propriedades, alterne para a guia **Origem** e, em seguida, clique em **+ Novo**.

   ![Novo conjunto de dados de origem - botão](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. Na janela **Novo Conjunto de Dados**, procure **SQL Server**, selecione **SQL Server** e clique em **Concluir**. Você verá uma nova guia intitulada **SqlServerTable1**. Consulte também o conjunto de dados **SqlServerTable1** no modo de exibição de árvore à esquerda. 

   ![Selecionar SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. Na guia **Geral** da janela Propriedades, insira **SqlServerDataset** como **Nome**.
    
   ![Conjunto de Dados de Origem - nome](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. Alterne para a guia **Conexões** e, em seguida, clique em **+ Novo**. Você pode criar uma conexão com o repositório de dados de origem (banco de dados do SQL Server) nesta etapa. 

   ![Conjunto de Dados de Origem - botão nova conexão](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. Na janela **Novo Serviço Vinculado**, clique em **Novo tempo de execução de integração**. Nesta seção, você cria um Integration Runtime auto-hospedado e o associa a um computador local com o banco de dados do SQL Server. O Integration Runtime auto-hospedado é o componente que copia dados do banco de dados do SQL Server em seu computador para o armazenamento de Blobs do Azure. 

   ![Novo tempo de execução de integração](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. Na janela **Instalação do Integration Runtime**, selecione **Rede Privada** e clique em **Avançar**. 

   ![Selecionar rede privada](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. Insira um nome para o tempo de execução de integração e clique em **Avançar**.  
    
   ![Integration Runtime - nome](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. Clique em **Clique aqui para iniciar a instalação expressa para este computador** na seção **Opção 1: instalação Expressa**. 

   ![Clique no link de instalação Expressa](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. Na janela **Instalação Expressa do Microsoft Integration Runtime (auto-hospedado)**, clique em **Fechar**. 

   ![Instalação do tempo de execução de integração - êxito](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. No navegador da Web, na janela **Configuração de Tempo de Execução de Integração**, clique em **Concluir**. Você deve ter voltado para a janela **Novo Serviço Vinculado**.

   ![Configuração do tempo de execução de integração - conclusão](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. Na janela **Novo serviço vinculado** execute as seguintes etapas:

    1. Insira **SqlServerLinkedService** como **Nome**.
    2. Confirme que o tempo de execução de integração auto-hospedado criado anteriormente é mostrado para **Conectar por meio da integração de tempo de execução**.
    3. Especifique o nome do SQL Server para **Nome do servidor**. 
    4. Especifique o nome do banco de dados com a tabela **emp** para o campo **Nome do banco de dados**. 
    5. Selecione o **tipo de autenticação** adequado que o Data Factory deve usar para se conectar ao seu banco de dados do SQL Server. 
    6. Insira o **nome de usuário** e a **senha**. Se precisar usar um caractere de barra invertida (\\) em sua conta de usuário e nome de servidor, use o caractere de escape à frente (\\). Por exemplo, use *mydomain\\\\myuser*. 
    7. Clique em **Testar conectividade**. Siga esta etapa para confirmar que o serviço Data Factory pode se conectar ao banco de dados do SQL Server usando o tempo de execução de integração auto-hospedado criado por você. 
    8. Para salvar um serviço vinculado, clique em **Salvar**.

       ![Serviço vinculado do SQL Server - configurações](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. Você deve ter voltado para a janela com o conjunto de dados de origem aberto. Na **Conexão** da janela **Propriedades**, siga estas etapas: 

    1. Confirme que você vê **SqlServerLinkedService** como **Serviço vinculado**. 
    2. Selecione **[dbo].[emp]** como **Tabela**.

        ![Conjunto de Dados de Origem - informações de conexão](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. Alterne para a guia com p SQLServerToBlobPipeline (ou) clique em **SQLServerToBlobPipeline** no modo de exibição de árvore. 

    ![Guia Pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. Alterne para a guia **Coletor** na janela **Propriedades** e clique em **+ Novo**. 

    ![Conjunto de dados do coletor- botão Novo](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. Na janela **Novo serviço vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. Você vê uma nova guia aberta para o conjunto de dados. Você também pode ver o conjunto de dados na exibição de árvore. 

    ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. Insira **AzureBlobDataset** como **Nome**.

    ![Conjunto de dados do coletor - nome](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. Alterne para a guia **Conexão** na janela **Propriedades** e clique em **+Novo** para o **Serviço vinculado**. 

    ![Novo serviço vinculado - botão](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. Na janela **Novo serviço vinculado** execute as seguintes etapas:

    1. Insira **AzureStorageLinkedService** como o **Nome**.
    2. Selecione sua conta de armazenamento do Azure como o **Nome da conta de armazenamento**. 
    3. Teste a conexão à sua conta de armazenamento do Azure clicando em **Testar conectividade**.
    4. Clique em **Salvar**.

        ![Serviço vinculado do Armazenamento do Azure - configurações](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  Você deve ter voltado para a janela com o conjunto de dados do coletor aberto. Na guia **Conexão**, siga estas etapas: 

        1. Confirme se **AzureStorageLinkedService** está selecionado como **Serviço vinculado**.
        2. Insira **adftutorial/fromonprem** como a parte da **pasta** do **Caminho do arquivo**. Se a pasta de saída não existir no contêiner adftutorial, o serviço Data Factory cria automaticamente a pasta de saída.
        3. Insira `@CONCAT(pipeline().RunId, '.txt')` como a parte **nome de arquivo** do **Caminho do arquivo**.

            ![Conjunto de dados do coletor - conexão](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. Alterne para a guia com o pipeline aberto (ou) clique no **pipeline** no **modo de exibição de árvore**. Confirme se **AzureBlobDataset** está selecionado para **Conjunto de Dados do Coletor**. 

    ![Conjunto de dados do coletor selecionado ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. Para validar as configurações de pipeline, clique em validar na barra de ferramentas para o pipeline. Feche o **Relatório de Validação de Pipe** clicando em **X** no canto direito. 

    ![Validar o pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. Publique entidades que você criou para o serviço do Azure Data Factory clicando em **Publicar**.

    ![Botão Publicar](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. Aguarde até que você veja o pop-up **Publicado com êxito**. Você também pode verificar o status da publicação clicando no link **Mostrar Notificações** à esquerda. Feche a janela de notificações clicando em **X**. 

    ![Publicado com êxito](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline
Clique em **Gatilho** na barra de ferramentas para o pipeline e clique em **Disparar agora**.

![Disparar Agora](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Alterne para a guia **Monitorar**. Você verá o pipeline que disparou manualmente na etapa anterior. 

    ![Execuções de pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Para ver as atividades em execução associadas com o pipeline de execução, clique em **Exibir atividades em execução** na coluna **Ações**. Você vê somente as execuções de atividade, já que há apenas uma atividade no pipeline. Para ver detalhes sobre a operação de cópia, clique em **Detalhes** (ícone de óculos) na coluna **Ações**. Você pode alternar novamente para o modo de execução do pipeline clicando no link **Pipelines** na parte superior.

    ![Execuções de atividade](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída chamada *fromonprem* no contêiner de blobs `adftutorial`. Confirme que você vê o arquivo *dbo.emp.txt* na pasta de saída. 

1. No portal do Azure, na janela de contêiner **adftutorial**, selecione **Atualizar** para ver a pasta de saída.

    ![Pasta de saída criada](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Selecione `fromonprem` na lista de pastas. 
3. Confirme que você vê um arquivo com o nome `dbo.emp.txt`.

    ![Arquivo de saída](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>Próximas etapas
O pipeline nesse exemplo copia dados de uma localização para outra no Armazenamento de Blobs do Azure. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um Integration Runtime auto-hospedado.
> * Criar serviços vinculados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e de Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Inicie uma execução de pipeline.
> * Monitore a execução de pipeline.

Para obter uma lista dos armazenamentos de dados com suporte pelo Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber mais sobre copiar dados em massa de uma origem para um destino, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)
