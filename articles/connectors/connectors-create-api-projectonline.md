<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Project Online é uma solução online flexível da Microsoft para PPM (gerenciamento de portfólio de projetos) e trabalho cotidiano. Distribuído por meio do Office 365, o Project Online permite que as organizações se familiarizem rapidamente com funcionalidades avançadas de gerenciamento de projetos para planejar, priorizar e gerenciar projetos e investimentos em portfólio de projetos – praticamente, em qualquer lugar e em qualquer dispositivo."
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
ms.date="05/16/2016"
ms.author="deonhe"/>

# Introdução ao conector do ProjectOnline

O Project Online é uma solução online flexível da Microsoft para PPM (gerenciamento de portfólio de projetos) e trabalho cotidiano. Distribuído por meio do Office 365, o Project Online permite que as organizações se familiarizem rapidamente com funcionalidades avançadas de gerenciamento de projetos para planejar, priorizar e gerenciar projetos e investimentos em portfólio de projetos – praticamente, em qualquer lugar e em qualquer dispositivo.

O conector do ProjectOnline pode ser usado por meio de:

- [Aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Fluxo](http://flow.microsoft.com)  

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do ProjectOnline pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do ProjectOnline contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do ProjectOnline
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Lista os projetos no site online do projeto|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Cria um novo projeto no site online do projeto|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Cria uma nova tarefa no projeto|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Cria um Recurso Empresarial no site online do projeto|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Lista as tarefas publicadas em um projeto|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Faz check-out de um projeto no site|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Faz check-in e publica um projeto existente no site|
### Gatilhos do ProjectOnline
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|Quando um novo projeto é criado|Dispara um fluxo sempre que um novo projeto é criado|
|Quando um novo recurso é criado|Dispara um novo fluxo quando um novo recurso é criado|
|Quando uma nova tarefa é criada|Dispara um fluxo quando uma nova tarefa é criada|


## Criar uma conexão com o ProjectOnline
Para criar Aplicativos Lógicos com o ProjectOnline, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer as credenciais do ProjectOnline|

>[AZURE.INCLUDE [Etapas para criar uma conexão com o ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência do ProjectOnline
Aplica-se à versão: 1.0

## OnNewProject
Quando um novo projeto é criado: dispara um fluxo sempre que um novo projeto é criado

```GET: /trigger/_api/ProjectData/Projects```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## OnNewResource
Quando um novo recurso é criado: dispara um novo fluxo quando um novo recurso é criado

```GET: /trigger/_api/ProjectData/Resources```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## OnNewTask
Quando uma nova tarefa é criada: dispara um fluxo quando uma nova tarefa é criada

```GET: /trigger/_api/ProjectData/Tasks```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## ListProjects
Listar projetos: lista os projetos no site online do projeto

```GET: /_api/ProjectServer/Projects```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## CreateProject
Criar novo projeto: cria um novo projeto no site online do projeto

```POST: /_api/ProjectServer/Projects```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|proj| |sim|corpo|nenhum|Novo projeto a ser criado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|ForbIDden|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## CreateTask
Criar nova tarefa: cria uma nova tarefa no projeto

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|sim|path|nenhum|ID exclusiva do projeto ao qual a tarefa será adicionada|
|task| |sim|corpo|nenhum|Nova tarefa a ser adicionada ao projeto|

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


## CreateResource
Criar novo recurso: Cria um Recurso Empresarial no site online do projeto

```POST: /_api/ProjectServer/EnterpriseResources```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|recurso| |sim|corpo|nenhum|Novo recurso empresarial a ser adicionado ao projeto|

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


## ListTasks
Listar tarefas: lista as tarefas publicadas em um projeto

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|sim|path|nenhum|ID exclusiva do projeto para buscar tarefas|

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


## CheckoutProject
Fazer check-out de um projeto: faz check-out de um projeto no site

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|sim|path|nenhum|ID exclusiva do projeto ao qual a tarefa será adicionada|

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


## PublishProject
Fazer check-in e publicar um projeto: faz check-in e publica um projeto existente no site

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|siteUrl|string|sim|query|nenhum|URL do site raiz do projeto (exemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|string|sim|path|nenhum|ID exclusiva do projeto para fazer check-in|

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

### TriggerProjectsWrapper


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### TriggerProject


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ProjectStartDate|string|Não |
|ProjectFinishDate|string|Não |
|ProjectCreatedDate|string|Não |
|ProjectId|string|Não |
|ProjectModifiedDate|string|Não |
|ProjectType|inteiro|Não |
|ProjectName|string|Não |



### TriggerResourcesWrapper


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### TriggerResource


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ResourceId|string|Não |
|ResourceBaseCalendar|string|Não |
|ResourceBookingType|inteiro|Não |
|ResourceCanLevel|booleano|Não |
|ResourceCostPerUse|número|Não |
|ResourceCreatedDate|string|Não |
|ResourceEarliestAvailableFrom|string|Não |
|ResourceEmail|string|Não |
|ResourceInitials|string|Não |
|ResourceIsActive|booleano|Não |
|ResourceIsGeneric|booleano|Não |
|ResourceLatestAvailableTo|string|Não |
|ResourceModifiedDate|string|Não |
|ResourceName|string|Não |
|ResourceStatsuName|string|Não |
|ResourceType|inteiro|Não |
|TypeDescription|string|Não |
|TypeName|string|Não |



### TriggerTasksWrapper


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### TriggerTask


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ProjectId|string|Não |
|TaskId|string|Não |
|ProjectName|string|Não |
|TaskName|string|Não |
|TaskCreatedDate|string|Não |
|TaskModifieddate|string|Não |
|TaskStartDate|string|Não |
|TaskFinishDate|string|Não |
|TaskPriority|inteiro|Não |
|TaskIsActive|booleano|Não |



### NewProject


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Nome|string|Sim |
|Descrição|string|Não |
|Iniciar|string|Não |



### NewReource


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Nome|string|Sim |
|IsBudget|booleano|Não |
|IsGeneric|booleano|Não |
|IsInactive|booleano|Não |



### Project


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ApprovedStart|string|Não |
|ApprovedEnd|string|Não |
|CheckedOutDate|string|Não |
|CheckOutDescription|string|Não |
|CheckOutId|string|Não |
|CreatedDate|string|Não |
|ID|string|Não |
|IsCheckedOut|booleano|Não |
|LastPublishedDate|string|Não |
|LastSavedDate|string|Não |
|OptimizerDecision|inteiro|Não |
|PlannerDecision|inteiro|Não |
|ProjectType|inteiro|Não |
|Nome|string|Não |
|WinprojVersion|string|Não |



### ProjectsWrapper


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### NewTask


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|parameters|não definido|Sim |



### TaskParameters


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Nome|string|Sim |
|Observações|string|Não |
|Iniciar|string|Não |
|Duração|string|Não |



### EnterpriseResource


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|CanLevel|booleano|Não |
|Código|string|Não |
|CostAccrual|inteiro|Não |
|CostCenter|string|Não |
|Criado|string|Não |
|DefaultBookingType|inteiro|Não |
|Email|string|Não |
|ExternalId|string|Não |
|Agrupar|string|Não |
|HireDate|string|Não |
|ID|string|Não |
|Initials|string|Não |
|IsActive|booleano|Não |
|IsBudget|booleano|Não |
|IsCheckedOut|booleano|Não |
|IsGeneric|booleano|Não |
|IsTeam|booleano|Não |
|MaterialLabel|string|Não |
|Modificado|string|Não |
|Nome|string|Não |
|Phonetics|string|Não |
|ResourceType|inteiro|Não |
|TerminationDate|string|Não |



### TasksWrapper


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### Tarefa


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Criado|string|Não |
|Modificado|string|Não |
|Iniciar|string|Não |
|Concluir|string|Não |
|Nome|string|Não |
|ID|string|Não |
|Prioridade|inteiro|Não |
|PercentComplete|inteiro|Não |
|Observações|string|Não |
|Contato|string|Não |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->