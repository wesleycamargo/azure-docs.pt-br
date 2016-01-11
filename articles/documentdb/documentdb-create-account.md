<properties 
	pageTitle="Criar uma conta de banco de dados NoSQL - Avaliação Gratuita | Microsoft Azure" 
	description="Saiba como criar contas de bancos de dados usando o criador de bancos de dados online do Banco de Dados de Documentos do Azure, um banco de dados de documentos NoSQL gerenciado para JSON. Obtenha hoje mesmo uma avaliação gratuita."
	keywords="Avaliação gratuita, criador de bancos de dados online, criar um banco de dados, criar banco de dados, banco de dados de documentos, azure, Microsoft azure"
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/17/2015" 
	ms.author="mimig"/>

# Criar uma conta de banco de dados do Banco de Dados de Documentos usando o portal do Azure

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI and ARM](documentdb-automation-resource-manager-cli.md)

Para usar documentos do Banco de Dados de Documentos do Microsoft Azure, você deve criar uma conta do Banco de Dados de Documentos usando o portal do Azure, modelos do Gerenciador de Recursos do Azure ou a CLI (interface de linha de comando) do Azure. Este artigo mostra como criar uma conta de banco de dados usando o portal do Azure. Para criar uma conta usando o Gerenciador de Recursos do Azure ou a CLI do Azure, consulte [Automatizar a criação da conta do Banco de Dados de Documentos](documentdb-automation-resource-manager-cli.md).

Você é novo no Banco de Dados de Documentos? Assista a [este](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vídeo de Scott Hanselman com duração de quatro minutos para saber como concluir as tarefas mais comuns no portal online.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Próximas etapas

Agora que você tem uma conta do Banco de Dados de Documentos, a próxima etapa é criar um banco de dados do Banco de Dados de Documentos. Você pode criar um banco de dados usando:

- As amostras de C# .NET no projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) do repositório [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) no GitHub.
- O portal do Azure, como descrito em [Criar um banco de dados do Banco de Dados de Documentos usando o portal do Azure](documentdb-create-database.md).
- Os tutoriais completos: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node. js](documentdb-nodejs-application.md) ou [Python](documentdb-python-application.md).
- As [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md). O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js. 


Depois de criar seu banco de dados, você precisa [adicionar uma ou mais coleções](documentdb-create-collection.md) ao banco de dados e [adicionar documentos](documentdb-view-json-document-explorer.md) às coleções.

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do Banco de Dados de Documentos](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-queries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no Portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](documentdb-sdk-dotnet.md).

Para saber mais sobre o Banco de Dados de Documentos, explore estes recursos:

-	[Roteiro de aprendizagem para o Banco de Dados de Documentos](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Conceitos e modelo de recursos do Banco de Dados de Documentos](documentdb-resources.md)

 

<!---HONumber=AcomDC_1223_2015-->