---
title: "Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio | Microsoft Docs"
description: "Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Visual Studio."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 01a6f060e6ae800b0de930c7c46ed60f73b530ac
ms.openlocfilehash: 58aae152e49a4e90822f98c9cf5ee7aad067ffa8


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Este tutorial mostra como criar e monitorar um data factory do Azure usando o Visual Studio. O pipeline no data factory usa uma Atividade de Cópia para copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL.

Eis as etapas executadas como parte deste tutorial:

1. Criar dois serviços vinculados: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. 
   
    O AzureStorageLinkedService1 vincula um armazenamento do Azure e AzureSqlLinkedService1 vincula um banco de dados SQL do Azure à data factory: **ADFTutorialDataFactoryVS**. Os dados de entrada do pipeline residem em um contêiner de blob no armazenamento de blobs do Azure e os dados de saída são armazenados em uma tabela no banco de dados SQL do Azure. Portanto, adicione esses dois repositórios de dados como serviços vinculados à data factory.
2. Crie dois conjuntos de dados: **InputDataset** e **OutputDataset**, que representam os dados de entrada/saída que são armazenados nos repositórios de dados. 
   
    Para InputDataset, você deve especificar o contêiner de blob que contém um blob com a fonte de dados. Para OutputDataset, você deve especificar a tabela SQL que armazena os dados de saída. Você também pode especificar outras propriedades, como estrutura, disponibilidade e política.
3. Criar um pipeline chamado **ADFTutorialPipeline** no ADFTutorialDataFactoryVS. 
   
    O pipeline tem uma **Atividade de Cópia** , que copia os dados de entrada do blob do Azure na tabela de saída do SQL Azure. A atividade de cópia realiza a movimentação de dados no Azure Data Factory. A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia. 
4. Crie um data factory denominado **VSTutorialFactory**. Implante o data factory e todas as entidades de Data Factory (serviços vinculados, tabelas e pipeline).    

