---
title: O que é o Serviço de Decisão Personalizada?
titlesuffix: Azure Cognitive Services
description: Este artigo fornece uma visão geral do Serviço de Decisão Personalizada.
services: cognitive-services
author: alekh
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 273f2965a0fcaaa729175c5232da1aba69589eec
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364189"
---
# <a name="what-is-custom-decision-service"></a>O que é o Serviço de Decisão Personalizada?

Em um aplicativo móvel ou Web típico, uma página inicial é vinculada a vários artigos ou outros tipos de conteúdo. À medida que a frontpage carrega, ela pode solicitar ao Serviço de Decisão Personalizada para classificar os artigos incluídos na frontpage. Desse modo, quando um usuário escolhe um artigo, clicando no artigo, uma segunda solicitação pode ser enviada ao Serviço de Decisão Personalizada que registrará o resultado dessa decisão do usuário.

O Serviço de Decisão Personalizada é fácil de usar, pois requer apenas um feed RSS para o conteúdo e algumas linhas de JavaScript a serem adicionadas ao aplicativo.

O Serviço de Decisão Personalizada converte o conteúdo em recursos para aprendizado de máquina. O sistema usa esses recursos para reconhecer o conteúdo em termos de texto, imagens, vídeos e sentimento geral. Ele usa vários outros [Serviços Cognitivos da Microsoft](https://www.microsoft.com/cognitive-services), como [Vinculação de Entidade](../entitylinking/home.md), [Análise de Texto](../text-analytics/overview.md), [Emoções](../emotion/home.md) e [Visual Computacional](../computer-vision/home.md).

Alguns casos de uso comum do Serviço de Decisão Personalizada incluem:

* Personalização de artigos em um site de notícias
* Personalização de conteúdo de vídeo em um portal de mídia
* Otimização de posicionamentos de anúncios ou páginas da web para os quais o anúncio direciona
* Classificação de itens recomendados em um site de compras.

O Serviço de Decisão Personalizada está atualmente em *visualização pública gratuita*. É possível personalizar uma lista de artigos em um site ou aplicativo. A extração de recursos funciona melhor para conteúdo em inglês. A [Funcionalidade limitada](../text-analytics/overview.md) é oferecida para outros idiomas como espanhol, francês, alemão, português e japonês. Esta documentação será revisada à medida que novas funcionalidades tornarem-se disponíveis.

O Serviço de Decisão Personalizada pode ser usado em aplicativos que não estejam no domínio de personalização de conteúdo. Esses aplicativos podem ser adequados para uma versão prévia personalizada. [Contate-nos](https://azure.microsoft.com/overview/sales-number/) para saber mais.

## <a name="api-usage-modes"></a>Modos de uso da API

O Serviço de Decisão Personalizada pode ser aplicado a páginas da Web e a aplicativos móveis. As APIs podem ser chamadas de um navegador ou de um aplicativo. O uso da API é semelhante em ambos, mas alguns detalhes são diferentes.

## <a name="glossary-of-terms"></a>Glossário de termos

Vários termos frequentemente ocorrem nesta documentação:

* **Definir ação**: o conjunto de itens de conteúdo para o Serviço de Decisão Personalizada a ser classificado. Esse conjunto pode ser especificado como um ponto de extremidade *Atom* ou *RSS*.
* **Classificação**: cada solicitação ao Serviço de Decisão Personalizada especifica um ou mais conjuntos de ações. O sistema responde separando todas as opções de conteúdo desses conjuntos e retornando na ordem de classificação.
* **Função de retorno de chamada**: essa função, que você especifica, renderiza o conteúdo na interface do usuário. O conteúdo é ordenado pela ordem de classificação retornada pelo Serviço de Decisão Personalizada.
* **Recompensa**: uma medida de como o usuário respondeu ao conteúdo renderizado. O Serviço de Decisão Personalizada mede a resposta do usuário usando cliques. Os cliques são relatados ao sistema usando o código personalizado inserido no aplicativo.

## <a name="next-steps"></a>Próximas etapas

* [Registre o aplicativo](custom-decision-service-get-started-register.md) com Serviço de Decisão Personalizada
* Comece a otimizar [uma página da Web](custom-decision-service-get-started-browser.md) ou [um aplicativo para smartphone](custom-decision-service-get-started-app.md).
* Consulte a [Referência de API](custom-decision-service-api-reference.md) para saber mais sobre a funcionalidade fornecida.