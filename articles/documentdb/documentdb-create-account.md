<properties
	pageTitle="Como criar uma conta do Banco de Dados de Documentos | Microsoft Azure"
	description="Crie um banco de dados NoSQL com o Banco de Dados de Documentos do Azure. Siga estas instruções para criar uma conta do Banco de Dados de Documentos e comece a criar seu banco de dados NoSQL de dimensões globais e incrivelmente rápido." 
	keywords="criar um banco de dados"
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
	ms.date="08/11/2016"
	ms.author="mimig"/>

# Como criar uma conta do Banco de Dados de Documentos usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](documentdb-create-account.md)
- [CLI do Azure e ARM](documentdb-automation-resource-manager-cli.md)

Para criar um banco de dados com o Banco de Dados de Documentos do Microsoft Azure, você deverá:

- Ter uma conta do Azure. Se ainda não tiver uma, você poderá obter uma [conta gratuita do Azure](https://azure.microsoft.com/free).
- Criar uma conta do Banco de Dados de Documentos

Você pode criar uma conta do Banco de Dados de Documentos usando o portal do Azure, modelos do Azure Resource Manager ou a CLI (interface de linha de comando) do Azure. Este artigo mostra como criar uma conta de banco de dados usando o portal do Azure. Para criar uma conta usando o Gerenciador de Recursos do Azure ou a CLI do Azure, consulte [Automatizar a criação da conta do Banco de Dados de Documentos](documentdb-automation-resource-manager-cli.md).

Você é novo no Banco de Dados de Documentos? Assista a [este](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vídeo de Scott Hanselman com duração de quatro minutos para saber como concluir as tarefas mais comuns no portal online.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Próximas etapas

Agora que você tem uma conta do Banco de Dados de Documentos, a próxima etapa é criar o banco de dados do Banco de Dados de Documentos. Você pode criar um banco de dados usando:

- O portal do Azure, como descrito em [Criar um banco de dados do Banco de Dados de Documentos usando o portal do Azure](documentdb-create-database.md).
- As amostras de C# .NET no projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) do repositório [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) no GitHub.
- Os tutoriais completos: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node. js](documentdb-nodejs-application.md) ou [Python](documentdb-python-application.md).
- As [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md). O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js.


Depois de criar seu banco de dados, você precisa [adicionar uma ou mais coleções](documentdb-create-collection.md) ao banco de dados e [adicionar documentos](documentdb-view-json-document-explorer.md) às coleções.

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do Banco de Dados de Documentos](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-queries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no Portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](documentdb-sdk-dotnet.md).

Para saber mais sobre o Banco de Dados de Documentos, explore estes recursos:

-	[Roteiro de aprendizagem para o Banco de Dados de Documentos](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Conceitos e modelo de recursos do Banco de Dados de Documentos](documentdb-resources.md)

<!---HONumber=AcomDC_0817_2016-->