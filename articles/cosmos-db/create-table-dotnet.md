---
title: "Início rápido: API de Tabela com .NET – Azure Cosmos DB | Microsoft Docs"
description: "Este guia de início rápido mostra como usar a API de Tabela do Azure Cosmos DB para criar um aplicativo com o Portal do Azure e o .NET"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quickstart connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: mimig
ms.openlocfilehash: 44637049dd5d6cfe353afe98427d843a0d4e403a
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="quickstart-build-a-table-api-app-with-net-and-azure-cosmos-db"></a>Início rápido: criar um aplicativo de API de Tabela com .NET e Azure Cosmos DB 

Este guia de início rápido mostra como usar o .NET e a [API de Tabela](table-introduction.md) do Azure Cosmos DB para criar um aplicativo por meio da clonagem de um exemplo do GitHub. Este guia de início rápido também mostra como criar uma conta do Azure Cosmos DB e como usar o Data Explorer para criar tabelas e entidades no Portal do Azure baseado na Web.

O BD Cosmos do Azure é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

> [!IMPORTANT] 
> Você precisa criar uma nova conta de API de tabela para trabalhar com os SDKs de API de tabela disponíveis. Não há suporte para contas de API de tabela criadas durante a versão prévia pelos SDKs disponíveis.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

Agora é possível adicionar dados à sua nova tabela usando o Data Explorer.

1. No Data Explorer, expanda **sample-table**, clique em **Entidades** e clique em **Adicionar Entidade**.

   ![Criar novas entidades no Data Explorer no portal do Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Agora adicione dados às caixas de valor PartitionKey e RowKey e clique em **Adicionar Entidade**.

   ![Definir a chave de partição e a chave de linha para uma nova entidade](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Agora, é possível adicionar mais entidades à tabela, editar as entidades ou consultar os dados no Data Explorer. Por meio do Data Explorer, também é possível dimensionar a taxa de transferência e adicionar procedimentos armazenados, funções definidas pelo usuário e gatilhos à tabela.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de Tabela do github, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para uma pasta para instalar o aplicativo de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Em seguida, abra o arquivo da solução TableStorage no Visual Studio. 

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. Isso permite que seu aplicativo se comunique com o banco de dados hospedado. 

1. No [Portal do Azure](http://portal.azure.com/), clique em **Cadeia de Conexão**. 

    Use os botões de cópia no lado direito da tela para copiar a CADEIA DE CONEXÃO PRIMÁRIA.

    ![Exiba e copie a CADEIA DE CONEXÃO PRIMÁRIA no painel Cadeia de Conexão](./media/create-table-dotnet/connection-string.png)

2. No Visual Studio, abra o arquivo App.config. 

3. Remova a marca de comentário da StorageConnectionString na linha 8 e comente a StorageConnectionString na linha 7, pois este tutorial não usa o Emulador de Armazenamento. As linhas 7 e 8 agora devem ser assim:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Cole o valor de CADEIA DE CONEXÃO PRIMÁRIA do portal no valor da StorageConnectionString na linha 8. Cole a cadeia de caracteres entre aspas. 

    > [!IMPORTANT]
    > Se o ponto de extremidade usa documents.azure.com, isso significa que você tem uma conta de versão prévia, e você precisa criar um [nova conta de API de tabela](#create-a-database-account) para trabalhar com o SDK de API de tabela geralmente disponível. 
    > 

    Agora, a linha 8 deve ser semelhante a:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Salve o arquivo App.config.

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Compilar e implantar o aplicativo

1. No Visual Studio, clique com o botão direito do mouse no projeto **TableStorage** em **Gerenciador de Soluções** e depois clique em **Gerenciar Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, digite *Microsoft.Azure.CosmosDB.Table*.

3. Com base nos resultados, instale a biblioteca **Microsoft.Azure.CosmosDB.Table**. Isso instala o pacote da API de Tabela do Azure Cosmos DB e todas as dependências.

4. Abra o BasicSamples.cs e adicione um ponto de interrupção à linha 30 e à linha 52.

5. Clique em CTRL + F5 para executar o aplicativo.

    A janela de console exibe os dados da tabela sendo adicionados ao novo banco de dados de tabela no Azure Cosmos DB. 
    
    Se você receber um erro sobre dependências, consulte [solução de problemas](table-sdk-dotnet.md#troubleshooting).

    Quando atingir o primeiro ponto de interrupção, volte para o Data Explorer no portal do Azure e expanda a tabela demonstração* e clique em **Entidades**. A guia **Entidades** à direita mostra a nova entidade que foi adicionada, observe que esse número de telefone para o usuário é 425-555-0101.
    
6. Feche a guia de Entidades no Data Explorer.
    
7. Continue a executar o aplicativo até o próximo ponto de interrupção.

    Quando você atinge o ponto de interrupção, retorne ao portal, clique em Entidades novamente para abrir a guia de Entidades e observe que o número de telefone foi atualizado para 425-555-0105.

8. Volta na janela do console, pressione CTRL + C para terminar a execução do aplicativo. 

    Agora, você pode voltar ao Data Explorer, adicionar ou modificar as entidades e consultar os dados.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do BD Cosmos do Azure, como criar uma tabela usando o Data Explorer e como executar um aplicativo.  Agora, você pode consultar os dados usando a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados de tabela para a API de Tabela](table-import.md)