## <a name="prerequisites"></a>Pré-requisitos
1. Leia o artigo [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e concluir as etapas de **pré-requisito** . 
2. Você deve ser um **administrador da assinatura do Azure** para poder publicar entidades de Data Factory no Azure Data Factory.  
3. Você deve ter os seguintes itens instalados no seu computador: 
   * Visual Studio 2013 ou Visual Studio 2015
   * Baixe o SDK do Azure para Visual Studio 2013 ou Visual Studio de 2015. Navegue até a [Página de Download do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na seção **.NET**.
   * Baixe o plug-in Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Você também pode atualizar o plug-in, executando as seguintes etapas: no menu, clique em **Ferramentas** -> **Extensões e Atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory** -> **Atualizar**.

## <a name="create-visual-studio-project"></a>Criar um projeto do Visual Studio
1. Inicie o **Visual Studio 2013**. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**. Você deverá ver a caixa de diálogo **Novo Projeto** .  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto Vazio de Data Factory**. Se você não vir o modelo DataFactory, feche o Visual Studio, instale o SDK do Azure para Visual Studio 2013 e reabra o Visual Studio.  
   
    ![Caixa de diálogo Novo projeto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Insira um **nome** para o projeto, o **local**, um nome para a **solução** e clique em **OK**.
   
    ![Gerenciador de Soluções](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Confira [repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver todas as fontes e coletores com suporte da Atividade de Cópia. Confira os [serviços vinculados de computação](data-factory-compute-linked-services.md) para ver uma lista dos serviços de computação com suporte do Data Factory. Neste tutorial, você não usa nenhum serviço de computação. 

Nesta etapa, você cria dois serviços vinculados: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. O serviço vinculado AzureStorageLinkedService1 vincula uma Conta de Armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à data factory: **ADFTutorialDataFactory**. 

### <a name="create-the-azure-storage-linked-service"></a>Criar o serviço de armazenamento do Azure vinculado
1. Clique com o botão direito do mouse em **Serviços Vinculados** no Gerenciador de Soluções, aponte para **Adicionar** e clique em **Novo Item**.      
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Vinculado de Armazenamento do Azure** na lista e clique em **Adicionar**. 
   
    ![Novo serviço vinculado](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Substitua `<accountname>` e `<accountkey>`* pelo nome da conta de armazenamento do Azure e sua chave. 
   
    ![Serviço vinculado de armazenamento do Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Salve o arquivo **AzureStorageLinkedService1.json** .

> Confira [Mover dados de/para Blobs do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre as propriedades JSON.
> 
> 

### <a name="create-the-azure-sql-linked-service"></a>Criar um serviço vinculado do SQL do Azure
1. Clique com o botão direito no nó **Serviços Vinculados** no **Gerenciador de Soluções** novamente, aponte para **Adicionar** e clique em **Novo Item**. 
2. Desta vez, selecione **Serviço Vinculado SQL do Azure** e clique em **Adicionar**. 
3. No arquivo **AzureSqlLinkedService1.json**, substitua `<servername>`, `<databasename>`, `<username@servername>` e `<password>` pelos nomes do SQL server do Azure, do banco de dados, da conta de usuário e pela senha.    
4. Salve o arquivo **AzureSqlLinkedService1.json** . 

> [!NOTE]
> Confira [Mover dados de/para Banco de Dados SQL](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter detalhes sobre as propriedades JSON.
> 
> 

## <a name="create-datasets"></a>Criar conjuntos de dados
Na etapa anterior, você criou os serviços vinculados **AzureStorageLinkedService1** e **AzureSqlLinkedService1** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure ao data factory: **ADFTutorialDataFactory**. Nesta etapa, você define dois conjuntos de dados, **InputDataset** e **OutputDataset**, que representam os dados de entrada/saída armazenados nos repositórios de dados referidos por AzureStorageLinkedService1 e AzureSqlLinkedService1, respectivamente. Para InputDataset, você deve especificar o contêiner de blob que contém um blob com a fonte de dados. Para OutputDataset, você deve especificar a tabela SQL que armazena os dados de saída.

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Nesta etapa, você criará um conjunto de dados denominado **InputDataset** que aponta para um contêiner de blob no Armazenamento do Azure representado pelo serviço vinculado **AzureStorageLinkedService1**. Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados com suporte no momento. 

1. Clique com o botão direito do mouse em **Tabelas** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**.
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Blob do Azure** e clique em **Adicionar**.   
3. Substitua o texto JSON pelo texto a seguir e salve o arquivo **AzureBlobLocation1.json** . 

  ```json   
  {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
  }
  ``` 
    Observe os seguintes pontos: 
   
   * O **tipo** do conjunto de dados foi definido como **AzureBlob**.
   * **linkedServiceName** é definido como **AzureStorageLinkedService**. Você criou esse serviço vinculado na Etapa 2.
   * **folderPath** é definido como o contêiner **adftutorial**. Você também pode especificar o nome de um blob dentro da pasta usando a propriedade **fileName** . Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como um entrada de dados.  
   * O **tipo** do formato é definido como **TextFormat**
   * Há dois campos no arquivo de texto, **FirstName** e **LastName**, separados por uma vírgula (**columnDelimiter**)    
   * A **availability** é definida como **hourly** (a **frequency** definida como **hour** e o **interval** é definido como **1**). Portanto, o Data Factory procurará dados de entrada a cada hora na pasta raiz do contêiner de blob (**adftutorial**) especificado. 
   
   Se você não especificar um **fileName** para um conjunto de dados de **entrada**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn.
   
   Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.&lt;Guid\&gt;.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).
   
   Para definir **folderPath** e **fileName** dinamicamente com base no horário **SliceStart**, use a propriedade **partitionedBy**. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2016-09-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2016/09/20 e o fileName é definido como 08.csv. 
  
    ```json   
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ```
            
> [!NOTE]
> Confira [Mover dados de/para Blobs do Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obter detalhes sobre as propriedades JSON.
> 
> 

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Nesta etapa, você cria um conjunto de dados de saída denominado **OutputDataset**. Esse conjunto de dados aponta para uma tabela SQL no banco de dados SQL do Azure representada por **AzureSqlLinkedService1**. 

1. Clique com o botão direito do mouse em **Tabelas** no **Gerenciador de Soluções** novamente, aponte para **Adicionar** e clique em **Novo Item**.
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **SQL do Azure** e clique em **Adicionar**. 
3. Substitua o texto JSON pelo JSON a seguir e salve o arquivo **AzureSqlTableLocation1.json** .

    ```json
    {
     "name": "OutputDataset",
     "properties": {
       "structure": [
         {
           "name": "FirstName",
           "type": "String"
         },
         {
           "name": "LastName",
           "type": "String"
         }
       ],
       "type": "AzureSqlTable",
       "linkedServiceName": "AzureSqlLinkedService1",
       "typeProperties": {
         "tableName": "emp"
       },
       "availability": {
         "frequency": "Hour",
         "interval": 1
       }
     }
    }
    ```
   
    Observe os seguintes pontos: 
   
   * O **tipo** do conjunto de dados foi definido como **AzureSQLTable**.
   * **linkedServiceName** é definido como **AzureSqlLinkedService** (você criou esse serviço vinculado na Etapa 2).
   * **tablename** está definido como **emp**.
   * Há três colunas (**ID**, **FirstName** e **LastName**) na tabela emp no banco de dados. ID é uma coluna de identidade. Portanto, você precisa especificar somente **FirstName** e **LastName** aqui.
   * A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1**).  O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.

> [!NOTE]
> Confira [Mover dados de/para Banco de Dados SQL](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter detalhes sobre as propriedades JSON.
> 
> 

## <a name="create-pipeline"></a>Criar um pipeline
Você criou tabelas e serviços vinculados de entrada/saída até o momento. Agora, você cria um pipeline com uma **Atividade de Cópia** para copiar dados do blob do Azure ao banco de dados SQL do Azure. 

1. Clique com o botão direito do mouse em **Pipelines** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**.  
2. Selecione **Copiar Pipeline de Dados** na caixa de diálogo **Adicionar Novo Item** e clique em **Adicionar**. 
3. Substitua o JSON pelo JSON a seguir e salve o arquivo **CopyActivity1.json** .

    ```json   
    {
     "name": "ADFTutorialPipeline",
     "properties": {
       "description": "Copy data from a blob to Azure SQL table",
       "activities": [
         {
           "name": "CopyFromBlobToSQL",
           "type": "Copy",
           "inputs": [
             {
               "name": "InputDataset"
             }
           ],
           "outputs": [
             {
               "name": "OutputDataset"
             }
           ],
           "typeProperties": {
             "source": {
               "type": "BlobSource"
             },
             "sink": {
               "type": "SqlSink",
               "writeBatchSize": 10000,
               "writeBatchTimeout": "60:00:00"
             }
           },
           "Policy": {
             "concurrency": 1,
             "executionPriorityOrder": "NewestFirst",
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2015-07-12T00:00:00Z",
       "end": "2015-07-13T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
   Observe os seguintes pontos:
   
   * Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**.
   * A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**.
   * Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor.
   
   Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Você pode especificar apenas a parte da data e ignorar a parte de hora do valor de data/hora. Por exemplo, "2016-02-03", que é equivalente a "2016-02-03T00:00:00Z"
   
   Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. A hora **final** é opcional, mas nós a usaremos neste tutorial. 
   
   Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**.
   
   No exemplo anterior, há 24 fatias de dados, pois cada fatia de dados é produzida a cada hora.

## <a name="publishdeploy-data-factory-entities"></a>Publicar/implantar entidades de data factory
Nesta etapa, você publica as entidades de Data Factory (serviços vinculados, conjuntos de dados e pipeline) criadas anteriormente. Você também pode especificar o nome do novo data factory a ser criado para manter essas entidades.  

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Publicar**. 
2. Se a caixa de diálogo **Entrar na sua conta da Microsoft** for exibida, insira as credenciais da conta com a assinatura do Azure e clique em **entrar**.
3. Você deve ver a caixa de diálogo a seguir:
   
   ![Caixa de diálogo Publicar](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Na página Configurar data factory, execute as seguintes etapas: 
   
   1. Selecione a opção **Criar Nova Data Factory** .
   2. Digite **VSTutorialFactory** para **Nome**.  
      
      > [!IMPORTANT]
      > O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber um erro sobre o nome do data factory durante a publicação, altere o nome (por exemplo, seunomeVSTutorialFactory) e tente publicar novamente. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.        
      > 
      > 
   3. Selecione a assinatura do Azure no campo **Assinatura** .
      
      > [!IMPORTANT]
      > Se você não vir nenhuma assinatura, verifique se está conectado usando uma conta que seja administrador ou coadministrador da assinatura.  
      > 
      > 
   4. Selecione o **grupo de recursos** para o data factory a ser criado. 
   5. Selecione a **região** do data factory. Apenas as regiões com suporte pelo serviço Data Factory são mostradas na lista suspensa.
   6. Clique em **Avançar** para alternar para a página **Publicar Itens**.
      
       ![Configurar página de data factory](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. Na página **Publicar Itens**, verifique se todas as entidades de Data Factories estão selecionadas e clique em **Avançar** para alternar para a página **Resumo**.
   
   ![Publicar página de item](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Examine o resumo e clique em **Avançar** para iniciar o processo de implantação e exibir o **Status da Implantação**.
   
   ![Publicar página de resumo](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. Na página **Status da Implantação** , você deve ver o status do processo de implantação. Clique em Concluir depois que a implantação tiver terminado.
 
   ![Página Status da implantação](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Observe os seguintes pontos: 

* Se você receber o erro: "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**", siga um destes procedimentos e tente publicar novamente: 
  
  * No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory. 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado. 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.
* O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.

> [!IMPORTANT]
> Para criar instâncias do Data Factory, você precisa ser administrador/coadministrador da assinatura do Azure
> 
> 

## <a name="summary"></a>Resumo
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o Visual Studio para criar a data factory, os serviços vinculados, os conjuntos de dados e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:  

1. Foi criado um **data factory**do Azure.
2. Foram criados **serviços vinculados**:
   1. Um serviço vinculado do **Armazenamento do Azure** para vincular sua conta do Armazenamento do Azure que contém os dados de entrada.     
   2. Um serviço vinculado do **SQL Azure** para vincular o banco de dados SQL do Azure que contém os dados de saída. 
3. Foram criados **conjuntos de dados**que descrevem os dados de entrada e de saída para os pipelines.
4. Foi criado um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como coletor. 

## <a name="use-server-explorer-to-view-data-factories"></a>Usar o Gerenciador de Servidores para exibir os data factories
1. No **Visual Studio**, clique em **Exibir** no menu e em **Gerenciador de Servidores**.
2. Na janela Gerenciador de Servidores, expanda **Azure** e expanda **Data Factory**. Se **Entrar no Visual Studio** for exibido, insira a **conta** associada à sua assinatura do Azure e clique em **Continuar**. Insira a **senha** e clique em **Entrar**. O Visual Studio tenta obter informações sobre todos os data factories do Azure em sua assinatura. Você verá o status da operação na janela **Lista de Tarefas de Data Factory** .

    ![Gerenciador de Servidores](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Clique com o botão direito do mouse em uma data factory e selecione Exportar Data Factory para Novo Projeto para criar um projeto do Visual Studio com base em uma data factory existente.

    ![Exportar data factory para um projeto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Atualizar ferramentas de data factory para o Visual Studio
Para atualizar as ferramentas da Azure Data Factory para o Visual Studio, execute as seguintes etapas:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**. 
2. Selecione **Atualizações** no painel esquerdo e selecione **Galeria do Visual Studio**.
3. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se você não vir essa entrada, você já tem a versão mais recente das ferramentas. 

Confira [Monitorar os conjuntos de dados e o pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) para obter instruções sobre como usar o portal do Azure para monitorar o pipeline e os conjuntos de dados que você criou neste tutorial.

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
|:--- |:--- |
| [Atividades de movimentação de dados](data-factory-data-movement-activities.md) |Este artigo fornece informações detalhadas sobre a Atividade de Cópia utilizada neste tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory |
| [Conjunto de dados](data-factory-create-datasets.md) |Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory. |
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. |




<!--HONumber=Dec16_HO3-->


