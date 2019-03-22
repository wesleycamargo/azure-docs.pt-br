---
title: Guia do editor de otimização do mecanismo de pesquisa do Azure Marketplace | Microsoft Docs
description: Fornece diretrizes sobre como maximizar a SEO (otimização do mecanismo de pesquisa).
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cacc7c0b269e8006903961049caf3cd7e3bee449
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834329"
---
<a name="azure-marketplace-seo-publisher-guide"></a>Guia do editor de SEO do Azure Marketplace
=======================================

### <a name="general-explanation-of-algorithm"></a>Explicação geral do algoritmo

O mercado utiliza o Azure Search para potencializar os recursos de pesquisa do site. O algoritmo é baseado na frequência de termo – frequência de documento inverso ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). É usado o [Analisador Lucene](https://lucene.apache.org/core/) padrão.

Em geral, todos os campos de texto, categorias e setores e incluídos no peso da relevância. Termos especializados que são usados com pouca frequência por aplicativos, mas frequentemente no aplicativo, geram uma pontuação de correspondência maior com a pesquisa. Portanto, incluir termos como "VM" ofereceria pouco benefício, enquanto que o "Azure Search" seria muito mais especializado.
A seguir, estão os campos mais relevantes a serem considerados.

 
|  Campo                   | Importância | Diretrizes                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Nome da oferta               |  Alto      | Exato ou próximo de uma correspondência completa com a consulta de pesquisa resultará em uma classificação alta.                       |
| Nome do editor           |  Alto      | Exato ou próximo de uma correspondência completa com a consulta de pesquisa resultará em uma classificação alta.                       |
| Descrição breve        |  Média    | Dada a nomenclatura de aplicativos e nomes de editores quase garantirá uma alta classificação, que pode não ser o mais relevante. Nesse caso, uma breve descrição é crítica. Mantenha o texto conciso e direto ao ponto. Palavras-chave e termos de pesquisa esperados devem ser incluídos para melhor resultado.  Por exemplo, "Este é o melhor PDV de varejo totalmente integrado ao Dynamics 365" é menos eficaz do que "PDV de varejo (ponto de venda) para o Dynamics 365".  | 
| Descrição longa         |  Baixo       | A descrição oferece uma maneira com mais detalhes. As descrições mais eficazes são de comprimento razoável e palavras-chave são usadas.  Uma descrição direta usando palavras-chave irá beneficiar mais do que textos longos e longos. Certifique-se de termos-chave, como "IoT", estão presentes na descrição.  |
| Categorias de produtos       | Média     |  As categorias de produtos são determinadas por uma combinação de opções de editores e da Microsoft. Selecione essas categorias adequadamente para que os usuários possam encontrar facilmente os aplicativos na categoria correta. |
|  |  |  |


### <a name="other-tips"></a>Outras dicas

-   A pesquisa sugere uma atividade intensa do usuário. Ele prioriza as correspondências em relação ao nome/editor do aplicativo. A descrição resumida torna-se o campo-chave para quando o termo de pesquisa não é uma correspondência exata com o nome do editor/aplicativo.
-   Os documentos para download não estão incluídos na ponderação de pesquisa.
-   A aquisição e o uso reais dos aplicativos também afetarão a classificação da pesquisa. Por exemplo, dois aplicativos equivalentes em que um possui muito mais usuários obterão uma classificação mais alta.
