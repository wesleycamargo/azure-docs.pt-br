---
title: "Criar uma coleção e banco de dados do DocumentDB | Microsoft Docs"
description: "Saiba como criar bancos de dados NoSQL e coleções de documento JSON usando o portal de serviço online para Azure DocumentDB, um banco de dados de documento com base na nuvem. Obtenha hoje mesmo uma avaliação gratuita."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b81ad2f6-df7f-4c6d-8ca9-f8a9982d647e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 8f3e6b271f3ca5973a00f2a5a9e11dafeb9637aa
ms.openlocfilehash: 45371ca337f291f37e1ac5c388170b7079dfad52


---
# <a name="how-to-create-a-documentdb-collection-and-database-using-the-azure-portal"></a>Como criar uma coleção e um banco de dados do DocumentDB usando o Portal do Azure
Para usar o Banco de Dados de Documentos do Microsoft Azure, você deve ter uma conta do [Banco de Dados de Documentos](documentdb-create-account.md), um banco de dados, uma coleção e documentos. Este tópico descreve como criar uma coleção do Banco de Dados de Documentos no Portal do Azure.

Não sabe direito o que é uma coleção? Confira [O que é uma coleção do Banco de Dados de Documentos?](#what-is-a-documentdb-collection)

1. No [portal do Azure](https://portal.azure.com/), na barra de atalhos, clique em **DocumentDB (NoSQL)** e, na folha **DocumentDB (NoSQL)**, escolha a conta à qual você deseja adicionar uma coleção. Se você não tiver contas listadas, você precisará [criar uma conta do Banco de Dados de Documentos](documentdb-create-account.md).

   ![Captura de tela realçando as contas do Banco de Dados de Documentos no Jumpbar, a conta na folha do Banco de Dados de Documentos e o banco de dados na folha da conta do Banco de Dados de Documentos, nas lentes dos Bancos de Dados](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

   Se **DocumentDB (NoSQL)** não estiver visível na barra de atalhos, clique em **Mais Serviços** e em **DocumentDB (NoSQL)**. Se você não tiver contas listadas, você precisará [criar uma conta do Banco de Dados de Documentos](documentdb-create-account.md).
2. Na folha **Conta do DocumentDB** da conta selecionada, clique em **Adicionar Coleção**.

    ![Captura de tela realçando as contas do Banco de Dados de Documentos no Jumpbar, a conta na folha do Banco de Dados de Documentos e o banco de dados na folha da conta do Banco de Dados de Documentos, nas lentes dos Bancos de Dados](./media/documentdb-create-collection/docdb-database-creation-3.png)
3. Na folha **Adicionar Coleção**, na caixa **ID**, insira a ID da nova coleção. Os nomes de coleção devem ter entre 1 e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita. Quando o nome for validado, uma marca de seleção verde será exibida na caixa de identificação.

    ![Captura de tela realçando o botão Adicionar Coleção na folha do Banco de Dados, as configurações na folha Adicionar Coleção e o botão OK — portal do Azure para Banco de Dados de Documentos — criador do banco de dados baseado na nuvem para bancos de dados JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)
4. Por padrão, **Tipo de Preço** é definido como **Standard** para que você possa personalizar a taxa de transferência e o armazenamento para sua coleção. Para obter mais informações sobre o tipo de preço, veja [Níveis de desempenho no Banco de Dados de Documentos](documentdb-performance-levels.md).  
5. Selecione um **Modo de particionamento** para a coleção, **Partição Única** ou **Particionado**.

    Uma **partição única** tem uma capacidade de armazenamento reservado de 10 GB e pode ter níveis de taxa de transferência de 400 a 10.000 unidades de solicitação/segundo (RU/s). Uma RU corresponde à taxa de transferência de uma leitura de um documento de 1 KB. Para obter mais informações sobre unidades de solicitação, confira [Unidades de solicitação](documentdb-request-units.md).

    Uma **coleção particionada** pode ser dimensionada para lidar com uma quantidade ilimitada de armazenamento em várias partições e pode ter níveis de produtividade começando em 10.100 RU/s. Para obter mais informações sobre coleções particionadas, veja [Partição única e coleções particionadas](documentdb-partition-data.md#single-partition-and-partitioned-collections).

    Por padrão, a taxa de transferência para uma nova coleção de partição única é definida como 1000 RU/s com uma capacidade de armazenamento de 10 GB. Para uma coleção particionada, a taxa de transferência da coleção é definida como 10100 RU/s sem limite superior na capacidade ou na taxa de transferência.
6. Se você estiver criando uma coleção particionada, selecione a **Chave de Partição** para a coleção. É importante selecionar a chave de partição correta ao criar uma coleção de alto desempenho. Para obter mais informações sobre como selecionar uma chave de partição, veja [Design para particionamento](documentdb-partition-data.md#designing-for-partitioning).
7. Na folha **Banco de dados**, crie um novo banco de dados ou use uma existente. Os nomes de banco de dados devem ter entre um e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita. Para validar o nome, clique fora da caixa de texto. Quando o nome for validado, uma marca de seleção verde será exibida na caixa.
8. Clique em **OK** na parte inferior da tela para criar uma nova coleção.
9. A nova coleção agora aparece na lente **Coleções** na folha **Visão geral**.

    ![Captura de tela da nova coleção na folha Banco de Dados — Portal do Azure para Banco de Dados de Documentos — criador do banco de dados baseado na nuvem para bancos de dados JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-9.png)
10. **Opcional:** para modificar a taxa de transferência da coleção no portal, clique em **Escala** no menu Recursos.

    ![Captura de tela do menu de recursos, com Escala selecionado](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

## <a name="what-is-a-documentdb-collection"></a>O que é uma coleção do Banco de Dados de Documentos?
Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Uma coleção é uma entidade faturável, em que o [custo](documentdb-performance-levels.md) é determinado pela taxa de transferência provisionada da coleção. As coleções podem abranger uma ou mais partições/servidores e podem ser dimensionadas para lidar com volumes de armazenamento ou taxa de transferência praticamente ilimitados.

As coleções são particionadas automaticamente em um ou mais servidores físicos pelo Banco de Dados de Documentos. Ao criar uma coleção, você pode especificar a produtividade provisionada em termos de unidades de solicitação por segundo e uma propriedade de chave de partição. O valor dessa propriedade será usado pelo Banco de Dados de Documentos para distribuir documentos entre partições e rotear solicitações como consultas. O valor de chave de partição também atua como o limite de transação para procedimentos armazenados e gatilhos. Cada coleção tem uma quantidade reservada de produtividade específica, que não é compartilhada com outras coleções na mesma conta. Portanto, você pode escalar horizontalmente o aplicativo em termos de armazenamento e taxa de transferência.

Coleções não são o mesmo que tabelas em bancos de dados relacionais. As coleções não impõem esquemas. Na verdade, o Banco de Dados de Documentos não impõe nenhum esquema. Trata-se de um banco de dados livre de esquema. Sendo assim, você pode armazenar tipos diferentes de documentos com vários esquemas na mesma coleção. É possível optar por usar coleções para armazenar objetos de um único tipo, assim como acontece com as tabelas. O melhor modelo depende apenas de como os dados aparecem juntos em consultas e transações.

## <a name="other-ways-to-create-a-documentdb-collection"></a>Outra formas de criar uma coleção do Banco de Dados de Documentos
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
* Você pode [importar documentos e dados](documentdb-import-data.md) usando a ferramenta de migração de dados do Banco de Dados de Documentos, que permite importar arquivos JSON e CSV, bem como dados do SQL Server, MongoDB, armazenamento de tabela do Azure e outras coleções do Banco de Dados de Documentos.
* Ou você pode adicionar documentos usando um dos [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md). O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js. Para obter exemplos de código C# que mostrem como trabalhar com documentos que usam o SDK do .NET do Banco de Dados de Documentos, confira os [exemplos de documentos do C#](documentdb-dotnet-samples.md#document-examples). Para obter exemplos de código Node.js que mostrem como trabalhar com documentos que usam o SDK do Node.js do Banco de Dados de Documentos, confira os [exemplos de documentos do Node.js](documentdb-nodejs-samples.md#document-examples).

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do DocumentDB](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-sql-queries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](documentdb-sdk-dotnet.md). 



<!--HONumber=Dec16_HO3-->


