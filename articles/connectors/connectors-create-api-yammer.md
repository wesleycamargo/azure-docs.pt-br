---
title: "Adicionar o Conector do Yammer aos seus Aplicativos Lógicos | Microsoft Docs"
description: "Visão geral do Conector do Yammer com os parâmetros da API REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 104edc61f964f5e0b6c2144a39201283b71145a2


---
# <a name="get-started-with-the-yammer-connector"></a>Introdução ao conector do Yammer
Conecte-se ao Yammer para acessar conversas em sua rede corporativa.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

Com o Yammer, você pode:

* Crie seu fluxo de negócios baseado nos dados obtidos do Yammer. 
* Use gatilhos para quando há uma nova mensagem em um grupo ou um feed a seguir.
* Use as ações para postar uma mensagem, obter todas as mensagens e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando uma nova mensagem for exibida, você poderá enviar um email usando o Office 365.

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O Yammer inclui os gatilhos e as ações a seguir. 

| Gatilho | Ações |
| --- | --- |
| <ul><li>Quando existir uma nova mensagem num grupo</li><li>Quando existir uma nova mensagem no meu feed Seguir</li></ul> |<ul><li>Obter todas as mensagens</li><li>Obtém as mensagens em um grupo</li><li>Obtém as mensagens do meu feed Seguir</li><li>Postar mensagem</li><li>Quando existir uma nova mensagem num grupo</li><li>Quando existir uma nova mensagem no meu feed Seguir</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML. 

## <a name="create-a-connection-to-yammer"></a>Criar uma conexão com o Yammer
Para usar o conector do Yammer, você primeiro cria uma **conexão**, em seguida, fornece os detalhes dessas propriedades: 

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer suas credenciais do Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## <a name="yammer-rest-api-reference"></a>Referência da API REST do Yammer
Esta documentação destina-se à versão: 1.0

### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Obter todas as mensagens públicas na rede do Yammer do usuário conectado
Corresponde a “Todas” as conversas na interface Web do Yammer.  
```GET: /messages.json```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| older_then |inteiro |não |query |nenhum |Retorna mensagens mais antigas que a ID de mensagem especificada como cadeia de caracteres numéricos. Isso é útil para paginação de mensagens. Por exemplo, se você está vendo 20 mensagens e a mais antiga tem o número 2912, você pode acrescentar "?older_than=2912" à sua solicitação para obter as 20 mensagens anteriores às que você está vendo. |
| newer_then |inteiro |não |query |nenhum |Retorna mensagens mais novas que a ID de mensagem especificada como cadeia de caracteres numéricos. Deve ser usado ao pesquisar novas mensagens. Se você estiver examinando mensagens e a última mensagem retornada for 3516, poderá fazer uma solicitação com o parâmetro "?newer_than=3516" para garantir que não obtenha cópias duplicadas de mensagens já em sua página. |
| limite |inteiro |não |query |nenhum |Retorna somente o número especificado de mensagens. |
| página |inteiro |não |query |nenhum |Obtém a página especificada. Se os dados retornados forem maiores que o limite, você poderá usar esse campo para acessar as páginas subsequentes |

### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 408 |Tempo Limite da Solicitação |
| 429 |Número Excessivo de Solicitações |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| 503 |Serviço do Yammer Indisponível |
| 504 |Tempo Limite do Gateway |
| padrão |Falha na Operação. |

### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Postar uma mensagem em um grupo ou no Feed de Toda a Empresa
Se a ID do grupo for fornecida, a mensagem será postada no grupo especificado. Caso contrário, ela será postada no Feed de Toda a Empresa.    
```POST: /messages.json``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| input | |Sim |corpo |nenhum |Postar solicitação de mensagem |

### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 201 |Criado |

## <a name="object-definitions"></a>Definições de objeto
#### <a name="message-yammer-message"></a>Mensagem: mensagem do Yammer
| Nome | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |não |
| content_excerpt |string |não |
| sender_id |inteiro |não |
| replied_to_id |inteiro |não |
| created_at |string |não |
| network_id |inteiro |não |
| message_type |string |não |
| sender_type |string |não |
| url |string |não |
| web_url |string |não |
| group_id |inteiro |não |
| corpo |não definido |não |
| thread_id |inteiro |não |
| direct_message |booleano |não |
| client_type |string |não |
| client_url |string |não |
| idioma |string |não |
| notified_user_ids |array |não |
| privacidade |string |não |
| liked_by |não definido |não |
| system_message |booleano |não |

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: representa uma solicitação POST para o conector do Yammer postar no Yammer
| Nome | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| corpo |string |sim |
| group_id |inteiro |não |
| replied_to_id |inteiro |não |
| direct_to_id |inteiro |não |
| difusão |booleano |não |
| topic1 |string |não |
| topic2 |string |não |
| topic3 |string |não |
| topic4 |string |não |
| topic5 |string |não |
| topic6 |string |não |
| topic7 |string |não |
| topic8 |string |não |
| topic9 |string |não |
| topic10 |string |não |
| topic11 |string |não |
| topic12 |string |não |
| topic13 |string |não |
| topic14 |string |não |
| topic15 |string |não |
| topic16 |string |não |
| topic17 |string |não |
| topic18 |string |não |
| topic19 |string |não |
| topic20 |string |não |

#### <a name="messagelist-list-of-messages"></a>MessageList: lista de mensagens
| Nome | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| mensagens |array |não |

#### <a name="messagebody-message-body"></a>MessageBody: corpo da mensagem
| Nome | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| analisado |string |não |
| sem formatação |string |não |
| avançado |string |não |

#### <a name="likedby-liked-by"></a>LikedBy: curtido por
| Nome | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| count |inteiro |não |
| nomes |array |não |

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: curtido por
| Nome | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| type |string |não |
| ID |inteiro |não |
| full_name |string |não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png



<!--HONumber=Dec16_HO2-->


