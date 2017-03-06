---
title: "Adicionar o conector Facebook a seus Aplicativos Lógicos | Microsoft Docs"
description: "Visão geral do conector do Facebook com os parâmetros de API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: b8a66308c4f4f1df610cdacd092ef133bd605665
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-facebook-connector"></a>Introdução ao conector do Facebook
Conecte-se ao Facebook e publique em uma linha do tempo, recebe um feed de página e muito mais. 

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

Com o Facebook, você pode:

* Criar seu fluxo de negócios com base nos dados que você obtém do Facebook. 
* Usar um gatilho quando uma nova publicação for recebida.
* Usar ações que publicam em sua linha do tempo, obtêm uma feed de página e mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando há uma nova publicação em sua linha do tempo, você pode publicá-la e enviá-la ao seu feed do Twitter. 

Para adicionar uma operação nos aplicativos lógicos, veja [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do Facebook inclui o gatilho e as ações a seguir. 

| Gatilhos | Ações |
| --- | --- |
| <ul><li>Quando há uma nova publicação em minha linha do tempo</li></ul> |<ul><li>Obter feed de minha linha de tempo</li><li>Publicar em minha linha do tempo</li><li>Quando há uma nova publicação em minha linha do tempo</li><li>Obter feed da página</li><li>Obter a linha do tempo do usuário</li><li>Publicar na página</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML.

## <a name="create-a-connection-to-facebook"></a>Criar uma conexão com o Facebook
Quando você adiciona esse conector aos seus aplicativos lógicos, precisa autorizar que os aplicativos lógicos se conectem ao Facebook.

1. Entre em sua conta do Facebook
2. Selecione **Autorizar**e permita que seus aplicativos lógicos se conectem e usem o Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> Você pode usar esta conexão com o Facebook em outros aplicativos lógicos.
> 
> 

## <a name="swagger-rest-api-reference"></a>Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### <a name="get-feed-from-my-timeline"></a>Obter feed de minha linha de tempo
Obtém os feeds na linha do tempo do usuário conectado.  
```GET: /me/feed```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| fields |string |não |query |nenhum |Especifica os campos que você deseja retornar. Exemplo (id, nome, imagem). |
| limite |inteiro |não |query |nenhum |Número máximo de publicações para recuperação |
| por: |string |não |query |nenhum |Restringe a lista de publicações apenas às com local conectado. |
| filtro |string |não |query |nenhum |Recupera apenas as publicações que correspondem a um filtro de fluxo específico. |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 500 |Erro interno do servidor |
| padrão |Falha na operação. |

### <a name="post-to-my-timeline"></a>Publicar em minha linha do tempo
Publique uma mensagem de status na linha do tempo do usuário conectado.  
```POST: /me/feed```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| post |string |Sim |corpo |nenhum |Nova mensagem a ser publicada |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 500 |Erro interno do servidor |
| padrão |Falha na operação. |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>Quando há uma nova publicação em minha linha do tempo
Dispara um novo fluxo quando há uma nova publicação na linha do tempo do usuário conectado.  
```GET: /trigger/me/feed```

Não há parâmetros. 

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 500 |Erro interno do servidor |
| padrão |Falha na operação. |

### <a name="get-page-feed"></a>Obter feed da página
Obtenha publicações no feed de uma página especificada.  
```GET: /{pageId}/feed```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| pageId |string |Sim |path |nenhum |ID da página da qual as publicações devem ser recuperadas. |
| limite |inteiro |não |query |nenhum |Número máximo de publicações para recuperação |
| include_hidden |Booliano |não |query |nenhum |Se deseja ou não incluir quaisquer publicações que foram ocultas pela página |
| fields |string |não |query |nenhum |Especifica os campos que você deseja retornar. Exemplo (id, nome, imagem). |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 500 |Erro interno do servidor |
| padrão |Falha na operação. |

### <a name="get-user-timeline"></a>Obter a linha do tempo do usuário
Obtenha as publicações na linha do tempo de um usuário.  
```GET: /{userId}/feed```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| coluna |string |Sim |path |nenhum |ID do usuário cuja linha do tempo precisa ser recuperada. |
| limite |inteiro |não |query |nenhum |Número máximo de publicações para recuperação |
| por: |string |não |query |nenhum |Restringe a lista de publicações apenas às com local conectado. |
| filtro |string |não |query |nenhum |Recupera apenas as publicações que correspondem a um filtro de fluxo específico. |
| fields |string |não |query |nenhum |Especifica os campos que você deseja retornar. Exemplo (id, nome, imagem). |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 500 |Erro interno do servidor |
| padrão |Falha na operação. |

### <a name="post-to-page"></a>Publicar na página
Publique uma mensagem em uma Página do Facebook como o usuário conectado.  
```POST: /{pageId}/feed```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| pageId |string |Sim |path |nenhum |ID da página a ser postada. |
| post |many |Sim |corpo |nenhum |Nova mensagem a ser postada. |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 500 |Erro interno do servidor |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
#### <a name="getfeedresponse"></a>GetFeedResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| data |array |não |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| data |array |não |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: uma única entrada no feed de um perfil 
O perfil pode ser um usuário, página, aplicativo ou grupo. 

| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| admin_creator |array |não |
| caption |string |não |
| created_time |string |não |
| Descrição |string |não |
| feed_targeting |não definido |não |
| from |não definido |não |
| ícone |string |não |
| is_hidden |Booliano |não |
| is_published |Booliano |não |
| link |string |não |
| message |string |não |
| Nome |string |não |
| object_id |string |não |
| picture |string |não |
| place |não definido |não |
| privacy |não definido |não |
| propriedades |array |não |
| fonte |string |não |
| status_type |string |não |
| story |string |não |
| targeting |não definido |não |
| para |array |não |
| type |string |não |
| updated_time |string |não |
| with_tags |não definido |não |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: uma única entrada no feed de um perfil 
O perfil pode ser um usuário, página, aplicativo ou grupo.

| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| created_time |string |não |
| from |não definido |não |
| message |string |não |
| type |string |não |

#### <a name="adminitem"></a>AdminItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| link |string |não |

#### <a name="propertyitem"></a>PropertyItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |não |
| texto |string |não |
| href |string |não |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| message |string |sim |
| link |string |não |
| picture |string |não |
| Nome |string |não |
| caption |string |não |
| Descrição |string |não |
| place |string |não |
| marcas |string |não |
| privacy |não definido |não |
| object_attachment |string |não |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| message |string |sim |
| link |string |não |
| picture |string |não |
| Nome |string |não |
| caption |string |não |
| Descrição |string |não |
| Ações |array |não |
| place |string |não |
| marcas |string |não |
| object_attachment |string |não |
| targeting |não definido |não |
| feed_targeting |não definido |não |
| published |booleano |não |
| scheduled_publish_time |string |não |
| backdated_time |string |não |
| backdated_time_granularity |string |não |
| child_attachments |array |não |
| multi_share_end_card |Booliano |não |

#### <a name="postfeedresponse"></a>PostFeedResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |

#### <a name="profilecollection"></a>ProfileCollection
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| data |array |não |

#### <a name="useritem"></a>UserItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| first_name |string |não |
| last_name |string |não |
| Nome |string |não |
| gender |string |não |
| about |string |não |

#### <a name="actionitem"></a>ActionItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |não |
| link |string |não |

#### <a name="targetitem"></a>TargetItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| countries |array |não |
| locales |array |não |
| regions |array |não |
| cities |array |não |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: objeto que controla o direcionamento do feed de notícias dessa publicação
É mais provável que qualquer pessoa nesses grupos veja essa publicação, enquanto em outros a probabilidade é menor. Aplica-se somente às Páginas.

| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| countries |array |não |
| regions |array |não |
| cities |array |não |
| age_min |inteiro |não |
| age_max |inteiro |não |
| genders |array |não |
| relationship_statuses |array |não |
| interested_in |array |não |
| college_years |array |não |
| interests |array |não |
| relevant_until |inteiro |não |
| education_statuses |array |não |
| locales |array |não |

#### <a name="placeitem"></a>PlaceItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| Nome |string |não |
| overall_rating |número |não |
| location |não definido |não |

#### <a name="locationitem"></a>LocationItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| city |string |não |
| country |string |não |
| latitude |número |não |
| located_in |string |não |
| longitude |número |não |
| Nome |string |não |
| region |string |não |
| state |string |não |
| street |string |não |
| zip |string |não |

#### <a name="privacyitem"></a>PrivacyItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Descrição |string |não |
| value |string |sim |
| allow |string |não |
| deny |string |não |
| friends |string |não |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| link |string |não |
| picture |string |não |
| image_hash |string |não |
| Nome |string |não |
| Descrição |string |não |

#### <a name="postphotorequest"></a>PostPhotoRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| url |string |sim |
| caption |string |não |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |sim |
| post_id |string |sim |

#### <a name="postvideorequest"></a>PostVideoRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| videoData |string |sim |
| Descrição |string |sim |
| título |string |sim |
| uploadedVideoName |string |não |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| data |não definido |sim |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| url |string |sim |
| is_silhouette |Booliano |sim |
| height |string |não |
| width |string |não |

#### <a name="geteventresponse"></a>GetEventResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| data |array |sim |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |sim |
| Nome |string |sim |
| start_time |string |não |
| end_time |string |não |
| timezone |string |não |
| location |string |não |
| Descrição |string |não |
| ticket_uri |string |não |
| rsvp_status |string |Sim |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).


