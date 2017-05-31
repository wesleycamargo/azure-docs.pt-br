---
title: "Criar um banco de dados e uma coleção do Azure Cosmos DB | Microsoft Docs"
description: "Saiba como criar bancos de dados e um contêiner usando o portal de serviços online do Azure Cosmos DB, um banco de dados de documentos baseado em nuvem. Obtenha hoje mesmo uma avaliação gratuita."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b81ad2f6-df7f-4c6d-8ca9-f8a9982d647e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 596a4ce9d315e7c6c41a43860469c339c7e96ac5
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-create-an-azure-cosmos-db-collection-and-database-using-the-azure-portal"></a>Como criar uma coleção e um banco de dados do Azure Cosmos DB usando o portal do Azure
Para usar o Microsoft Azure Cosmos DB, você deve ter uma [conta do Azure Cosmos DB](documentdb-create-account.md), um banco de dados, uma coleção e documentos. Este tópico descreve como criar uma coleção do Azure Cosmos DB no portal do Azure.

Não sabe direito o que é uma coleção? Consulte [O que é uma coleção do Azure Cosmos DB?](#what-is-a-documentdb-collection).

Há duas maneiras de criar uma coleção no portal, usando o botão Adicionar Coleção ou usando o [Data Explorer (visualização)](#data-explorer).

## <a name="create-a-colletion-using-add-collection-button"></a>Criar uma coleção usando o botão Adicionar Coleção

1. No [portal do Azure](https://portal.azure.com/), na barra de atalhos, clique em **Azure Cosmos DB** e, na folha **Azure Cosmos DB**, escolha a conta à qual você deseja adicionar uma coleção. Se você não tiver contas listadas, você precisará [criar uma conta do DocumentDB](documentdb-create-account.md).

   ![Captura de tela realçando as Contas do Azure Cosmos DB na Barra de Atalhos, a conta na folha Contas do Azure Cosmos DB e o banco de dados na folha da conta do Azure Cosmos DB, na lente Bancos de Dados](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

   Se o **Azure Cosmos DB** não estiver visível na barra de atalhos, clique em **Mais Serviços** e, em seguida, em **Azure Cosmos DB**. Se você não tiver contas listadas, você precisará [criar uma conta do DocumentDB](documentdb-create-account.md).
2. Na folha **Conta do Azure Cosmos DB** da conta selecionada, clique em **Adicionar Coleção**.

    ![Captura de tela realçando as Contas do Azure Cosmos DB na Barra de Atalhos, a conta na folha Contas do Azure Cosmos DB e o banco de dados na folha da conta do Azure Cosmos DB, na lente Bancos de Dados](./media/documentdb-create-collection/docdb-database-creation-3.png)
3. Na folha **Adicionar Coleção**, na caixa **ID da Coleção**, insira a ID da nova coleção. Os nomes de coleção devem ter entre 1 e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita. Quando o nome for validado, uma marca de seleção verde será exibida na caixa de identificação.

    ![Captura de tela realçando o botão Adicionar Coleção na folha Banco de Dados, as configurações na folha Adicionar Coleção e o botão OK – portal do Azure para o Azure Cosmos DB – criador de banco de dados baseado na nuvem para bancos de dados JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)
4. Por padrão, a **Capacidade de Armazenamento** é definida como **Ilimitada** para manipular coleções particionadas.

    Defina a capacidade de armazenamento para **10 GB** se você quiser uma [única coleção de partição](documentdb-partition-data.md#single-partition-and-partitioned-collections) com níveis de taxa de transferência de 400 a 10.000 unidades de solicitação/segundo (RU/s). Uma RU corresponde à taxa de transferência de uma leitura de um documento de 1 KB. Para obter mais informações sobre unidades de solicitação, confira [Unidades de solicitação](documentdb-request-units.md).

    Defina a capacidade de armazenamento como **Ilimitada** se deseja que uma [coleção particionada](documentdb-partition-data.md#single-partition-and-partitioned-collections) possa ser dimensionada para manipular uma quantidade ilimitada de armazenamento em várias partições e ter níveis de produtividade a partir de 2.500 RU/s.

    Defina a capacidade de armazenamento como **Personalizada** se quiser provisionar uma quantidade diferente de 10 GB ou 250 GB. O Azure Cosmos DB tem uma escala praticamente ilimitada. Portanto, inclua o tamanho do armazenamento solicitado e o valor da produtividade na solicitação de suporte.

6. Na caixa **Chave de Partição**, digite uma chave de partição para a coleção. Ela é necessária para coleções particionadas e opcional para coleções de partição única. É importante selecionar a chave de partição correta ao criar uma coleção de alto desempenho. Para obter mais informações sobre como selecionar uma chave de partição, veja [Design para particionamento](../cosmos-db/partition-data.md#designing-for-partitioning).
7. Na folha **Banco de dados**, crie um novo banco de dados ou use uma existente. Os nomes de banco de dados devem ter entre um e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita. Para validar o nome, clique fora da caixa de texto. Quando o nome for validado, uma marca de seleção verde será exibida na caixa.
8. Clique em **OK** na parte inferior da tela para criar uma nova coleção.
9. A nova coleção agora aparece na lente **Coleções** na folha **Visão geral**.

    ![Captura de tela da nova coleção na folha Banco de Dados – portal do Azure para o Azure Cosmos DB – criador de banco de dados baseado na nuvem para bancos de dados JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-9.png)
10. **Opcional:** para modificar a taxa de transferência da coleção no portal, clique em **Escala** no menu Recursos.

    ![Captura de tela do menu de recursos, com Escala selecionado](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

<a id="data-explorer"></a>
## <a name="create-a-collection-by-using-data-explorer-preview"></a>Criar uma coleção usando o Data Explorer (visualização)

O outro método para criar uma coleção no portal é usar o Data Explorer. Para abrir o Data Explorer, clique em **Data Explorer (visualização)** na barra de navegação no portal, clique no botão **Nova Coleção**, como mostra a captura de tela a seguir.

 ![Captura de tela mostrando o botão Nova Coleção no portal](./media/documentdb-create-collection/azure-documentdb-data-explorer.png)


## <a name="what-is-an-azure-cosmos-db-collection"></a>O que é uma coleção do Azure Cosmos DB?
Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Uma coleção é uma entidade faturável, em que o [custo](documentdb-performance-levels.md) é determinado pela taxa de transferência provisionada da coleção. As coleções podem abranger uma ou mais partições/servidores e podem ser dimensionadas para lidar com volumes de armazenamento ou taxa de transferência praticamente ilimitados.

As coleções são particionadas automaticamente em um ou mais servidores físicos pelo Azure Cosmos DB. Ao criar uma coleção, você pode especificar a produtividade provisionada em termos de unidades de solicitação por segundo e uma propriedade de chave de partição. O valor dessa propriedade será usado pelo Azure Cosmos DB para distribuir documentos entre partições e encaminhar solicitações como consultas. O valor de chave de partição também atua como o limite de transação para procedimentos armazenados e gatilhos. Cada coleção tem uma quantidade reservada de produtividade específica, que não é compartilhada com outras coleções na mesma conta. Portanto, você pode escalar horizontalmente o aplicativo em termos de armazenamento e taxa de transferência.

Coleções não são o mesmo que tabelas em bancos de dados relacionais. As coleções não impõem esquemas. Na verdade, o Azure Cosmos DB não impõe nenhum esquema. Trata-se de um banco de dados sem esquemas. Sendo assim, você pode armazenar tipos diferentes de documentos com vários esquemas na mesma coleção. É possível optar por usar coleções para armazenar objetos de um único tipo, assim como acontece com as tabelas. O melhor modelo depende apenas de como os dados aparecem juntos em consultas e transações.

## <a name="other-ways-to-create-an-azure-cosmos-db-collection"></a>Outras maneiras de criar uma coleção do Azure Cosmos DB
As coleções não precisam ser criadas usando o portal. Também é possível criá-las usando os [SDKs do DocumentDB](documentdb-sdk-dotnet.md) e a API REST.

* Para um exemplo de código C#, confira os [exemplos de coleção em C#](documentdb-dotnet-samples.md#collection-examples).
* Para obter um exemplo de código Node.js, confira os [exemplos de coleção em Node.js](documentdb-nodejs-samples.md#collection-examples).
* Para obter um exemplo de código Python, confira os [exemplos de coleção em Python](documentdb-python-samples.md#collection-examples).
* Para obter um exemplo de API REST, confira [Criar uma coleção](https://msdn.microsoft.com/library/azure/mt489078.aspx).

## <a name="troubleshooting"></a>Solucionar problemas
Se a opção **Adicionar Coleção** estiver desabilitada no Portal do Azure, isso significa que sua conta está desabilitada no momento, o que normalmente ocorre quando todos os créditos de benefícios do mês são usados.    

## <a name="next-steps"></a>Próximas etapas
Agora que você tem uma coleção, a próxima etapa é adicionar documentos ou importar documentos na coleção. Quando se trata de adicionar documentos a uma coleção, você tem algumas opções:

* Você pode [adicionar documentos](documentdb-view-json-document-explorer.md) usando o Gerenciador de Documentos no portal.
* Você pode [importar documentos e dados](documentdb-import-data.md) usando a ferramenta de migração de dados do DocumentDB, que permite importar arquivos JSON e CSV, bem como dados do SQL Server, MongoDB, armazenamento de tabela do Azure e outras coleções do DocumentDB.
* Ou você pode adicionar documentos usando um dos [SDKs do DocumentDB](documentdb-sdk-dotnet.md). O DocumentDB tem SDKs de API de JavaScript, .NET, Java, Python e Node.js. Para obter exemplos de código C# que mostrem como trabalhar com documentos que usam o SDK do .NET do DocumentDB, confira os [exemplos de documentos do C#](documentdb-dotnet-samples.md#document-examples). Para obter exemplos de código Node.js que mostrem como trabalhar com documentos que usam o SDK do Node.js do DocumentDB, confira os [exemplos de documentos do Node.js](documentdb-nodejs-samples.md#document-examples).

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do DocumentDB](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#ExecutingSqlQueries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](documentdb-sdk-dotnet.md). 

