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

Na Pesquisa do Azure, o serviço opera com dados persistentes (um índice), que fornecem documentos e informações usados para processar um índice, executar consultas ou formular os resultados da pesquisa. Para popular um índice, você pode usar um modelo de push ou pull para carregar dados.

Antes da importação, o índice já deverá existir. Consulte [Índices na Pesquisa do Azure](search-what-is-an-index.md) para saber mais.

##Enviar por push dados para um índice

Essa abordagem se refere a obter um conjunto de dados existente que esteja de acordo com o esquema de índice e publicá-lo em seu serviço de pesquisa. Para aplicativos com requisitos de latência muito baixos (por exemplo, se você precisar que as operações de pesquisa estejam sincronizadas com bancos de dados de inventário), um modelo de push será sua única opção.

Você pode usar a API REST ou o SDK do .NET para enviar por push dados para um índice. Atualmente, não há nenhum suporte de ferramenta para enviar por push dados por meio do portal.

Essa abordagem é mais flexível do que um modelo de pull, pois carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, o limite que vier em primeiro lugar).

##Dados de pull (rastreamento) 

Um modelo de pull rastreia a uma fonte de dados com suporte e carrega o índice para você. Na Pesquisa do Azure, esse recurso é implementado por meio de *indexadores*, atualmente disponíveis para o banco de dados SQL do Azure, o Banco de Dados de Documentos e o SQL Server em VMs do Azure. Consulte [Indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) para saber mais sobre o carregamento de dados do SQL do Azure.

Você pode usar o portal, a API REST ou o SDK do .NET para enviar por pull dados para um índice.

##Requisitos de conjunto de dados

Não existem restrições do tipo de dados a ser carregado, desde que o esquema e os conjuntos de dados sejam formulados como estruturas JSON.

Os dados devem se originar de qualquer banco de dados ou fonte de dados que seu aplicativo personalizado cria ou consome. Por exemplo, se seu aplicativo for um catálogo online de varejo, o índice criado para a pesquisa do Azure deverá desenhar dados do inventário de produtos ou de bancos de dados de vendas que ofereçam suporte a seu aplicativo.

O conjunto de dados deve derivar de uma única tabela, exibição, contêiner de blob ou equivalente. Talvez seja necessário criar uma estrutura de dados em seu banco de dados ou aplicativo noSQL que forneça os dados para a Pesquisa do Azure. Como alternativa, para determinadas fontes de dados, como o Banco de Dados SQL do Azure ou o Banco de Dados de Documentos, você poderá criar um indexador que rastreie uma tabela, uma exibição ou um contêiner de blob externos para dados a serem carregados na Pesquisa do Azure.

##Escolhendo uma abordagem de importação de dados

|Critérios|Abordagem recomendada|
|------------|---------------|
|Sincronização de dados quase em tempo real|Código, .NET ou API REST, para enviar por push atualizações para um índice. A abordagem de pull para ingestão de dados é uma operação agendada, que não pode ser executada rápido o suficiente para acompanhar as alterações rápidas em uma fonte de dados principal.|
|Banco de Dados SQL do Azure, Banco de Dados de Documentos ou SQL Server em VMs do Azure|Os indexadores são vinculados a tipos de fonte de dados específicos. Se as fontes de dados primárias forem um tipo de fonte de dados com suporte, um indexador será a maneira mais fácil de carregar um índice. Você pode agendar a atualização de dados em intervalos de horas. Você pode configurar um indexador no portal ou no código.|
|Atualização de dados agendada|Use um indexador (veja acima).|
|Criação de protótipos sem código ou edição|O portal inclui um Assistente para Importar Dados que configura um indexador, às vezes gerando um esquema preliminar, se houver informações suficientes no banco de dados primário para fazer isso. O assistente inclui opções para configurar a atualização de dados agendada. Opcionalmente, você pode adicionar os analisadores de linguagem ou as opções de CORS. Há algumas desvantagens: não é possível adicionar perfis de pontuação nem exportar um esquema criado no portal para um arquivo JSON para ser usado no código.| 

<!---HONumber=Nov15_HO4-->