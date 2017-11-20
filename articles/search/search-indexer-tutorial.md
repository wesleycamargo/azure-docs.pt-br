---
title: "Tutorial para indexação de bancos de dados SQL do Azure no Azure Search | Microsoft Docs"
description: "Rastrear um banco de dados SQL do Azure para extrair dados pesquisáveis e preencher um índice do Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: c5b7d5a5f20a4a70cbbbe43e33fdf65f12cb8e7d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Como rastrear um banco de dados SQL do Azure usando indexadores do Azure Search

Este tutorial mostra como configurar um indexador para extrair dados pesquisáveis de um banco de dados SQL do Azure de exemplo. [Indexadores](search-indexer-overview.md) são componentes do Azure Search que rastreiam as fontes de dados externas, preenchendo um [índice de pesquisa](search-what-is-an-index.md) com conteúdo. De todos os indexadores, o indexador do banco de dados SQL do Azure é mais usado. 

Ser proficiente na configuração do indexador é útil porque ele simplifica a quantidade de código que você precisa escrever e manter. Em vez de preparar e enviar por push um conjunto de dados JSON compatíveis com esquema, você pode anexar um indexador para uma fonte de dados, fazer o indexador extrair dados e inseri-lo em um índice e, opcionalmente, executar o indexador em um agendamento recorrente para acompanhar as alterações na fonte subjacente.

Neste tutorial, usando as [Bibliotecas de cliente .NET do Azure Search](https://aka.ms/search-sdk) e um aplicativo de console .NET Core, você realiza as seguintes tarefas:

> [!div class="checklist"]
> * Baixa e configura a solução
> * Adiciona informações de serviço de pesquisa às configurações do aplicativo
> * Prepara um conjunto de dados externo no banco de dados SQL do Azure 
> * Analisa as definições de índice e indexador no código de exemplo
> * Executa o código do indexador para importar dados
> * Pesquisar o índice
> * Exibe configuração do indexador no portal

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/free/). 

* Um serviço de Azure Search. Para obter ajuda sobre como configurar um serviço, confira [Criar um serviço de pesquisa](search-create-service-portal.md).

* Um banco de dados SQL do Azure fornecendo a fonte de dados externa usada por um indexador. A solução de exemplo fornece um arquivo de dados do SQL para criar a tabela.

