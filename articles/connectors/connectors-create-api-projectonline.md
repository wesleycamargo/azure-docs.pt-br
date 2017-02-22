---
title: ProjectOnline | Microsoft Docs
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Project Online é uma solução online flexível da Microsoft para PPM (gerenciamento de portfólio de projetos) e trabalho cotidiano. Distribuído por meio do Office 365, o Project Online permite que as organizações se familiarizem rapidamente com funcionalidades avançadas de gerenciamento de projetos para planejar, priorizar e gerenciar projetos e investimentos em portfólio de projetos – praticamente, em qualquer lugar e em qualquer dispositivo."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 77e106d9170fc49d66d322bd9c92bf0e14869259


---
# <a name="get-started-with-the-projectonline-connector"></a>Introdução ao conector do ProjectOnline
O Project Online é uma solução online flexível da Microsoft para PPM (gerenciamento de portfólio de projetos) e trabalho cotidiano. Distribuído por meio do Office 365, o Project Online permite que as organizações se familiarizem rapidamente com funcionalidades avançadas de gerenciamento de projetos para planejar, priorizar e gerenciar projetos e investimentos em portfólio de projetos – praticamente, em qualquer lugar e em qualquer dispositivo.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. 
> 
> 

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do ProjectOnline pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML. 

 O conector do ProjectOnline contém as seguintes ações e/ou gatilhos disponíveis:

