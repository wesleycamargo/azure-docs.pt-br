---
title: " Usar o Conector do Slack em seus Aplicativos Lógicos | Microsoft Docs"
description: "Introdução ao uso do conector do Slack de atraso em seu aplicativo lógico"
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 404776283ee10aa9410d002597e054757516bcab


---
# <a name="get-started-with-the-slack-connector"></a>Introdução ao conector do Slack
O Slack é uma ferramenta de comunicação da equipe que reúne todas as comunicações de equipe em um só lugar, que pode ser pesquisado instantaneamente e está disponível onde você estiver.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

Com o conector do Slack, você pode:

* Usá-lo para criar aplicativos lógicos

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Vamos conversar sobre gatilhos e ações
O conector do Slack pode ser usado como uma ação. Ele não tem gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML. 

 O conector do Slack possui as seguintes ações e/ou gatilhos disponíveis:

### <a name="slack-actions"></a>Ações do Slack
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| PostMessage |Poste uma mensagem em um canal específico. |

## <a name="create-a-connection-to-slack"></a>Criar uma conexão com o Slack
Para usar o conector do Slack, crie primeiro uma **conexão** e forneça os detalhes dessas propriedades: 

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer credenciais do Slack |

Siga estas etapas para entrar na Margem de atraso e concluir a configuração da **conexão** da Margem de atraso em seu aplicativo lógico:

1. Selecione **Recorrência**
2. Selecione uma **Frequência** e insira um **Intervalo**
3. Selecione **Adicionar uma ação**  
   ![Configurar a Margem de atraso][1]  
4. Insira Margem de atraso na caixa de pesquisa e aguarde até que a pesquisa retorne todas as entradas com Margem de atraso no nome
5. Selecione **Margem de atraso – Postar mensagem**
6. Selecione **Entrar no Slack**:  
   ![Configurar a Margem de atraso][2]
7. Fornecer suas credenciais do Slack para entrar e autorizar o aplicativo    
   ![Configurar a Margem de atraso][3]  
8. Você será redirecionado à página de logon de sua organização. **Autorize** o Slack a interagir com seu aplicativo lógico:      
   ![Configurar a Margem de atraso][5] 
9. Após o término da autorização, você será redirecionado para seu aplicativo lógico para concluí-lo configurando a seção **Slack - Obter todas as mensagens**. Adicione outros gatilhos e outras ações necessárias.  
   ![Configurar a Margem de atraso][6]
10. Salve seu trabalho selecionando **Salvar** na barra de menus acima.

> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## <a name="slack-rest-api-reference"></a>Referência da API REST do Slack
#### <a name="this-documentation-is-for-version-10"></a>Esta documentação destina-se à versão: 1.0
### <a name="post-a-message-to-a-specified-channel"></a>Poste uma mensagem em um canal específico.
**```POST: /chat.postMessage```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| canal |string |sim |query |nenhum |Canal, grupo privado ou canal de IM para envio de mensagem. Pode ser um nome (ex: #geral) ou uma ID codificada. |
| texto |string |sim |query |nenhum |Texto da mensagem a ser enviada. Para ver as opções de formatação, consulte https://api.slack.com/docs/formatting. |
| Nome de Usuário |string |não |query |nenhum |Nome do bot |
| as_user |booleano |não |query |nenhum |Passa true para postar a mensagem como o usuário autenticado em vez de um bot |
| analisar |string |não |query |nenhum |Altera como as mensagens são tratadas. Para obter detalhes, consulte https://api.slack.com/docs/formatting. |
| link_names |inteiro |não |query |nenhum |Localiza e vincula os nomes de canal e nomes de usuário. |
| unfurl_links |booleano |não |query |nenhum |Passe true para habilitar o desdobramento do conteúdo basicamente baseado em texto. |
| unfurl_media |booleano |não |query |nenhum |Passe false para desabilitar o conteúdo de mídia. |
| icon_url |string |não |query |nenhum |URL para uma imagem a ser usada como ícone para esta mensagem |
| icon_emoji |string |não |query |nenhum |Emoji a ser usado como ícone para esta mensagem |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 408 |Tempo Limite da Solicitação |
| 429 |Número Excessivo de Solicitações |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| 503 |Serviço do Slack Indisponível |
| 504 |Tempo Limite do Gateway |
| padrão |Falha na Operação. |

- - -
## <a name="object-definitions"></a>Definições de objeto:
 **Mensagem**: Mensagem do Slack

Propriedades necessárias para a mensagem:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| ID |inteiro |
| content_excerpt |string |
| sender_id |inteiro |
| replied_to_id |inteiro |
| created_at |string |
| network_id |inteiro |
| message_type |string |
| sender_type |string |
| url |string |
| web_url |string |
| group_id |inteiro |
| corpo |não definido |
| thread_id |inteiro |
| direct_message |booleano |
| client_type |string |
| client_url |string |
| idioma |string |
| notified_user_ids |array |
| privacidade |string |
| liked_by |não definido |
| system_message |booleano |

 **PostOperationRequest**: representa uma solicitação POST para o conector do Slack postar no Slack

Propriedades obrigatórias para PostOperationRequest:

corpo

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| corpo |string |
| group_id |inteiro |
| replied_to_id |inteiro |
| direct_to_id |inteiro |
| difusão |booleano |
| topic1 |string |
| topic2 |string |
| topic3 |string |
| topic4 |string |
| topic5 |string |
| topic6 |string |
| topic7 |string |
| topic8 |string |
| topic9 |string |
| topic10 |string |
| topic11 |string |
| topic12 |string |
| topic13 |string |
| topic14 |string |
| topic15 |string |
| topic16 |string |
| topic17 |string |
| topic18 |string |
| topic19 |string |
| topic20 |string |

 **MessageList**: lista de mensagens

Propriedades obrigatórias para MessageList:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| mensagens |array |

 **MessageBody**: corpo da mensagem

Propriedades obrigatórias para MessageBody:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| analisado |string |
| sem formatação |string |
| avançado |string |

 **LikedBy**: curtido por

Propriedades obrigatórias para LikedBy:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| count |inteiro |
| nomes |array |

 **YammmerEntity**: curtido por

Propriedades obrigatórias para YammmerEntity:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| type |string |
| ID |inteiro |
| full_name |string |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)

