---
title: Gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure
description: Aprenda como gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: 
author: jejiang
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: Jejiang
ms.openlocfilehash: baa7eee614159f9c6af493aff74b27773471f7d7
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure (Versão Prévia)

O uso do Azure Cosmos DB no Gerenciador de Armazenamento do Azure permite que os usuários gerenciem entidades do Azure Cosmos DB, manipulem dados, atualizem procedimentos armazenados e gatilhos junto com outras entidades do Azure, como os blobs de armazenamento e as filas. Agora você pode usar a mesma ferramenta para gerenciar suas diferentes entidades do Azure em um único local. No momento, o Gerenciador de Armazenamento do Azure oferece suporte a todas as <!--and MongoDB--> contas do SQL. O Gerenciador de Armazenamento do Azure não funciona com o Emulador Local do Azure Cosmos DB. 

Neste artigo, você pode aprender como usar o Gerenciador de Armazenamento para gerenciar o Azure Cosmos DB.


## <a name="prerequisites"></a>pré-requisitos

Uma conta do Azure Cosmos DB para a API do SQL <!--or MongoDB API-->. Caso não tenha uma conta, você pode criar uma no Portal do Azure, conforme descrito em [Azure Cosmos DB: Compilar um aplicativo Web da API com o .NET e com o Portal do Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalação

Instale os bits mais recentes do Gerenciador de Armazenamento do Azure aqui: [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), agora há suporte para a versão do Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

1. Depois de instalar o **Gerenciador de Armazenamento do Azure**, clique no ícone de **plug-in** à esquerda, conforme mostrado na imagem a seguir.
       
   ![Ícone do plug-in](./media/storage-explorer/plug-in-icon.png)
 
2. Selecione **Adicionar uma Conta do Azure** e, em seguida, clique em **Entrar**.

   ![Conectar-se a uma assinatura do Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. Na caixa de diálogo **Entrar no Azure**, selecione **Entrar** e insira suas credenciais do Azure.

    ![Entrar](./media/storage-explorer/sign-in.png)

3. Selecione sua assinatura na lista e, em seguida, clique em **Aplicar**.

    ![Aplicar](./media/storage-explorer/apply-subscription.png)

    O painel do Explorer atualiza e exibe as contas na assinatura selecionada.

    ![Lista de contas](./media/storage-explorer/account-list.png)

    Você conectou sua **Conta do Azure Cosmos DB** à sua assinatura do Azure com êxito.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão

Um modo alternativo de se conectar a um Azure Cosmos DB é usar uma cadeia de conexão. Siga as etapas abaixo para se conectar usando uma cadeia de conexão.

1. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Azure Cosmos DB** e escolha **Conectar-se ao Azure Cosmos DB...**

    ![Conectar-se ao Azure Cosmos DB por uma cadeia de conexão](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Escolha a **Experiência Padrão** apropriada para o seu tipo de conta, <!--either-->DocumentDB **ou** MongoDB<!--or **MongoDB**-->, cole na sua **Cadeia de Conexão** e, em seguida, clique em **OK** para conectar a conta do Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de conexão, consulte [Obtenha a cadeia de conexão](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Cadeia de conexão](./media/storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Gerenciamento de recursos do Azure Cosmos DB

Você pode gerenciar uma conta do Azure Cosmos DB seguindo as operações a seguir:
* Abra a conta no Portal do Azure
* Adicione o recurso à lista de Acesso Rápido
* Pesquise e atualize os recursos
* Crie e exclua bancos de dados
* Crie e exclua coleções
* Crie, edite, exclua e filtre documentos
* Gerencie procedimentos armazenados, gatilhos e funções definidas pelo usuário

### <a name="quick-access-tasks"></a>Tarefas de acesso rápido

Ao clicar com o botão direito do mouse em uma assinatura no painel do Explorer, você poderá executar várias tarefas de ação rápida:

* Clique com o botão direito do mouse em uma conta ou banco de dados do Azure Cosmos DB. Você pode escolher a opção **Abrir no Portal** e gerenciar o recurso no navegador do Portal do Azure.

     ![Abrir no portal](./media/storage-explorer/open-in-portal.png)

* Você também pode adicionar a conta, o banco de dados e a coleção do Azure Cosmos DB no **Acesso Rápido**.
* A opção **Pesquisar daqui** habilita a pesquisa de palavra-chave no caminho selecionado.

    ![Pesquisar daqui](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Gerenciamento de banco de dados e coleção
#### <a name="create-a-database"></a>Criar um banco de dados 
Clique com o botão direito do mouse na conta do Azure Cosmos DB, escolha **Criar Banco de Dados**, insira o nome do banco de dados e pressione **Enter** para concluir.

![Criar banco de dados](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Excluir um banco de dados
Clique com o botão direito do mouse no banco de dados, clique em **Excluir Banco de Dados** e em **Sim** na janela pop-up. O nó do banco de dados é excluído e a conta do Azure Cosmos DB é atualizada automaticamente.

![Excluir database1](./media/storage-explorer/delete-database1.png)  

![Excluir database2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Criar uma coleção
Clique com o botão direito do mouse no banco de dados, escolha **Criar Coleção** e, em seguida, forneça as seguintes informações como **ID da Coleção**, **Capacidade de Armazenamento**, etc. Clique em **OK** para concluir. Para obter informações sobre configurações da chave de partição, consulte [Design de particionamento](partition-data.md#designing-for-partitioning).

Se uma chave de partição for usada ao criar uma coleção, depois que a criação for concluída, o valor da chave de partição não poderá ser alterado na coleção.

![Criar collection1](./media/storage-explorer/create-collection.png)

![Criar collection2](./media/storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Excluir uma coleção
Clique com o botão direito do mouse na coleção, clique em **Excluir Coleção** e, em seguida, clique em **Sim** na janela pop-up. 

O nó da coleção é excluído e o banco de dados é atualizado automaticamente.  

![Excluir coleção](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Gerenciamento de documentos

#### <a name="create-and-modify-documents"></a>Criar e modificar documentos
Para criar um novo documento, abra **Documentos** na janela esquerda, clique em **Novo Documento**, edite o conteúdo no painel direito e clique em **Salvar**. Você também pode atualizar um documento existente e, em seguida, clicar em **Salvar**. As alterações podem ser descartadas clicando em **Descartar**.

![Documento](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Excluir um documento
Clique no botão **Excluir** para excluir o documento selecionado.
#### <a name="query-for-documents"></a>Consulta de documentos
Edite o filtro de documentos inserindo uma [Consulta SQL](sql-api-sql-query.md) e, em seguida, clique em **Aplicar**.

![Filter](./media/storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gerenciar procedimentos armazenados, gatilhos e UDFs
* Para criar um procedimento armazenado, na árvore à esquerda, clique com botão direito do mouse em **Procedimento Armazenado**, escolha **Criar Procedimento Armazenado**, insira um nome à esquerda, digite os scripts do procedimento armazenado na janela à direita e, em seguida, clique em **Criar**. 
* Você também pode editar os procedimentos armazenados existentes ao clicar neles duas vezes, fazer a atualização e, em seguida, clicar em **Atualizar** para salvar ou clicar em **Descartar** para cancelar a alteração.

![Procedimento armazenado](./media/storage-explorer/stored-procedure.png)

* As operações para **Gatilhos** e **UDF** são semelhantes às operações para **Procedimentos Armazenados**.

## <a name="next-steps"></a>Próximas etapas

* Assista ao seguinte vídeo para aprender a usar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure: [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) (Usar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure).
* Saiba mais sobre o Gerenciador de Armazenamento e conecte mais serviços em [Introdução ao Gerenciador de Armazenamento (versão prévia)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

