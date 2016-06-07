<properties
    pageTitle="Consultar o índice de Pesquisa do Azure usando o Portal do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Emita uma consulta de pesquisa no Gerenciador de Pesquisa do Portal do Azure."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"
/>
# Consultar seu índice de Pesquisa do Azure usando o Portal do Azure
> [AZURE.SELECTOR]
- [Visão geral](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Este guia mostrará como consultar o índice de Pesquisa do Azure no Portal do Azure.

Antes de começar este passo a passo, você já deve ter [criado um índice de Pesquisa do Azure](search-what-is-an-index.md) e [preenchido-o com dados](search-what-is-data-import.md).

## I. Vá para a folha de Pesquisa do Azure
1. Clique em "Todos os recursos" no menu à esquerda do [Portal do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selecione o serviço de Pesquisa do Azure

## II. Selecione o índice que você deseja pesquisar
1. Selecione o índice que você deseja pesquisar no bloco "Índices".

![](./media/search-explorer/pick-index.png)

## III. Clique no bloco "Gerenciador de Pesquisa"
![](./media/search-explorer/search-explorer-tile.png)

## III. Iniciar a pesquisa
1. Para pesquisar o índice de Pesquisa do Azure, comece a digitar no campo "*Cadeia de caracteres de consulta*"e pressione"**Pesquisar**".
 * Ao usar o Gerenciador de Pesquisa, você pode especificar qualquer um dos [parâmetros de consulta](https://msdn.microsoft.com/library/dn798927.aspx)

2. Na seção "*Resultados*", os resultados da consulta serão apresentados no JSON bruto que você receberia em um Corpo de Resposta de HTTP ao emitir solicitações de pesquisa em relação à API REST da Pesquisa do Azure.
3. A cadeia de caracteres de consulta é analisada automaticamente para a URL de solicitação adequada para enviar uma solicitação HTTP em relação à API REST de Pesquisa do Azure

![](./media/search-explorer/search-bar.png)

<!---HONumber=AcomDC_0601_2016-->