## <a name="object-definitions"></a>Definições de objeto:
 **WebResultModel**: resultados da pesquisa da Web do Bing

Propriedades obrigatórias para WebResultModel:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| Title |string |
| Descrição |string |
| DisplayUrl |string |
| ID |string |
| FullUrl |string |

 **VideoResultModel**: resultados da pesquisa de vídeo do Bing

Propriedades obrigatórias para VideoResultModel:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| Title |string |
| DisplayUrl |string |
| ID |string |
| MediaUrl |string |
| Tempo de execução |inteiro |
| Miniatura |não definido |

 **ThumbnailModel**: propriedades de miniatura do elemento de multimídia

Propriedades obrigatórias para ThumbnailModel:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| MediaUrl |string |
| ContentType |string |
| Largura |inteiro |
| Altura |inteiro |
| FileSize |inteiro |

 **ImageResultModel**: resultados da pesquisa de imagens do Bing

Propriedades obrigatórias para ImageResultModel:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| Title |string |
| DisplayUrl |string |
| ID |string |
| MediaUrl |string |
| SourceUrl |string |
| Miniatura |não definido |

 **NewsResultModel**: resultados da pesquisa de notícias do Bing

Propriedades obrigatórias para NewsResultModel:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| Title |string |
| Descrição |string |
| DisplayUrl |string |
| ID |string |
| Fonte |string |
| Data |string |

 **SpellResultModel**: resultados das sugestões de ortografia do Bing

Propriedades obrigatórias para SpellResultModel:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| ID |string |
| Valor |string |

 **RelatedSearchResultModel**: resultados da pesquisa relacionada do Bing

Propriedades obrigatórias para RelatedSearchResultModel:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| Title |string |
| ID |string |
| BingUrl |string |

 **CompositeSearchResultModel**: resultados da pesquisa composta do Bing

Propriedades obrigatórias para CompositeSearchResultModel:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| WebResultsTotal |inteiro |
| ImageResultsTotal |inteiro |
| VideoResultsTotal |inteiro |
| NewsResultsTotal |inteiro |
| SpellSuggestionsTotal |inteiro |
| WebResults |array |
| ImageResults |array |
| VideoResults |array |
| NewsResults |array |
| SpellSuggestionResults |array |
| RelatedSearchResults |array |

## <a name="object-definitions"></a>Definições de objeto:
 **PostOperationResponse**: representa a resposta da operação de postagem do Conector da Margem de atraso para postar no Margem de atraso

Propriedades obrigatórias para PostOperationResponse:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| OK |booleano |
| canal |string |
| ts |string |
| Message |não definido |
| error |string |

 **MessageItem**: uma mensagem de canal.

Propriedades obrigatórias para MessageItem:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| texto |string |
| ID |string |
| usuário |string |
| criado |inteiro |
| is_user-deleted |booleano |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png



<!--HONumber=Jan17_HO3-->


