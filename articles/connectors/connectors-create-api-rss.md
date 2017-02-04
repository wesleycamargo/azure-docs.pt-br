---
title: RSS | Microsoft Docs
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O conector do RSS permite que os usuários publiquem e recuperem itens de feed. Também permite que os usuários inicializem operações quando um novo item é publicado no feed."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 5e13e126fecda66a453b4ced619016121af98b2c


---
# <a name="get-started-with-the-rss-connector"></a>Introdução ao conector do RSS
O RSS é um formato popular de distribuição da Web usado para publicar conteúdo atualizado com frequência, como entradas de blog e manchetes de notícias.  Muitos editores de conteúdo fornecem um feed RSS para permitir que usuários o assinem.  Use o conector RSS para recuperar informações de feeds e fluxos de gatilho quando novos itens forem publicados em um feed RSS.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. 
> 
> 

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do RSS pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML. 

 O conector do RSS contém as seguintes ações e/ou gatilhos disponíveis:

### <a name="rss-actions"></a>Ações do RSS
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [ListFeedItems](connectors-create-api-rss.md#listfeeditems) |Obter todos os itens de RSS feed. |

### <a name="rss-triggers"></a>Gatilhos do RSS
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| Quando um novo item de feed é publicado |Dispara um fluxo de trabalho quando um novo feed é publicado |

## <a name="create-a-connection-to-rss"></a>Criar uma conexão com o RSS
> [!INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## <a name="reference-for-rss"></a>Referência do RSS
Aplica-se à versão: 1.0

## <a name="onnewfeed"></a>OnNewFeed
Quando um novo item de feed é publicado: dispara um fluxo de trabalho quando um novo feed é publicado 

```GET: /OnNewFeed``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |sim |query |nenhum |URL de feed |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="listfeeditems"></a>ListFeedItems
Listar todos os itens de RSS feed: obtém todos os itens de RSS feed. 

```GET: /ListFeedItems``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |sim |query |nenhum |URL de feed |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse[FeedItem]
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="feeditem"></a>FeedItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Sim |
| título |string |Sim |
| conteúdo |string |Sim |
| links |array |Não |
| updatedOn |string |Não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


