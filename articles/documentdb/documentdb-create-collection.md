<properties 
	pageTitle="Criar uma coleção de banco de dados do Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba como criar coleções usando o portal de serviço online do Banco de Dados de Documentos do Azure, um banco de dados de documentos NoSQL gerenciado para JSON. Obtenha hoje mesmo uma avaliação gratuita." 
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

# Criar uma coleção do Banco de Dados de Documentos usando o portal de visualização do Azure

Para usar o Banco de Dados de Documentos do Microsoft Azure, você deve ter uma conta do [Banco de Dados de Documentos](documentdb-create-account.md), um [banco de dados](documentdb-create-database.md), uma coleção e documentos. Este tópico descreve como criar uma coleção do Banco de Dados de Documentos no portal de visualização do Azure.

![Captura de tela realçando as contas do Banco de Dados de Documentos no Jumpbar, a conta na folha do Banco de Dados de Documentos e o banco de dados na folha da conta do Banco de Dados de Documentos, nas lentes dos Bancos de Dados](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  No [Portal de Visualização do Azure](https://portal.azure.com/), no Jumpbar, clique em **Contas de Banco de Dados de Documentos**. 

2.  Na folha **Contas do Banco de Dados de Documentos**, selecione a conta na qual adicionar uma coleção. Se você não tiver contas listadas, você precisará [criar uma conta do Banco de Dados de Documentos](documentdb-create-account.md).

3. Na folha **Conta de Banco de Dados de Documentos** para a conta selecionada, role para baixo até as lentes dos **Bancos de dados** e, em seguida, selecione o banco de dados ao qual adicionar a uma coleção.
    
4. Na folha **Banco de dados**, clique em **Adicionar coleções**.

	![Captura de tela realçando o botão Adicionar coleção na folha do Banco de dados, a configurações na folha Adicionar Coleção e o botão OK](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. Na folha **Adicionar coleção**, insira a ID da nova coleção. Quando o nome for validado, uma marca de seleção verde será exibida na caixa de identificação.

6. Selecione uma faixa de preço para a nova coleção. Cada coleção que você cria é uma entidade faturável. Para obter mais informações sobre os níveis de desempenho disponíveis, consulte [Níveis de desempenho no Banco de Dados de Documentos](documentdb-performance-levels.md).

7. Selecione uma das seguintes **Políticas de Indexação**.

	- **Padrão**. Essa política é mais útil quando você está executando consultas de igualdade em cadeias de caracteres e usando as consultas ORDER BY, intervalo e igualdade para números. Essa política tem uma sobrecarga de armazenamento de índice menor que o **Intervalo**.
	- **Hash**. Essa política é mais útil quando você está executando consultas de igualdade para números e cadeias de caracteres. Essa política tem a menor sobrecarga de armazenamento do índice.
	- **Intervalo**. Essa política é mais útil quando você está usando consultas ORDER BY, intervalo e igualdade em números e cadeias de caracteres. Essa política tem uma sobrecarga de armazenamento de índice maior do que o **Padrão** ou **Hash**.

	Para obter mais informações sobre políticas de indexação, consulte [Políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md).

8. Clique em **OK** na parte inferior da tela para criar uma nova coleção.


9. A nova coleção agora aparece na lente **Coleções** na folha **Banco de dados**.
 
	![Captura de tela da nova coleção na folha do banco de dados](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## Outra formas de criar uma coleção do Banco de Dados de Documentos

Coleções não precisam ser criadas usando o portal de visualização, você também pode criá-las usando [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx). Para ver um exemplo de código em C# mostrando como criar uma coleção usando o SDK .NET do Banco de Dados de Documentos, consulte o arquivo [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) arquivo no projeto CollectionManagement, disponível no repositório [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) em [GitHub.com](https://github.com).

## Próximas etapas

Agora que você tem uma coleção, a próxima etapa é adicionar documentos ou importar documentos na coleção. Quando se trata de adicionar documentos a uma coleção, você tem algumas opções:

- Você pode [adicionar documentos](../documentdb-view-json-document-explorer.md) usando o Gerenciador de Documentos no portal de visualização.
- Você pode [importar documentos e dados](documentdb-import-data.md) usando a ferramenta de migração de dados do Banco de Dados de Documentos, que permite importar arquivos JSON e CSV, bem como dados do SQL Server, MongoDB, armazenamento de tabela do Azure e outras coleções do Banco de Dados de Documentos. 
- Ou você pode adicionar documentos usando um dos [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx). O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js. O arquivo [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) arquivo no projeto DocumentManagement, disponível no repositório [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) no [GitHub.com](https://github.com), demonstra operações CRUD em documentos usando o SDK .NET do Banco de Dados de Documentos.

Depois de ter documentos em uma coleção, você pode usar o [SQL do Banco de Dados de Documentos](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-queries) em seus documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal de visualização, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), ou um dos [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=Oct15_HO3-->