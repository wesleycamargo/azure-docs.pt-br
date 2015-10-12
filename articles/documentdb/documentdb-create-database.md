<properties 
	pageTitle="Criar um banco de dados NoSQL do Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba como criar bancos de dados gerenciados usando o portal de serviço online do Banco de Dados de Documentos do Azure, um banco de dados de documentos NoSQL gerenciado para JSON. Obtenha hoje mesmo uma avaliação gratuita." 
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
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="mimig"/>

# Criar um banco de dados do Banco de Dados de Documentos usando o portal de visualização do Azure

Para usar o Banco de Dados de Documentos do Microsoft Azure, você deve ter uma conta do [Banco de Dados de Documentos](documentdb-create-account.md), um banco de dados, uma coleção e documentos. Este tópico descreve como criar um banco de dados do Banco de Dados de Documentos no portal de visualização do Microsoft Azure.

![Captura de tela realçando o botão Procurar, contas do Banco de Dados de Documentos na folha Procurar e uma conta do Banco de Dados de Documentos na folha Contas do Banco de Dados de Documentos](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  No [Portal de Visualização do Azure](https://portal.azure.com/), no Jumpbar, clique em **Contas de Banco de Dados de Documentos**. 

2.  Na folha **Contas do Banco de Dados de Documentos**, selecione a conta na qual você deseja adicionar um banco de dados do Banco de Dados de Documentos. Se você não tiver contas listadas, você precisará [criar uma conta do Banco de Dados de Documentos](documentdb-create-account.md).

3. Na folha **Conta do Banco de Dados de Documentos**, clique em **Adicionar banco de dados**.

4. Na folha **Adicionar banco de dados**, insira a ID do novo banco de dados. Quando o nome for validado, uma marca de seleção verde será exibida na caixa de **identificação**.

5. Clique em **OK** na parte inferior da tela para criar um novo banco de dados.

7. O novo banco de dados agora aparece na lente **Bancos de dados** na folha **Conta do Banco de Dados de Documentos**.
 
	![Captura de tela do novo banco de dados na folha Conta do Banco de Dados de Documentos](./media/documentdb-create-database/docdb-database-creation-7.png)

## Outra formas de criar um Banco de Dados de Documentos

Bancos de dados não precisam ser criados usando o portal de visualização, você também pode criá-los usando [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx). Para ver um exemplo de código em C# mostrando como criar um banco de dados usando o SDK .NET do Banco de Dados de Documentos, consulte o arquivo [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) arquivo no projeto DatabaseManagement, disponível no repositório [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) em [GitHub.com](https://github.com).

## Próximas etapas

Agora que você tem um banco de dados do Banco de Dados de Documentos, a próxima etapa é [criar uma coleção](documentdb-create-collection.md).

Depois de criar sua coleção, você pode [adicionar documentos](../documentdb-view-json-document-explorer.md) usando o Gerenciador de Documentos no portal de visualização ou [importar documentos](documentdb-import-data.md) para a coleção usando a ferramenta de migração de dados do Banco de Dados de Documentos, ou usar um dos [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx) para realizar operações CRUD. O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js. Para ver exemplos de código .NET que mostram como criar, remover, atualizar e excluir documentos, consulte [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) no projeto DocumentManagement no repositório azure-documentdb-net no GitHub.com.

Depois de ter documentos em uma coleção, você pode usar o [SQL do Banco de Dados de Documentos](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-queries) em seus documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal de visualização, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), ou um dos [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=Oct15_HO1-->