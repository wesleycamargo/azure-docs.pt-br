<properties 
	pageTitle="Como criar um banco de dados no Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba como criar um banco de dados usando o portal de serviço online do Banco de Dados de Documentos do Azure, um banco de dados de documentos NoSQL para o JSON. Obtenha uma conta gratuita hoje mesmo." 
	keywords="como criar um banco de dados" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/19/2016" 
	ms.author="mimig"/>

# Como criar um banco de dados para o Banco de Dados de Documentos usando o portal do Azure

Para usar o Banco de Dados de Documentos do Microsoft Azure, você deve ter uma conta do [Banco de Dados de Documentos](documentdb-create-account.md), um banco de dados, uma coleção e documentos. Este tópico descreve como criar um banco de dados para o Banco de Dados de Documentos no portal do Microsoft Azure. Para saber mais sobre como criar um banco de dados usando um dos SDKs, veja [Outras maneiras de criar um banco de dados do Banco de Dados de Documentos](#other-ways-to-create-a-documentdb-database).

![Captura de tela que mostra como criar um banco de dados e realça Contas do Banco de Dados de Documentos na folha Procurar e uma conta do Banco de Dados de Documentos na folha Contas do Banco de Dados de Documentos](./media/documentdb-create-database/docdb-database-creation-1-2.png)

1.  No [Portal do Azure](https://portal.azure.com/), no Jumpbar, clique em **Contas do Banco de Dados de Documentos**. Se **Contas do Banco de Dados de Documentos** não estiver visível, clique em **Procurar** e então clique em **Contas do Banco de Dados de Documentos**.

2.  Na folha **Contas do Banco de Dados de Documentos**, selecione a conta na qual você deseja adicionar um banco de dados NoSQL do Banco de Dados de Documentos. Se você não tiver contas listadas, você precisará [criar uma conta do Banco de Dados de Documentos](documentdb-create-account.md).

    ![Captura de tela que mostra como criar um banco de dados e realça o botão Adicionar Banco de Dados, a caixa ID e o botão OK](./media/documentdb-create-database/docdb-database-creation-3-5.png)

3. Na folha **Conta do Banco de Dados de Documentos**, clique em **Adicionar Banco de Dados**.

4. Na folha **Adicionar banco de dados**, insira a ID do novo banco de dados. Quando o nome for validado, uma marca de seleção verde será exibida na caixa **ID**.

5. Clique em **OK** na parte inferior da tela para criar um novo banco de dados.

6. O novo banco de dados agora aparece na lente **Bancos de dados** na folha **Conta do Banco de Dados de Documentos**.
 
	![Captura de tela do novo banco de dados na folha Conta do Banco de Dados de Documentos](./media/documentdb-create-database/docdb-database-creation-6.png)

## Outra formas de criar um Banco de Dados de Documentos

Os bancos de dados não precisam ser criados com o Portal; você também pode criá-los usando os [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md) ou a [API REST](https://msdn.microsoft.com/library/mt489072.aspx). Para saber mais sobre o trabalho com bancos de dados usando o SDK do .NET, veja [Exemplos de banco de dados do .NET](documentdb-dotnet-samples.md#database-examples). Para saber mais sobre o trabalho com bancos de dados usando o SDK do Node.js, veja [Exemplos de banco de dados do Node.js](documentdb-nodejs-samples.md#database-examples).

## Próximas etapas

Agora que você sabe como criar um banco de dados para o Banco de Dados de Documentos, a próxima etapa é [criar uma coleção](documentdb-create-collection.md).

Depois de criar sua coleção, você pode [adicionar documentos JSON](documentdb-view-json-document-explorer.md) usando o Gerenciador de Documentos no Portal, [importar documentos](documentdb-import-data.md) na coleção usando a Ferramenta de Migração de Dados do Banco de Dados de Documentos ou usar um dos [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md) para executar operações CRUD. O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js. Para obter exemplos de código .NET que mostrem como criar, remover, atualizar e excluir documentos, veja [Exemplos de documentos do .NET](documentdb-dotnet-samples.md#document-examples). Para saber mais sobre o trabalho com documentos usando o SDK do Node.js, veja [Exemplos de documentos do Node.js.](documentdb-nodejs-samples.md#document-examples)

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do Banco de Dados de Documentos](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-sql-queries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no Portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](documentdb-sdk-dotnet.md).

<!---HONumber=AcomDC_0224_2016-->