* Visual Studio 2017. Você pode usar a versão gratuita do [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

> [!Note]
> Se você estiver usando o serviço do Azure Search gratuito, você está limitado a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Não se esqueça de deixar um espaço em seu serviço para aceitar os novos recursos.

## <a name="download-the-solution"></a>Baixar a solução

A solução de indexador usada neste tutorial é de uma coleção de exemplos do Azure Search distribuída em um único download principal. A solução usada para este tutorial é a *DotNetHowToIndexers*.

1. Vá para [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started) no repositório de GitHub de exemplos do Azure.

2. Clique em **Clonar ou baixar** > **Baixar ZIP**. Por padrão, o arquivo vai para a pasta de Downloads.

3. Em **Explorador de Arquivos** > **Downloads**, clique no arquivo com o botão direito do mouse e escolha **Extrair tudo**.

4. Desative as permissões de somente leitura. Clique com o botão direito do mouse no nome da pasta > **Propriedades** > **Geral** e, em seguida, desmarque o atributo **Somente leitura** para a pasta, as subpastas e os arquivos atuais.

5. No **Visual Studio 2017**, abra a solução *DotNetHowToIndexers.sln*.

6. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó pai superior Solução > **Restaurar Pacotes Nuget**.

## <a name="set-up-connections"></a>Configurar as conexões
As informações de conexão para os serviços necessários são especificadas no arquivo **appsettings.json** da solução. 

No Gerenciador de Soluções, abra **appsettings.json** para que você possa preencher cada configuração, usando as instruções neste tutorial.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Obter o nome de serviço de pesquisa e a chave de API do administrador

Você pode encontrar o ponto de extremidade de serviço de pesquisa e a chave no portal. Uma chave fornece acesso às operações de serviço. As chaves de administração permitem acesso de gravação, necessário para criar e excluir objetos como índices e indexadores, em seu serviço.

1. Entre no [portal do Azure](https://portal.azure.com/) e localize os [serviços de pesquisa para sua assinatura](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Abrir a página de serviço.

3. Na parte superior, localize o nome do serviço na página principal. Na captura de tela a seguir, é *azs-tutorial*.

   ![Nome do serviço](./media/search-indexer-tutorial/service-name.png)

4. Copie e cole-o como a primeira entrada em **appsettings.json** no Visual Studio.

  > [!Note]
  > Um nome de serviço é parte do ponto de extremidade que inclui search.windows.net. Se você estiver curioso, é possível ver a URL completa em **Conceitos básicos** na página Visão geral. A URL é parecida com este exemplo: https://your-service-name.search.windows.net

5. À esquerda, em **Configurações** > **Chaves**, copie uma das chaves de administração e cole-a como a segunda entrada em **appsettings.json**. As chaves são cadeias de caracteres alfanuméricas geradas para o serviço durante o provisionamento e necessárias para acesso autorizado a operações de serviço. 

  Depois de adicionar as duas configurações, o arquivo deve ficar semelhante a este exemplo:

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>Preparar uma fonte de dados externa

Nesta etapa, crie uma fonte de dados externa que pode ser rastreada por um indexador. O arquivo de dados para este tutorial é *hotels.sql*, fornecido na pasta da solução \DotNetHowToIndexers. 

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

Você pode usar o portal do Azure e o arquivo *hotels.sql* de exemplo para criar o conjunto de dados no Banco de Dados SQL do Azure. O Azure Search consome conjuntos de linhas bidimensionais, como aqueles gerados em uma exibição ou consulta. O arquivo SQL na solução de exemplo cria e preenche uma única tabela.

O exercício a seguir pressupõe que não há nenhum servidor ou banco de dados existentes e instrui você a criar os dois na etapa 2. Opcionalmente, se você tiver um recurso existente, pode adicionar a tabela hotéis, começando na etapa 4.

1. Entre no [Portal do Azure](https://portal.azure.com/). 

2. Clique em **Novo** > **Banco de Dados SQL** para criar um banco de dados, um servidor e um grupo de recursos. Você pode usar os padrões e o tipo de preço com o valor mais baixo. Uma vantagem de criar um servidor é que você pode especificar um nome de usuário e senha do administrador, necessários para criar e carregar tabelas em uma etapa posterior.

   ![Nova página de banco de dados](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Clique em **Criar** para implantar o novo servidor e o banco de dados. Aguarde a implantação do servidor e do banco de dados.

4. Abra a página do Banco de Dados SQL para o seu novo banco de dados, se ela já não estiver aberta. O nome do recurso deve dizer *Banco de Dados SQL* e não *SQL Server*.

  ![Página do banco de dados SQL](./media/search-indexer-tutorial/hotels-db.png)

4. Na barra de comandos, clique em **Ferramentas** > **Editor de consultas**.

5. Clique em **Logon** e insira o nome de usuário e senha do administrador do servidor.

6. Clique em **Abrir consulta** e navegue até o local de *hotels.sql*. 

7. Selecione o arquivo e clique em **Abrir**. O script deve ser semelhante à captura de tela a seguir:

  ![Script SQL](./media/search-indexer-tutorial/sql-script.png)

8. Clique em **Executar** para executar a consulta. No painel Resultados, você verá uma mensagem de consulta bem-sucedida, para 3 linhas.

9. Para retornar um conjunto de linhas desta tabela, você pode executar a consulta a seguir como uma etapa de verificação:

   ```sql
   SELECT HotelId, HotelName, Tags FROM Hotels
   ```
   A consulta de modelo, `SELECT * FROM Hotels`, não funciona no Editor de Consultas. Os dados de exemplo incluem coordenadas geográficas no campo Localização, que atualmente não é tratado no editor. Para obter uma lista das outras colunas para consultar, você pode executar essa instrução: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Agora que você tem um conjunto de dados externo, copie a cadeia de conexão ADO.NET para o banco de dados. Na página de Banco de Dados SQL do seu banco de dados, vá para **Configurações** > **Cadeias de Conexão** e copie a cadeia de conexão ADO.NET.
 
  Uma cadeia de conexão ADO.NET se parece com o exemplo a seguir, modificada para usar um nome de banco de dados, nome de usuário e senha válidos.

  ```sql
  Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  ```
11. Cole a cadeia de conexão "AzureSqlConnectionString" como a terceira entrada no arquivo **appsettings.json** no Visual Studio.

    ```json
    {
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Entender o código de índice e indexador

Seu código agora está pronto para compilar e executar. Antes de fazer isso, reserve um minuto para estudar as definições de índice e indexador para este exemplo. O código relevante encontra-se em dois arquivos:

  + **hotel.cs**, que contém o esquema que define o índice
  + **Program.cs**, que contém as funções para criar e gerenciar estruturas em seu serviço

### <a name="in-hotelcs"></a>Em hotel.cs

O esquema de índice define a coleção de campos, incluindo atributos especificando as operações permitidas, por exemplo, se um campo é de texto completo que pode ser pesquisado, filtrado ou classificado, conforme mostrado na definição de campo a seguir para HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Um esquema também pode incluir outros elementos, incluindo perfis de pontuação para acelerar uma pontuação de pesquisa, analisadores personalizados e outros constructos. No entanto, neste caso, o esquema é definido de forma esparsa, consistindo somente em campos encontrados nos conjuntos de dados de exemplo.

Neste tutorial, o indexador extrai dados de uma fonte de dados. Na prática, você pode anexar vários indexadores ao mesmo índice, criando um índice pesquisável consolidado de várias fontes de dados e indexadores. Você pode usar o mesmo par de índice e indexador, variando apenas as fontes de dados ou um índice com várias combinações de indexador e fonte de dados, dependendo de onde você precisa de flexibilidade.

### <a name="in-programcs"></a>Em Program.cs

O programa principal inclui funções para todas as três fontes de dados normais. Os seguintes objetos destacam-se quando nos concentrarmos somente no Banco de Dados SQL do Azure:

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

No Azure Search, os objetos que você pode exibir, configurar ou excluir de forma independente incluem índices, indexadores e fontes de dados (*hotéis*, *azure-sql-indexer*, *azure-sql*, respectivamente). 

A coluna *AzureSqlHighWaterMarkColumnName* merece menção especial porque ela fornece informações de detecção de alteração, usadas pelo indexador para determinar se uma linha foi alterada desde a última carga de trabalho de indexação. [Alterar as políticas de detecção](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) são compatíveis somente com indexadores e variam de acordo com a fonte de dados. Para o Banco de Dados SQL do Azure, você pode escolher entre duas políticas, dependendo dos requisitos do banco de dados.

O código a seguir mostra os métodos em Program.cs usados para criar uma fonte de dados e um indexador. O código verifica e exclui os recursos existentes do mesmo nome, sob a suposição de que você pode executar este programa várias vezes.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Observe que as chamadas de API do indexador são independentes da plataforma exceto [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), que especifica o tipo de rastreador a ser invocado.

## <a name="run-the-indexer"></a>Executar o indexador

Nesta etapa, vamos compilar e executar o programa. 

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **DotNetHowToIndexers** e selecione **Compilar**.
2. Novamente, clique com o botão direito do mouse em **DotNetHowToIndexers**, depois em **Depurar** > **Iniciar nova instância**.

O programa é executado no modo de depuração. Uma janela de console relata o status de cada operação.

  ![Script SQL](./media/search-indexer-tutorial/console-output.png)

Seu código é executado localmente no Visual Studio, conectando-se ao seu serviço de pesquisa no Azure, que por sua vez usa a cadeia de conexão para se conectar ao Banco de Dados SQL do Azure e recuperar o conjunto de dados. Com este número de operações, há vários potenciais pontos de falha, mas se você receber um erro, verifique primeiro as condições a seguir:

+ As informações de conexão do serviço de pesquisa que você fornece são limitadas ao nome do serviço neste tutorial. Se você digitou a URL completa, as operações são interrompidas durante a criação do índice, com um erro de falha de conexão.

+ Informações de conexão do banco de dados em **appsettings.json**. Elas devem ser limitadas à cadeia de conexão ADO.NET obtida no portal, modificadas para incluir um nome de usuário e senha válidos para o seu banco de dados. A conta de usuário deve ter permissão para recuperar dados.

+ Limites de recursos. Lembre-se de que o serviço (gratuito) compartilhado tem um limite de 3 índices, indexadores e fontes de dados. Um serviço no limite máximo não pode criar novos objetos.

## <a name="search-the-index"></a>Pesquisar o índice 

No portal do Azure, na página de Visão geral do serviço de pesquisa, clique em **Gerenciador de pesquisa** na parte superior para enviar algumas consultas no novo índice.

1. Clique em **Alterar índice** na parte superior para selecionar o índice *hotéis*.

2. Clique no botão **Pesquisar** para emitir uma pesquisa vazia. 

  As três entradas no índice são retornadas como documentos JSON. O Gerenciador de pesquisa retorna documentos em JSON para que você possa exibir toda a estrutura.

3. Em seguida, insira uma cadeia de caracteres de pesquisa: `search=river&$count=true`. 

  Essa consulta invoca a pesquisa de texto completo no termo `river`, e o resultado inclui uma contagem dos documentos correspondentes. Retornar a contagem de correspondência de documentos é útil em cenários de teste, quando você tiver um índice grande com milhares ou milhões de documentos. Nesse caso, apenas um documento corresponde à consulta.

4. Por fim, insira uma cadeia de caracteres de pesquisa que limita a saída JSON para campos de interesse: `search=river&$count=true&$select=hotelId, baseRate, description`. 

  A resposta da consulta é reduzida para os campos selecionados, resultando em uma saída mais concisa.

## <a name="view-indexer-configuration"></a>Exibir configuração do indexador

Todos os indexadores, incluindo o que você acabou de criar de forma programática, são listados no portal. Você pode abrir uma definição do indexador e exibir sua fonte de dados ou configurar um agendamento de atualização para obter linhas novas e alteradas.

1. Abra a página Visão geral do serviço do seu serviço do Azure Search.
2. Role para baixo para encontrar os blocos de **Indexadores** e **Fontes de Dados**.
3. Clique em um bloco para abrir uma lista de cada recurso. Você pode selecionar fontes de dados ou indexadores individuais para exibir ou modificar as definições de configuração.

  ![Blocos de indexador e de fonte de dados](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for mais usar estes serviços, siga as seguintes etapas para excluir todos os recursos criados neste tutorial no portal do Azure. 

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Na sua página de grupo de recursos, clique em **Excluir grupo de recursos**, digite o nome do recurso a ser excluído na caixa de texto e, em seguida, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e tarefas específicas de outras fontes de dados com suporte, confira os artigos a seguir:

* [Banco de Dados SQL do Azure ou SQL Server em uma máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Banco de dados do Azure Cosmos](search-howto-index-documentdb.md)
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexação de blobs CSV usando o indexador de Blobs do Azure Search](search-howto-index-csv-blobs.md)
* [Indexação de blobs JSON com o indexador de blobs do Azure Search](search-howto-index-json-blobs.md)

