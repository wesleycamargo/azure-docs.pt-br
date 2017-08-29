---
title: Portais para criar e editar consultas de log no Azure Log Analytics | Microsoft Docs
description: "Este artigo descreve os portais que você pode usar no Azure Log Analytics para criar e editar pesquisas de logs."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: d878dae95d449bd4da58e8dad4daccaf07f98f66
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portais para criar e editar consultas de log no Azure Log Analytics

> [!NOTE]
> Este artigo descreve portais no Azure Log Analytics usando a nova linguagem de consulta.  Você pode saber mais sobre o novo idioma e obter o procedimento para atualizar seu espaço de trabalho em [Atualizar seu espaço de trabalho do Azure Log Analytics para uma nova pesquisa de logs](log-analytics-log-search-upgrade.md).  
>
> Se o seu espaço de trabalho não tiver sido atualizado para a nova linguagem de consulta, você deverá consultar [Localizar dados usando pesquisas de logs no Log Analytics](log-analytics-log-searches.md) para obter informações sobre a versão atual do portal de Pesquisa de Logs.

Você pode usar pesquisas de logs em vários locais pelo Log Analytics para recuperar dados do espaço de trabalho.  No entanto, para realmente criar e editar consultas, além de trabalhar interativamente com os dados retornados, você tem duas opções que são descritas abaixo.  

## <a name="log-search-portal"></a>Portal de pesquisa de logs
O portal Pesquisa de Logs é acessível do Portal do Azure ou do portal do OMS.  Ele é adequado para criar consultas básicas que podem ser criadas em uma única linha.  O portal Pesquisa de Logs pode ser usado sem iniciar um portal externo e você pode usá-lo para executar uma variedade de funções com pesquisas de logs, incluindo a criação de regras de alerta, a criação de grupos de computadores e a exportação dos resultados da consulta.  

O portal Pesquisa de Logs fornece vários recursos para editar a consulta sem ter um conhecimento completo da linguagem de consulta.  Você pode obter um resumo desses recursos em [Criar pesquisas de logs no Azure Log Analytics usando o portal Pesquisa de Logs](log-analytics-log-search-log-search-portal.md).


![Portal de Pesquisa de Logs](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portal Análise Avançada
O portal Análise Avançada é um portal dedicado que fornece funcionalidade avançada não disponível no portal Pesquisa de Logs.  Os recursos incluem a capacidade de editar uma consulta em várias linhas e de executar código seletivamente, além do IntelliSense sensível ao contexto e da análise inteligente.  O portal Análise Avançada é mais adequado para a criação de consultas complexas, que são salvas como uma pesquisa de logs ou então copiadas e coladas em outros elementos do Log Analytics.  Você inicia o portal Análise Avançada de um link no portal Pesquisa de Logs.

![Portal Análise Avançada](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Devido a seus recursos avançados, normalmente você usará o portal Análise Avançada como a principal ferramenta para criar e editar consultas.  Depois de determinar se a consulta funciona conforme o esperado, copie e cole-a em outro lugar, como o portal Pesquisa de Logs ou o Designer de Exibição.  No entanto, já que o portal Análise Avançada dá suporte a consultas com várias linhas, você precisa levar em consideração o exposto a seguir ao copiar uma consulta desse portal.

- Comentários devem ser removidos da consulta antes que ele seja copiada e colada em outro local.  Você pode comentar uma linha, precedendo-a de duas barras (//).  Quando você cola uma consulta de várias linhas em uma única linha, quebras de linha são removidas.  Se os comentários forem incluídos, todos os caracteres após o primeiro comentário serão considerados parte do comentário.


## <a name="next-steps"></a>Próximas etapas

- Percorrer um tutorial sobre como usar o [portal Pesquisa de Logs](log-analytics-log-search-log-search-portal.md) ou o [portal Análise Avançada](https://docs.loganalytics.io/docs/Learn/Tutorials/Getting-started-with-the-Analytics-portal) para criar consultas.
- Confira um [tutorial sobre como escrever consultas](https://docs.loganalytics.io/docs/Learn/Tutorials/Getting-started-with-queries) usando a nova linguagem de consulta.

