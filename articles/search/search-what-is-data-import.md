<properties
	pageTitle="Importação de dados na Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Como carregar dados em um índice na Pesquisa do Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importar dados para a Pesquisa do Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

A Pesquisa do Azure é um serviço de pesquisa de nuvem hospedado que fornece um mecanismo de pesquisa, recursos de pesquisa e armazenamento de dados. O serviço opera com dados persistentes (um índice), que fornecem documentos e informações usados para processar um índice, executar consultas ou formular os resultados da pesquisa. Entre essas operações de alto nível estão operações específicas de baixo nível para análise de texto, classificação, filtragem, classificação, criação de facetas e outras funções usadas para computar ou exibir os resultados da pesquisa.

A relevância da experiência de pesquisa depende da qualidade dos dados que você carrega e da frequência com que ela precisa ser atualizada. Neste artigo, vamos apresentar as abordagens de envio por push e pull (rastreamento) para importar e atualizar um índice.

Antes que seja possível importar dados, o índice já deverá existir e os dados que você carregar deverão obedecer ao esquema. Confira [Índices na Pesquisa do Azure](search-what-is-an-index.md) para saber mais.

##Considerações sobre conjuntos de dados

Não existem restrições do tipo de dados a ser carregado, desde que o esquema e os conjuntos de dados sejam formulados como estruturas JSON.

Os dados carregados devem se originar de qualquer banco de dados ou fonte de dados que seu aplicativo personalizado cria ou consome. Por exemplo, se seu aplicativo for um catálogo online de varejo, o índice criado para a pesquisa do Azure deverá desenhar dados do inventário de produtos ou de bancos de dados de vendas que ofereçam suporte a seu aplicativo.

Um índice na Pesquisa do Azure obtém dados de uma única tabela, exibição, contêiner de blob ou equivalente. Talvez seja necessário criar uma estrutura de dados em seu banco de dados ou aplicativo noSQL que forneça os dados para a Pesquisa do Azure. Como alternativa, para determinadas fontes de dados, como o Banco de Dados SQL do Azure ou o Banco de Dados de Documentos, você poderá criar um indexador que rastreie uma tabela, uma exibição ou um contêiner de blob externos para dados a serem carregados na Pesquisa do Azure.

##Requisitos de sincronização de dados e latência

A tabela a seguir é um resumo dos requisitos comuns e das recomendações para cumpri-los.

|Requisitos|Recomendações|
|------------|---------------|
|Sincronização de dados quase em tempo real|Código, .NET ou API REST, para enviar por push atualizações para um índice. A abordagem de pull para ingestão de dados é uma operação agendada, que não pode ser executada rápido o suficiente para acompanhar as alterações rápidas em uma fonte de dados principal.|
|Banco de Dados SQL do Azure, Banco de Dados de Documentos ou SQL Server em VMs do Azure|Os indexadores são vinculados a tipos de fonte de dados específicos. Se as fontes de dados primárias estiverem em uma fonte de dados com suporte, você poderá usar um indexador para rastrear a fonte de dados e agendar a atualização de dados de hora em hora. Você pode configurar um indexador no portal ou no código.|
|Atualização de dados agendada|Use um indexador (veja acima).|
|Rastreador|Use um indexador (veja acima).|
|Criação de protótipos sem código ou edição|O portal inclui um Assistente para Importar Dados que configura um indexador, às vezes gerando um esquema preliminar, se houver informações suficientes no banco de dados primário para fazer isso. O assistente inclui opções para configurar a atualização de dados agendada. Opcionalmente, você pode adicionar os analisadores de linguagem ou as opções de CORS. Há algumas desvantagens: não é possível adicionar perfis de pontuação nem exportar um esquema criado no portal para um arquivo JSON para ser usado no código.| 

<!---HONumber=Nov15_HO3-->