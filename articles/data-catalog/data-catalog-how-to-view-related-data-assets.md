---
title: "Como exibir ativos de dados relacionados no Catálogo de Dados do Azure | Microsoft Docs"
description: "Este artigo explica como exibir os ativos de dados relacionados de um ativo de dados selecionado no Catálogo de Dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 0623847ea1e79c3a45a8fbdcc1d3a551864aefd6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Como exibir ativos de dados relacionados no Catálogo de Dados do Azure?
O Catálogo de Dados do Azure permite que você exiba os ativos de dados relacionados a um ativo de dados selecionado exiba as relações entre eles. 

## <a name="supported-data-sources"></a>Fontes de dados com suporte 
Quando você registra os ativos de dados das fontes de dados a seguir, o Catálogo de Dados do Azure registra automaticamente metadados sobre as relações de união entre os ativos de dados selecionados. 

- SQL Server
- Banco de Dados SQL do Azure
- MySQL
- Oracle

## <a name="view-related-data-assets"></a>Exibir ativos de dados relacionados
Para exibir os ativos de dados relacionados a um conjunto de dados selecionado, use a guia **Relações** conforme mostrado na imagem a seguir: 

![Catálogo de Dados do Azure – exibir ativos de dados relacionados](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

Neste exemplo, existem duas relações para o ativo de dados **ProductSubcategory** selecionado: 

- A coluna ProductSubcategoryID da tabela Produto tem uma relação de chave estrangeira com a coluna ProductSubcategoryID da tabela ProductSubcategory selecionada. 
- A coluna ProductCategoryID da tabela ProductSubCategory tem uma relação de chave estrangeira com a coluna ProductCategoryID da tabela ProductCategory selecionada.

> [!NOTE]
> Observe a direção da seta na exibição de árvore de relações.  

Para ver mais detalhes, como o nome totalmente qualificado da coluna, mova o mouse sobre ela e você verá um pop-up semelhante à imagem a seguir: 

![Catálogo de Dados do Azure – pop-up de relação](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Para incluir relações entre os ativos que já foram registrados, registre novamente esses ativos.

## <a name="next-steps"></a>Próximas etapas
- [Como gerenciar ativos de dados](data-catalog-how-to-manage.md)