<properties
pageTitle="MailChimp | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O MailChimp é um serviço de SaaS que permite que as empresas gerenciem e automatizem atividades de marketing de email, incluindo envio de emails de marketing, mensagens automatizadas e campanhas direcionadas."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# Introdução ao conector do MailChimp



O conector do MailChimp pode ser usado por meio de:

- [Aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Fluxos](http://flows.microsoft.com)  

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do MailChimp pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do MailChimp contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do MailChimp
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Cria uma nova campanha com base em um Tipo de Campanha, lista de Destinatários e Configurações da Campanha (subject line, title, from\_name e reply\_to)|
|[newlist](connectors-create-api-mailchimp.md#newlist)|Cria uma nova lista em sua conta do MailChimp|
|[addmember](connectors-create-api-mailchimp.md#addmember)|Adiciona ou atualiza um membro da lista|
|[removemember](connectors-create-api-mailchimp.md#removemember)|Exclui um membro de uma lista.|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Atualiza informações de um membro específico da lista|
### Gatilhos do MailChimp
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|Quando um Membro tiver sido adicionado a uma lista|Dispara um fluxo de trabalho quando um novo membro tiver sido adicionado a uma lista|
|Quando uma nova lista é criada|Dispara um fluxo de trabalho quando uma nova lista é criada|


## Criar uma conexão com o MailChimp
Para criar Aplicativos Lógicos com o MailChimp, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer as credenciais do MailChimp|
Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência do MailChimp
Aplica-se à versão: 1.0

## newcampaign
Nova campanha: cria uma nova campanha com base em um Tipo de Campanha, lista de Destinatários e Configurações da Campanha (subject line, title, from\_name e reply\_to)

```POST: /campaigns```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|newCampaignRequest| |sim|corpo|nenhum|Objeto JSON a ser enviado no corpo com os parâmetros de solicitação da nova campanha|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## newlist
Nova lista: Cria uma nova lista em sua conta do MailChimp

```POST: /lists```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|newListRequest| |sim|corpo|nenhum|Objeto JSON a ser enviado no corpo com os parâmetros de solicitação da nova campanha|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## addmember
Adicionar membros à lista: adiciona ou atualiza um membro da lista

```POST: /lists/{list_id}/members```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|string|sim|path|nenhum|A ID exclusiva da lista|
|newMemberInList| |sim|corpo|nenhum|Objeto JSON a ser enviado no corpo com as informações do novo membro|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## removemember
Remover membro da lista: exclui um membro de uma lista.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|string|sim|path|nenhum|A ID exclusiva da lista|
|member\_email|string|sim|path|nenhum|O endereço de email do membro a ser excluído|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## updatemember
Atualizar informações do membro: atualiza informações de um membro específico da lista

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|string|sim|path|nenhum|A ID exclusiva da lista|
|member\_email|string|sim|path|nenhum|O endereço de email exclusivo do membro a ser atualizado|
|updateMemberInListRequest| |sim|corpo|nenhum|Objeto JSON a ser enviado no corpo com as informações do membro atualizado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## OnMemberSubscribed
Quando um Membro tiver sido adicionado a uma lista: dispara um fluxo de trabalho quando um novo membro tiver sido adicionado a uma lista

```GET: /trigger/lists/{list_id}/members```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|string|sim|path|nenhum|A ID exclusiva da lista|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## OnCreateList
Quando uma nova lista é criada: dispara um fluxo de trabalho quando uma nova lista é criada

```GET: /trigger/lists```

Não há parâmetros para essa chamada
#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## Definições de objeto 

### NewCampaignRequest


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|type|string|Sim |
|recipients|não definido|Sim |
|configurações|não definido|Sim |
|variate\_settings|não definido|Não |
|tracking|não definido|Não |
|rss\_opts|não definido|Não |
|social\_card|não definido|Não |



### Destinatário


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|list\_id|string|Sim |
|segment\_opts|não definido|Não |



### Configurações


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|subject\_line|string|Sim |
|título|string|Não |
|from\_name|string|Sim |
|reply\_to|string|Sim |
|use\_conversation|booleano|Não |
|to\_name|string|Não |
|folder\_id|inteiro|Não |
|authenticate|booleano|Não |
|auto\_footer|booleano|Não |
|inline\_css|booleano|Não |
|auto\_tweet|booleano|Não |
|auto\_fb\_post|array|Não |
|fb\_comments|booleano|Não |



### Variate\_Settings


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|winner\_criteria|string|Não |
|wait\_time|inteiro|Não |
|test\_size|inteiro|Não |
|subject\_lines|array|Não |
|send\_times|array|Não |
|from\_names|array|Não |
|reply\_to\_addresses|array|Não |



### Acompanhamento


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|opens|booleano|Não |
|html\_clicks|booleano|Não |
|text\_clicks|booleano|Não |
|goal\_tracking|booleano|Não |
|ecomm360|booleano|Não |
|google\_analytics|string|Não |
|clicktale|string|Não |
|salesforce|não definido|Não |
|highrise|não definido|Não |
|capsule|não definido|Não |



### RSS\_Opts


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|feed\_url|string|Não |
|frequência|string|Não |
|constrain\_rss\_img|string|Não |
|schedule|não definido|Não |



### Social\_Card


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|image\_url|string|Não |
|description|string|Não |
|título|string|Não |



### Segment\_Opts


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|saved\_segment\_id|inteiro|Não |
|match|string|Não |



### Salesforce


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|campaign|booleano|Não |
|HDInsight|booleano|Não |



### Highrise


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|campaign|booleano|Não |
|HDInsight|booleano|Não |



### Capsule


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|HDInsight|booleano|Não |



### Agenda


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|hora|inteiro|Não |
|daily\_send|não definido|Não |
|weekly\_send\_day|string|Não |
|monthly\_send\_date|número|Não |



### Daily\_Send


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|sunday|booleano|Não |
|monday|booleano|Não |
|tuesday|booleano|Não |
|wednesday|booleano|Não |
|thursday|booleano|Não |
|friday|booleano|Não |
|saturday|booleano|Não |



### CampaignResponseModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Não |
|type|string|Não |
|create\_time|string|Não |
|archive\_url|string|Não |
|status|string|Não |
|emails\_sent|inteiro|Não |
|send\_time|string|Não |
|content\_type|string|Não |
|recipient|array|Não |
|configurações|não definido|Não |
|variate\_settings|não definido|Não |
|tracking|não definido|Não |
|rss\_opts|não definido|Não |
|ab\_split\_opts|não definido|Não |
|social\_card|não definido|Não |
|report\_summary|não definido|Não |
|delivery\_status|não definido|Não |
|_\_links|array|No |



### AB\_Split\_Opts


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|split\_test|string|Não |
|pick\_winner|string|Não |
|wait\_units|string|Não |
|wait\_time|inteiro|Não |
|split\_size|inteiro|Não |
|from\_name\_a|string|Não |
|from\_name\_b|string|Não |
|reply\_email\_a|string|Não |
|reply\_email\_b|string|Não |
|subject\_a|string|Não |
|subject\_b|string|Não |
|send\_time\_a|string|Não |
|send\_time\_b|string|Não |
|send\_time\_winner|string|Não |



### Report\_Summary


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|opens|inteiro|Não |
|unique\_opens|inteiro|Não |
|open\_rate|número|Não |
|clicks|inteiro|Não |
|subscriber\_clicks|número|Não |
|click\_rate|número|Não |



### Delivery\_Status


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|habilitado|booleano|Não |
|can\_cancel|booleano|Não |
|status|string|Não |
|emails\_sent|inteiro|Não |
|emails\_canceled|inteiro|Não |



### Link


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|rel|string|Não |
|href|string|Não |
|estático|string|Não |
|targetSchema|string|Não |
|schema|string|Não |



### NewListRequest


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|name|cadeia de caracteres|Sim |
|contact|não definido|Sim |
|permission\_reminder|string|Sim |
|use\_archive\_bar|booleano|Não |
|campaign\_defaults|não definido|Sim |
|notify\_on\_subscribe|string|Não |
|notify\_on\_unsubscribe|string|Não |
|email\_type\_option|booleano|Sim |
|visibility|string|Não |



### Contato


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|company|string|Sim |
|address1|string|Sim |
|address2|string|Não |
|city|string|Sim |
|state|string|Sim |
|zip|string|Sim |
|country|string|Sim |
|phone|string|Sim |



### Campaign\_Defaults


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|from\_name|string|Sim |
|from\_email|string|Sim |
|subject|string|Não |
|idioma|string|Sim |



### CreateNewListResponseModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Sim |
|name|cadeia de caracteres|Sim |
|contact|não definido|Sim |
|permission\_reminder|string|Sim |
|use\_archive\_bar|booleano|Não |
|campaign\_defaults|não definido|Sim |
|notify\_on\_subscribe|string|Não |
|notify\_on\_unsubscribe|string|Não |
|date\_created|string|Não |
|list\_rating|inteiro|Não |
|email\_type\_option|booleano|Sim |
|subscribe\_url\_short|string|Não |
|subscribe\_url\_long|string|Não |
|beamer\_address|string|Não |
|visibility|string|Não |
|modules|array|Não |
|stats|não definido|Não |
|_\_links|array|No |



### Estatísticas


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|member\_count|inteiro|Não |
|unsubscribe\_count|inteiro|Não |
|cleaned\_count|inteiro|Não |
|member\_count\_since\_send|inteiro|Não |
|unsubscribe\_count\_since\_send|inteiro|Não |
|cleaned\_count\_since\_send|inteiro|Não |
|campaign\_count|inteiro|Não |
|campaign\_last\_sent|inteiro|Não |
|merge\_field\_count|inteiro|Não |
|avg\_sub\_rate|número|Não |
|avg\_unsub\_rate|número|Não |
|target\_sub\_rate|número|Não |
|open\_rate|número|Não |
|click\_rate|número|Não |
|last\_sub\_date|string|Não |
|last\_unsub\_date|string|Não |



### GetListsResponseModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|lists|array|Não |
|total\_items|inteiro|Não |



### NewMemberInListRequest


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|email\_type|string|Não |
|status|string|Sim |
|merge\_fields|não definido|Não |
|interests|string|Não |
|idioma|string|Não |
|vip|booleano|Não |
|location|não definido|Não |
|email\_address|string|Sim |



### FirstAndLastName


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|FNAME|string|Não |
|LNAME|string|Não |



### Local padrão


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|latitude|número|Não |
|longitude|número|Não |



### MemberResponseModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Não |
|email\_address|string|Não |
|unique\_email\_id|string|Não |
|email\_type|string|Não |
|status|string|Não |
|merge\_fields|não definido|Não |
|interests|string|Não |
|stats|não definido|Não |
|ip\_signup|string|Não |
|timestamp\_signup|string|Não |
|ip\_opt|string|Não |
|timestamp\_opt|string|Não |
|member\_rating|inteiro|Não |
|last\_changed|string|Não |
|idioma|string|Não |
|vip|booleano|Não |
|email\_client|string|Não |
|location|não definido|Não |
|last\_note|não definido|Não |
|list\_id|string|Não |
|_\_links|array|No |



### Last\_Note


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|note\_id|inteiro|Não |
|created\_at|string|Não |
|created\_by|string|Não |
|note|string|Não |



### GetAllMembersResponseModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|membros|array|Não |
|list\_id|string|Não |
|total\_items|inteiro|Não |



### Objeto


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|



### UpdateMemberInListRequest


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|email\_address|string|Não |
|email\_type|string|Não |
|status|string|Sim |
|merge\_fields|não definido|Não |
|interests|string|Não |
|idioma|string|Não |
|vip|booleano|Não |
|location|não definido|Não |



### GetMembersResponseModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|membros|array|Não |
|list\_id|string|Não |
|total\_items|inteiro|Não |



### AddUserResponseModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Sim |
|email\_address|string|Sim |
|unique\_email\_id|string|Não |
|email\_type|string|Não |
|status|string|Não |
|merge\_fields|não definido|Sim |
|interests|string|Não |
|stats|não definido|Não |
|ip\_signup|string|Não |
|timestamp\_signup|string|Não |
|ip\_opt|string|Não |
|timestamp\_opt|string|Não |
|member\_rating|inteiro|Não |
|last\_changed|string|Não |
|idioma|string|Não |
|vip|booleano|Não |
|email\_client|string|Não |
|location|não definido|Não |
|last\_note|não definido|Não |
|list\_id|string|Não |
|_\_links|array|No |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->