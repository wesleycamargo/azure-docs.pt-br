---
title: "Consultar seu índice do Azure Search usando o Portal do Azure | Microsoft Docs"
description: Emita uma consulta de pesquisa no Gerenciador de Pesquisa do Portal do Azure.
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Consultar seu índice de Pesquisa do Azure usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Este guia mostrará como consultar o índice de Pesquisa do Azure no Portal do Azure.

Antes de começar este passo a passo, você já deve ter [criado um índice do Azure Search](search-what-is-an-index.md), e este já deve estar [preenchido com os dados](search-what-is-data-import.md).

## <a name="i-go-to-your-azure-search-blade"></a>I. Vá para a folha de Pesquisa do Azure
1. Clique em "Todos os recursos" no menu à esquerda do [Portal do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selecione o serviço de Pesquisa do Azure

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. Selecione o índice que você deseja pesquisar
1. Selecione o índice que você deseja pesquisar no bloco "Índices".

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. Clique no bloco "Gerenciador de Pesquisa"
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. Iniciar a pesquisa
1. Para pesquisar o índice de Pesquisa do Azure, comece a digitar no campo "*Cadeia de caracteres de consulta*"e pressione"**Pesquisar**".
   
   * Ao usar o Gerenciador de Pesquisa, você pode especificar qualquer um dos [parâmetros de consulta](https://msdn.microsoft.com/library/dn798927.aspx)
2. Na seção "*Resultados*", os resultados da consulta serão apresentados no JSON bruto que você receberia em um Corpo de Resposta de HTTP ao emitir solicitações de pesquisa em relação à API REST da Pesquisa do Azure.
3. A cadeia de caracteres de consulta é analisada automaticamente para a URL de solicitação adequada para enviar uma solicitação HTTP em relação à API REST de Pesquisa do Azure

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->


