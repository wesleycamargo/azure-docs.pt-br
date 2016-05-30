<properties
pageTitle="GitHub | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O GitHub é um serviço de hospedagem do repositório Git baseado na Web. Ele oferece toda a funcionalidade de controle de revisão distribuída e de SCM (gerenciamento do código-fonte) do Git, além de adicionar seus próprios recursos."
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# Introdução ao conector do GitHub



O conector do GitHub pode ser usado por meio de:

- [Aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Fluxo](http://flows.microsoft.com)  

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do GitHub pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do GitHub contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do GitHub
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Cria um problema|
### Gatilhos do GitHub
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|Quando um problema é aberto|Um problema é aberto|
|Quando um problema é fechado|Um problema é fechado|
|Quando um problema é atribuído|Um problema é atribuído|


## Criar uma conexão com o GitHub
Para criar Aplicativos Lógicos com o GitHub, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer as credenciais do GitHub|
Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

>[AZURE.INCLUDE [Etapas para criar uma conexão com o GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência do GitHub
Aplica-se à versão: 1.0

## CreateIssue
Criar um problema: cria um problema

```POST: /repos/{repositoryOwner}/{repositoryName}/issues```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|repositoryOwner|string|sim|path|nenhum|Proprietário do repositório|
|repositoryName|string|sim|path|nenhum|Nome do repositório|
|issueBasicDetails| |sim|corpo|nenhum|Detalhes do problema|

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


## IssueOpened
Quando um problema é aberto: um problema é aberto

```GET: /trigger/issueOpened```

Não há parâmetros para essa chamada
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


## IssueClosed
Quando um problema é fechado: um problema é fechado

```GET: /trigger/issueClosed```

Não há parâmetros para essa chamada
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


## IssueAssigned
Quando um problema é atribuído: um problema é atribuído

```GET: /trigger/issueAssigned```

Não há parâmetros para essa chamada
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


## Definições de objeto 

### IssueBasicDetailsModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|título|string|Sim |
|body|string|Sim |
|assignee|string|Sim |



### IssueDetailsModel


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|título|string|Sim |
|body|string|Sim |
|assignee|string|Sim |
|número|string|Não |
|state|string|Não |
|created\_at|string|Não |
|repository\_url|string|Não |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->