<properties
	pageTitle="Adicionar a API dos Usuários do Office 365 aos seus Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da API dos Usuários do Office 365 com os parâmetros da API REST"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Introdução à API dos Usuários do Office 365

O provedor de Conexão de Usuários do Office 365 permite acessar perfis de usuário na sua organização usando sua conta do Office 365. Você pode executar várias ações como obter seu perfil, um perfil do usuário, do gerente de um usuário ou subalternos diretos e também atualizar um perfil do usuário.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [API do Office 365](../app-service-logic/app-service-logic-connector-office365.md).


Com os Usuários do Office 365, você pode:

* Use-o para compilar aplicativos lógicos
* Use-o para compilar aplicativos power

Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação a aplicativos lógicos, consulte [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Agora vamos falar sobre gatilhos e ações

A API dos Usuários do Office 365 pode ser usada como uma ação; não há nenhum gatilho. Todas as APIs dão suporte a dados nos formatos JSON e XML.

 A API dos Usuários do Office 365 tem as seguintes ações e/ou gatilhos disponíveis:

### Ações de Usuários do Office 365
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|MyProfile|Recupera o perfil do usuário atual|
|UserProfile|Recupera um perfil do usuário específico|
|Gerente|Recupera o perfil do usuário para o gerente do usuário especificado|
|DirectReports|Obter relatórios diretos|
|SearchUser|Recupera os resultados da pesquisa de perfis de usuário|
## Criar uma conexão com os Usuários do Office 365
Para usar a API dos Usuários do Office 365, crie primeiro uma **conexão**, em seguida, forneça os detalhes dessas propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Forneça as Credenciais do Office365|


>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos lógicos.

## Referência da API REST dos Usuários do Office 365
#### Esta documentação destina-se à versão: 1.0


### Obter meu perfil 


 Recupera o perfil para o usuário atual ```GET: /users/me```

Não há parâmetros para esta chamada
#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|202|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Obter o perfil do usuário 


 Recupera um perfil do usuário específico ```GET: /users/{userId}```



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|coluna|string|sim|path|nenhum|Nome UPN ou ID do email|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|202|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Obter o gerenciador 


 Recupera o perfil do usuário para o gerente do usuário especificado ```GET: /users/{userId}/manager```



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|coluna|string|sim|path|nenhum|Nome UPN ou ID do email|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|202|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Obter relatórios diretos 


 Obter relatórios diretos ```GET: /users/{userId}/directReports```



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|coluna|string|sim|path|nenhum|Nome UPN ou ID do email|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|202|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Pesquisar por usuários 


 Recupera os resultados da pesquisa dos perfis de usuário ```GET: /users```



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|searchTerm|string|não|query|nenhum|Pesquisar cadeia de caracteres (aplica-se a: nome de exibição, nome, sobrenome, email, apelido de email e nome UPN)|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|202|Operação concluída com êxito|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



## Definições de objeto: 

 **Usuário**: classe de modelo de usuário.

Propriedades obrigatórias para o Usuário:

ID

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|DisplayName|string|
|GivenName|string|
|Sobrenome|string|
|Email|string|
|MailNickname|string|
|TelephoneNumber|string|
|AccountEnabled|booleano|
|ID|string|
|UserPrincipalName|string|
|Departamento|string|
|JobTitle|string|


## Próximas etapas
Depois de adicionar a API do Office 365 ao PowerApps Enterprise, [conceda permissões aos usuários](../power-apps/powerapps-manage-api-connection-user-access.md) para usar a API em seus aplicativos.

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->