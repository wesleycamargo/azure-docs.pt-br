<properties
	pageTitle="Indexadores na Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Rastrear um banco de dados para extrair dados pesquisáveis e popular um índice de Pesquisa do Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="03/09/2016"
	ms.author="heidist"/>

# Indexadores na Pesquisa do Azure
> [AZURE.SELECTOR]
- [Visão geral](search-indexer-overview.md)
- [Portal](search-import-data-portal.md)
- [Armazenamento de blobs (preview)](search-howto-indexing-azure-blob-storage.md)
- [SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Banco de Dados de Documentos](../documentdb/documentdb-search-indexer.md)

Um **indexador** na Pesquisa do Azure é um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados externa e popula um índice com base nos mapeamentos de campo a campo entre o índice e a fonte de dados. Essa abordagem às vezes é chamada de 'modelo de pull' porque o serviço obtém os dados sem que você precise escrever código que envia dados por push a um índice.

Você pode usar um indexador como o único meio para ingestão de dados ou usar uma combinação de técnicas que incluam o uso de um indexador para carregar apenas alguns dos campos no índice.

Os indexadores podem ser executados sob demanda ou em uma agenda de atualização de dados recorrente que é executada a cada quinze minutos. Atualizações mais frequentes exigem um modelo de push que atualiza simultaneamente os dados na Pesquisa do Azure e na fonte de dados externa.

Um indexador extrai dados de uma **fonte de dados** que contém informações como uma cadeia de conexão. Atualmente, há suporte às seguintes fontes de dados:

- [Banco de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (ou o SQL Server em uma máquina virtual do Azure)
- [Banco de Dados de Documentos](../documentdb/documentdb-search-indexer.md)
- [Armazenamento de blobs do Azure](search-howto-indexing-azure-blob-storage.md) (atualmente em preview. Extrai o texto de PDF, documentos do Office, HTML, XML.)

As fontes de dados são configuradas e gerenciadas independentemente dos indexadores que as utilizam, o que significa que uma fonte de dados pode ser usada por vários indexadores para carregar mais de um índice por vez.

Tanto o [SDK do .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) quanto a [API REST do Serviço](https://msdn.microsoft.com/library/azure/dn946891.aspx) dão suporte a indexadores e fontes de dados de gerenciamento.

Como alternativa, você também pode configurar um indexador no portal quando usar o assistente de **Importar Dados**. Veja [Introdução à Pesquisa do Azure no portal](search-get-started-portal) para um tutorial rápido, usando o indexador do Banco de Dados de Documentos e dados de exemplo para criar e carregar um índice usando o assistente.

<!---HONumber=AcomDC_0309_2016-->