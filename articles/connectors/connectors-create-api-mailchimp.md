---
title: MailChimp | Microsoft Docs
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O MailChimp é um serviço de SaaS que permite que as empresas gerenciem e automatizem atividades de marketing de email, incluindo envio de emails de marketing, mensagens automatizadas e campanhas direcionadas."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3df3dfa25bc438cfe4a340ba50868d2f2263b44a


---
# <a name="get-started-with-the-mailchimp-connector"></a>Introdução ao conector do MailChimp
O MailChimp é um serviço de SaaS que permite que as empresas gerenciem e automatizem atividades de marketing de email, incluindo envio de emails de marketing, mensagens automatizadas e campanhas direcionadas.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do MailChimp pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do MailChimp contém as seguintes ações e/ou gatilhos disponíveis:

### <a name="mailchimp-actions"></a>Ações do MailChimp
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |Cria uma nova campanha com base em um Tipo de Campanha, lista de Destinatários e Configurações da Campanha (subject line, title, from_name e reply_to) |
| [newlist](connectors-create-api-mailchimp.md#newlist) |Cria uma nova lista em sua conta do MailChimp |
| [addmember](connectors-create-api-mailchimp.md#addmember) |Adiciona ou atualiza um membro da lista |
| [removemember](connectors-create-api-mailchimp.md#removemember) |Exclui um membro de uma lista. |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |Atualiza informações de um membro específico da lista |

### <a name="mailchimp-triggers"></a>Gatilhos do MailChimp
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| Quando um Membro tiver sido adicionado a uma lista |Dispara um fluxo de trabalho quando um novo membro tiver sido adicionado a uma lista |
| Quando uma nova lista é criada |Dispara um fluxo de trabalho quando uma nova lista é criada |

## <a name="create-a-connection-to-mailchimp"></a>Criar uma conexão com o MailChimp
Para criar Aplicativos Lógicos com o MailChimp, primeiro, você deve criar uma **conexão** e fornecer os detalhes das seguintes propriedades:

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer as credenciais do MailChimp |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## <a name="reference-for-mailchimp"></a>Referência do MailChimp
Aplica-se à versão: 1.0

## <a name="newcampaign"></a>newcampaign
Nova campanha: cria uma nova campanha com base em um Tipo de Campanha, lista de Destinatários e Configurações da Campanha (subject line, title, from_name e reply_to)

```POST: /campaigns```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |sim |corpo |nenhum |Objeto JSON a ser enviado no corpo com os parâmetros de solicitação da nova campanha |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="newlist"></a>newlist
Nova lista: Cria uma nova lista em sua conta do MailChimp

```POST: /lists```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |sim |corpo |nenhum |Objeto JSON a ser enviado no corpo com os parâmetros de solicitação da nova campanha |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="addmember"></a>addmember
Adicionar membros à lista: adiciona ou atualiza um membro da lista

```POST: /lists/{list_id}/members```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sim |path |nenhum |A ID exclusiva da lista |
| newMemberInList | |sim |corpo |nenhum |Objeto JSON a ser enviado no corpo com as informações do novo membro |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="removemember"></a>removemember
Remover membro da lista: exclui um membro de uma lista.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sim |path |nenhum |A ID exclusiva da lista |
| member_email |string |Sim |path |nenhum |O endereço de email do membro a ser excluído |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="updatemember"></a>updatemember
Atualizar informações do membro: atualiza informações de um membro específico da lista

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sim |path |nenhum |A ID exclusiva da lista |
| member_email |string |Sim |path |nenhum |O endereço de email exclusivo do membro a ser atualizado |
| updateMemberInListRequest | |sim |corpo |nenhum |Objeto JSON a ser enviado no corpo com as informações do membro atualizado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="onmembersubscribed"></a>OnMemberSubscribed
Quando um Membro tiver sido adicionado a uma lista: dispara um fluxo de trabalho quando um novo membro tiver sido adicionado a uma lista

```GET: /trigger/lists/{list_id}/members```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sim |path |nenhum |A ID exclusiva da lista |

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

## <a name="oncreatelist"></a>OnCreateList
Quando uma nova lista é criada: dispara um fluxo de trabalho quando uma nova lista é criada

```GET: /trigger/lists```

Não há parâmetros para essa chamada

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
### <a name="newcampaignrequest"></a>NewCampaignRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| type |string |Sim |
| recipients |não definido |Sim |
| configurações |não definido |Sim |
| variate_settings |não definido |Não |
| tracking |não definido |Não |
| rss_opts |não definido |Não |
| social_card |não definido |Não |

### <a name="recipient"></a>Destinatário
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| list_id |string |Sim |
| segment_opts |não definido |Não |

### <a name="settings"></a>Configurações
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| subject_line |string |Sim |
| título |string |Não |
| from_name |string |Sim |
| reply_to |string |Sim |
| use_conversation |Booliano |Não |
| to_name |string |Não |
| folder_id |inteiro |Não |
| authenticate |Booliano |Não |
| auto_footer |Booliano |Não |
| inline_css |Booliano |Não |
| auto_tweet |Booliano |Não |
| auto_fb_post |array |Não |
| fb_comments |Booliano |Não |

### <a name="variatesettings"></a>Variate_Settings
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| winner_criteria |string |Não |
| wait_time |inteiro |Não |
| test_size |inteiro |Não |
| subject_lines |array |Não |
| send_times |array |Não |
| from_names |array |Não |
| reply_to_addresses |array |Não |

### <a name="tracking"></a>Acompanhamento
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| opens |Booliano |Não |
| html_clicks |Booliano |Não |
| text_clicks |Booliano |Não |
| goal_tracking |Booliano |Não |
| ecomm360 |Booliano |Não |
| google_analytics |string |Não |
| clicktale |string |Não |
| salesforce |não definido |Não |
| highrise |não definido |Não |
| capsule |não definido |Não |

### <a name="rssopts"></a>RSS_Opts
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| feed_url |string |Não |
| frequência |string |Não |
| constrain_rss_img |string |Não |
| schedule |não definido |Não |

### <a name="socialcard"></a>Social_Card
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| image_url |string |Não |
| description |string |Não |
| título |string |Não |

### <a name="segmentopts"></a>Segment_Opts
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| saved_segment_id |inteiro |Não |
| match |string |Não |

### <a name="salesforce"></a>Salesforce
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| campaign |Booliano |Não |
| HDInsight |Booliano |Não |

### <a name="highrise"></a>Highrise
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| campaign |Booliano |Não |
| HDInsight |Booliano |Não |

### <a name="capsule"></a>Capsule
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| HDInsight |Booliano |Não |

### <a name="schedule"></a>Agenda
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| hora |inteiro |Não |
| daily_send |não definido |Não |
| weekly_send_day |string |Não |
| monthly_send_date |número |Não |

### <a name="dailysend"></a>Daily_Send
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| sunday |Booliano |Não |
| monday |Booliano |Não |
| tuesday |Booliano |Não |
| wednesday |Booliano |Não |
| thursday |Booliano |Não |
| friday |Booliano |Não |
| saturday |Booliano |Não |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| type |string |Não |
| create_time |string |Não |
| archive_url |string |Não |
| status |string |Não |
| emails_sent |inteiro |Não |
| send_time |string |Não |
| content_type |string |Não |
| recipient |array |Não |
| configurações |não definido |Não |
| variate_settings |não definido |Não |
| tracking |não definido |Não |
| rss_opts |não definido |Não |
| ab_split_opts |não definido |Não |
| social_card |não definido |Não |
| report_summary |não definido |Não |
| delivery_status |não definido |Não |
| _links |array |Não |

### <a name="absplitopts"></a>AB_Split_Opts
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| split_test |string |Não |
| pick_winner |string |Não |
| wait_units |string |Não |
| wait_time |inteiro |Não |
| split_size |inteiro |Não |
| from_name_a |string |Não |
| from_name_b |string |Não |
| reply_email_a |string |Não |
| reply_email_b |string |Não |
| subject_a |string |Não |
| subject_b |string |Não |
| send_time_a |string |Não |
| send_time_b |string |Não |
| send_time_winner |string |Não |

### <a name="reportsummary"></a>Report_Summary
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| opens |inteiro |Não |
| unique_opens |inteiro |Não |
| open_rate |número |Não |
| clicks |inteiro |Não |
| subscriber_clicks |número |Não |
| click_rate |número |Não |

### <a name="deliverystatus"></a>Delivery_Status
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| habilitado |Booliano |Não |
| can_cancel |Booliano |Não |
| status |string |Não |
| emails_sent |inteiro |Não |
| emails_canceled |inteiro |Não |

### <a name="link"></a>Link
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| rel |string |Não |
| href |string |Não |
| estático |string |Não |
| targetSchema |string |Não |
| schema |string |Não |

### <a name="newlistrequest"></a>NewListRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |Sim |
| contact |não definido |Sim |
| permission_reminder |string |Sim |
| use_archive_bar |Booliano |Não |
| campaign_defaults |não definido |Sim |
| notify_on_subscribe |string |Não |
| notify_on_unsubscribe |string |Não |
| email_type_option |Booliano |Sim |
| visibility |string |Não |

### <a name="contact"></a>Contato
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| company |string |Sim |
| address1 |string |Sim |
| address2 |string |Não |
| city |string |Sim |
| state |string |Sim |
| zip |string |Sim |
| country |string |Sim |
| phone |string |Sim |

### <a name="campaigndefaults"></a>Campaign_Defaults
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| from_name |string |Sim |
| from_email |string |Sim |
| subject |string |Não |
| idioma |string |Sim |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Sim |
| name |string |Sim |
| contact |não definido |Sim |
| permission_reminder |string |Sim |
| use_archive_bar |Booliano |Não |
| campaign_defaults |não definido |Sim |
| notify_on_subscribe |string |Não |
| notify_on_unsubscribe |string |Não |
| date_created |string |Não |
| list_rating |inteiro |Não |
| email_type_option |Booliano |Sim |
| subscribe_url_short |string |Não |
| subscribe_url_long |string |Não |
| beamer_address |string |Não |
| visibility |string |Não |
| modules |array |Não |
| stats |não definido |Não |
| _links |array |Não |

### <a name="stats"></a>Estatísticas
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| member_count |inteiro |Não |
| unsubscribe_count |inteiro |Não |
| cleaned_count |inteiro |Não |
| member_count_since_send |inteiro |Não |
| unsubscribe_count_since_send |inteiro |Não |
| cleaned_count_since_send |inteiro |Não |
| campaign_count |inteiro |Não |
| campaign_last_sent |inteiro |Não |
| merge_field_count |inteiro |Não |
| avg_sub_rate |número |Não |
| avg_unsub_rate |número |Não |
| target_sub_rate |número |Não |
| open_rate |número |Não |
| click_rate |número |Não |
| last_sub_date |string |Não |
| last_unsub_date |string |Não |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| lists |array |Não |
| total_items |inteiro |Não |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| email_type |string |Não |
| status |string |Sim |
| merge_fields |não definido |Não |
| interests |string |Não |
| idioma |string |Não |
| vip |Booliano |Não |
| location |não definido |Não |
| email_address |string |Sim |

### <a name="firstandlastname"></a>FirstAndLastName
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| FNAME |string |Não |
| LNAME |string |Não |

### <a name="location"></a>Local
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| latitude |número |Não |
| longitude |número |Não |

### <a name="memberresponsemodel"></a>MemberResponseModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| email_address |string |Não |
| unique_email_id |string |Não |
| email_type |string |Não |
| status |string |Não |
| merge_fields |não definido |Não |
| interests |string |Não |
| stats |não definido |Não |
| ip_signup |string |Não |
| timestamp_signup |string |Não |
| ip_opt |string |Não |
| timestamp_opt |string |Não |
| member_rating |inteiro |Não |
| last_changed |string |Não |
| idioma |string |Não |
| vip |Booliano |Não |
| email_client |string |Não |
| location |não definido |Não |
| last_note |não definido |Não |
| list_id |string |Não |
| _links |array |Não |

### <a name="lastnote"></a>Last_Note
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| note_id |inteiro |Não |
| created_at |string |Não |
| created_by |string |Não |
| note |string |Não |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| membros |array |Não |
| list_id |string |Não |
| total_items |inteiro |Não |

### <a name="object"></a>Objeto
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| email_address |string |Não |
| email_type |string |Não |
| status |string |Sim |
| merge_fields |não definido |Não |
| interests |string |Não |
| idioma |string |Não |
| vip |Booliano |Não |
| location |não definido |Não |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| membros |array |Não |
| list_id |string |Não |
| total_items |inteiro |Não |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Sim |
| email_address |string |Sim |
| unique_email_id |string |Não |
| email_type |string |Não |
| status |string |Não |
| merge_fields |não definido |Sim |
| interests |string |Não |
| stats |não definido |Não |
| ip_signup |string |Não |
| timestamp_signup |string |Não |
| ip_opt |string |Não |
| timestamp_opt |string |Não |
| member_rating |inteiro |Não |
| last_changed |string |Não |
| idioma |string |Não |
| vip |Booliano |Não |
| email_client |string |Não |
| location |não definido |Não |
| last_note |não definido |Não |
| list_id |string |Não |
| _links |array |Não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