### <a name="projectonline-actions"></a>Ações do ProjectOnline
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [ListProjects](connectors-create-api-projectonline.md#listprojects) |Lista os projetos no site online do projeto |
| [CreateProject](connectors-create-api-projectonline.md#createproject) |Cria um novo projeto no site online do projeto |
| [CreateTask](connectors-create-api-projectonline.md#createtask) |Cria uma nova tarefa no projeto |
| [CreateResource](connectors-create-api-projectonline.md#createresource) |Cria um Recurso Empresarial no site online do projeto |
| [ListTasks](connectors-create-api-projectonline.md#listtasks) |Lista as tarefas publicadas em um projeto |
| [CheckoutProject](connectors-create-api-projectonline.md#checkoutproject) |Faz check-out de um projeto no site |
| [PublishProject](connectors-create-api-projectonline.md#publishproject) |Faz check-in e publica um projeto existente no site |

### <a name="projectonline-triggers"></a>Gatilhos do ProjectOnline
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| Quando um novo projeto é criado |Dispara um fluxo sempre que um novo projeto é criado |
| Quando um novo recurso é criado |Dispara um novo fluxo quando um novo recurso é criado |
| Quando uma nova tarefa é criada |Dispara um fluxo quando uma nova tarefa é criada |

## <a name="create-a-connection-to-projectonline"></a>Criar uma conexão com o ProjectOnline
Para criar Aplicativos lógicos com o ProjectOnline, primeiro você deve criar uma **conexão**, em seguida, forneça os detalhes para as seguintes propriedades: 

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer as credenciais do ProjectOnline |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## <a name="reference-for-projectonline"></a>Referência do ProjectOnline
Aplica-se à versão: 1.0

## <a name="onnewproject"></a>OnNewProject
Quando um novo projeto é criado: dispara um fluxo sempre que um novo projeto é criado 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Response
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="onnewresource"></a>OnNewResource
Quando um novo recurso é criado: dispara um novo fluxo quando um novo recurso é criado 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Response
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="onnewtask"></a>OnNewTask
Quando uma nova tarefa é criada: dispara um fluxo quando uma nova tarefa é criada 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Response
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="listprojects"></a>ListProjects
Listar projetos: lista os projetos no site online do projeto 

```GET: /_api/ProjectServer/Projects``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Response
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="createproject"></a>CreateProject
Criar novo projeto: cria um novo projeto no site online do projeto 

```POST: /_api/ProjectServer/Projects``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| proj | |sim |corpo |nenhum |Novo projeto a ser criado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |ForbIDden |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="createtask"></a>CreateTask
Criar nova tarefa: cria uma nova tarefa no projeto 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sim |path |nenhum |ID exclusiva do projeto ao qual a tarefa será adicionada |
| task | |sim |corpo |nenhum |Nova tarefa a ser adicionada ao projeto |

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

## <a name="createresource"></a>CreateResource
Criar novo recurso: Cria um Recurso Empresarial no site online do projeto 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| recurso | |sim |corpo |nenhum |Novo recurso empresarial a ser adicionado ao projeto |

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

## <a name="listtasks"></a>ListTasks
Listar tarefas: lista as tarefas publicadas em um projeto 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sim |path |nenhum |ID exclusiva do projeto para buscar tarefas |

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

## <a name="checkoutproject"></a>CheckoutProject
Fazer check-out de um projeto: faz check-out de um projeto no site 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sim |path |nenhum |ID exclusiva do projeto ao qual a tarefa será adicionada |

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

## <a name="publishproject"></a>PublishProject
Fazer check-in e publicar um projeto: faz check-in e publica um projeto existente no site 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |sim |query |nenhum |URL do site-raiz do site do projeto (Exemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sim |path |nenhum |ID exclusiva do projeto para fazer check-in |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="triggerproject"></a>TriggerProject
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ProjectStartDate |string |Não |
| ProjectFinishDate |string |Não |
| ProjectCreatedDate |string |Não |
| ProjectId |string |Não |
| ProjectModifiedDate |string |Não |
| ProjectType |inteiro |Não |
| ProjectName |string |Não |

### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="triggerresource"></a>TriggerResource
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ResourceId |string |Não |
| ResourceBaseCalendar |string |Não |
| ResourceBookingType |inteiro |Não |
| ResourceCanLevel |Booliano |Não |
| ResourceCostPerUse |número |Não |
| ResourceCreatedDate |string |Não |
| ResourceEarliestAvailableFrom |string |Não |
| ResourceEmail |string |Não |
| ResourceInitials |string |Não |
| ResourceIsActive |Booliano |Não |
| ResourceIsGeneric |Booliano |Não |
| ResourceLatestAvailableTo |string |Não |
| ResourceModifiedDate |string |Não |
| ResourceName |string |Não |
| ResourceStatsuName |string |Não |
| ResourceType |inteiro |Não |
| TypeDescription |string |Não |
| TypeName |string |Não |

### <a name="triggertaskswrapper"></a>TriggerTasksWrapper
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="triggertask"></a>TriggerTask
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ProjectId |string |Não |
| TaskId |string |Não |
| ProjectName |string |Não |
| TaskName |string |Não |
| TaskCreatedDate |string |Não |
| TaskModifieddate |string |Não |
| TaskStartDate |string |Não |
| TaskFinishDate |string |Não |
| TaskPriority |inteiro |Não |
| TaskIsActive |Booliano |Não |

### <a name="newproject"></a>NewProject
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |Sim |
| Descrição |string |Não |
| Iniciar |string |Não |

### <a name="newreource"></a>NewReource
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |Sim |
| IsBudget |Booliano |Não |
| IsGeneric |Booliano |Não |
| IsInactive |Booliano |Não |

### <a name="project"></a>Project
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ApprovedStart |string |Não |
| ApprovedEnd |string |Não |
| CheckedOutDate |string |Não |
| CheckOutDescription |string |Não |
| CheckOutId |string |Não |
| CreatedDate |string |Não |
| ID |string |Não |
| IsCheckedOut |Booliano |Não |
| LastPublishedDate |string |Não |
| LastSavedDate |string |Não |
| OptimizerDecision |inteiro |Não |
| PlannerDecision |inteiro |Não |
| ProjectType |inteiro |Não |
| Nome |string |Não |
| WinprojVersion |string |Não |

### <a name="projectswrapper"></a>ProjectsWrapper
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="newtask"></a>NewTask
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| parameters |não definido |Sim |

### <a name="taskparameters"></a>TaskParameters
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |Sim |
| Observações |string |Não |
| Iniciar |string |Não |
| Duração |string |Não |

### <a name="enterpriseresource"></a>EnterpriseResource
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| CanLevel |Booliano |Não |
| Código |string |Não |
| CostAccrual |inteiro |Não |
| CostCenter |string |Não |
| Criado |string |Não |
| DefaultBookingType |inteiro |Não |
| Email |string |Não |
| ExternalId |string |Não |
| Agrupar |string |Não |
| HireDate |string |Não |
| ID |string |Não |
| Initials |string |Não |
| IsActive |booleano |Não |
| IsBudget |Booliano |Não |
| IsCheckedOut |Booliano |Não |
| IsGeneric |Booliano |Não |
| IsTeam |Booliano |Não |
| MaterialLabel |string |Não |
| Modificado |string |Não |
| Nome |string |Não |
| Phonetics |string |Não |
| ResourceType |inteiro |Não |
| TerminationDate |string |Não |

### <a name="taskswrapper"></a>TasksWrapper
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="task"></a>Tarefa
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Criado |string |Não |
| Modificado |string |Não |
| Iniciar |string |Não |
| Concluir |string |Não |
| Nome |string |Não |
| ID |string |Não |
| Prioridade |inteiro |Não |
| PercentComplete |inteiro |Não |
| Observações |string |Não |
| Contato |string |Não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO3-->


