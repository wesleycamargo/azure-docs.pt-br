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
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 37d12209d28b73f0d7fc6d940ded344fbeae968d
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Como exibir ativos de dados relacionados no Catálogo de Dados do Azure?
O Catálogo de Dados do Azure permite que você exiba os ativos de dados relacionados a um ativo de dados selecionado exiba as relações entre eles. 

## <a name="supported-data-sources"></a>Fontes de dados com suporte 
Quando você registra os ativos de dados das fontes de dados a seguir, o Catálogo de Dados do Azure registra automaticamente metadados sobre as relações de união entre os ativos de dados selecionados. 

- SQL Server
- Banco de Dados SQL do Azure
- MySQL
- Oracle

> [!NOTE]
> Para o Catálogo de Dados importar a relação entre os ativos de dados, você deve registrar os dois ativos ao mesmo tempo. Se você tiver adicionado um deles separadamente, adicione-o novamente e os outros ativos de dados para importar a relação entre elas.

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