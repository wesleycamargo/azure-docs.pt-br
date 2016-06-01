<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Wunderlist fornece um gerenciador de tarefas e de lista de tarefas pendentes para ajudar as pessoas a realizar seu trabalho. Se você estiver compartilhando uma lista de compras com alguém da família, trabalhando em um projeto ou planejando suas férias, o Wunderlist facilitará a captura, o compartilhamento e a realização de suas listas de tarefas pendentes. O Wunderlist é sincronizado instantaneamente com seu telefone, tablet e computador, para que você possa acessar todas as tarefas de qualquer lugar."
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

# Introdução ao conector do Wunderlist



O conector do Wunderlist pode ser usado por meio de:

- [Aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Fluxo](http://flows.microsoft.com)  

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do Wunderlist pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do Wunderlist contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do Wunderlist
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Recupera as listas associadas à sua conta.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Cria uma lista.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Recupera tarefas de uma lista específica.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Cria uma tarefa|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Recupera as subtarefas de uma lista ou tarefa específica.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Cria uma subtarefa em uma tarefa específica|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Recupera as anotações de uma lista ou tarefa específica.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Adiciona uma anotação a uma tarefa específica|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Recupera os comentários de tarefa de uma lista ou tarefa específica.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Adiciona um comentário a uma tarefa específica|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Recupera os lembretes de uma lista ou tarefa específica.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Define um lembrete.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Recupera arquivos de uma lista ou tarefa específica.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Recupera uma lista específica|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Exclui uma lista|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Atualiza uma lista específica|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Recupera uma tarefa específica|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Atualiza uma tarefa específica|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Exclui uma tarefa específica|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Recupera uma subtarefa específica|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Atualiza uma subtarefa específica|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Exclui uma subtarefa específica|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Recupera uma anotação específica|
|[UpdateNote](connectors-create-api-wunderlist.md#updatenote)|Atualiza uma anotação específica|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Exclui uma anotação específica|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Recupera um comentário de tarefa específico|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Atualiza um lembrete específico|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Exclui um lembrete específico|
### Gatilhos do Wunderlist
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|Quando uma tarefa está pendente|Dispara um novo fluxo quando uma tarefa na lista está pendente|
|Quando uma nova tarefa é criada|Dispara um novo fluxo quando uma nova tarefa é criada na lista|
|Quando ocorre um lembrete|Dispara um novo fluxo quando ocorre um lembrete|


## Criar uma conexão com o Wunderlist
Para criar Aplicativos Lógicos com o Wunderlist, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer credenciais do Wunderlist|
Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.


>[AZURE.INCLUDE [Etapas para criar uma conexão com o Wunderlist](../../includes/connectors-create-api-wunderlist.md)]


>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência do Wunderlist
Aplica-se à versão: 1.0

## TriggerTaskDue
Quando uma tarefa está pendente: dispara um novo fluxo quando uma tarefa na lista está pendente

```GET: /trigger/tasksdue```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|


## TriggerTaskNew
Quando uma nova tarefa é criada: dispara um novo fluxo quando uma nova tarefa é criada na lista

```GET: /trigger/tasksnew```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|


## TriggerReminder
Quando ocorre um lembrete: dispara um novo fluxo quando ocorre um lembrete

```GET: /trigger/reminders```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|task\_id|inteiro|não|query|nenhum|ID da tarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|


## RetrieveLists
Obter listas: recupera as listas associadas à sua conta.

```GET: /lists```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## CreateList
Criar uma lista: cria uma lista.

```POST: /lists```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|post| |sim|corpo|nenhum|Nova lista a ser criada|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|padrão|Falha na Operação.|


## ListTasks
Obter tarefas: recupera tarefas de uma lista específica.

```GET: /tasks```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|concluído|booleano|não|query|nenhum|Concluído|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## CreateTask
Criar uma tarefa: cria uma tarefa

```POST: /tasks```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|post| |sim|corpo|nenhum|Nova tarefa a ser criada|

#### Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## ListSubTasks
Obter subtarefas: recupera subtarefas de uma lista ou tarefa específica.

```GET: /subtasks```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|task\_id|inteiro|não|query|nenhum|ID da tarefa|
|concluído|booleano|não|query|nenhum|Concluído|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## CreateSubTask
Criar uma subtarefa: cria uma subtarefa em uma tarefa específica

```POST: /subtasks```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|post| |sim|corpo|nenhum|Nova subtarefa a ser criada|

#### Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## ListNotes
Obter anotações: recupera as anotações de uma lista ou tarefa específica.

```GET: /notes```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|task\_id|inteiro|não|query|nenhum|ID da tarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## CreateNote
Criar uma nota: adiciona uma anotação a uma tarefa específica

```POST: /notes```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|post| |sim|corpo|nenhum|Nova anotação a ser criada|

#### Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## ListComments
Obter comentários de tarefa: recupera os comentários de tarefa de uma lista ou tarefa específica.

```GET: /task_comments```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|task\_id|inteiro|não|query|nenhum|ID da tarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## CreateComment
Adicionar um comentário a uma tarefa: adiciona um comentário a uma tarefa específica

```POST: /task_comments```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|post| |sim|corpo|nenhum|Novo comentário de tarefa a ser criado|

#### Resposta

|Nome|Descrição|
|---|---|
|201|Criado|


## RetrieveReminders
Obter lembretes: recupera os lembretes de uma lista ou tarefa específica.

```GET: /reminders```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|task\_id|inteiro|não|query|nenhum|ID da tarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## CreateReminder
Definir um lembrete: define um lembrete.

```POST: /reminders```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|post| |sim|corpo|nenhum|Novo lembrete a ser criado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|padrão|Falha na Operação.|


## RetrieveFiles
Obter arquivos: recupera os arquivos de uma lista ou tarefa específica.

```GET: /files```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|task\_id|inteiro|não|query|nenhum|ID da tarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## GetList
Obter lista: recupera uma lista específica

```GET: /lists/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|ID da lista|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## DeleteList
Excluir lista: exclui uma lista

```DELETE: /lists/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|inteiro|sim|path|nenhum|ID da lista|
|revision|inteiro|sim|query|nenhum|Revisão|

#### Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## UpdateList
Atualizar uma lista: atualiza uma lista específica

```PATCH: /lists/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|inteiro|sim|path|nenhum|ID da lista|
|post| |sim|corpo|nenhum|Lista detalhes|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## GetTask
Obter tarefa: recupera uma tarefa específica

```GET: /tasks/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|ID|inteiro|sim|path|nenhum|ID da tarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## UpdateTask
Atualizar uma tarefa: atualiza uma tarefa específica

```PATCH: /tasks/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|ID|inteiro|sim|path|nenhum|ID da tarefa|
|post| |sim|corpo|nenhum|Detalhes de tarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## DeleteTask
Excluir tarefa: exclui uma tarefa específica

```DELETE: /tasks/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|list\_id|inteiro|sim|query|nenhum|ID da lista|
|ID|inteiro|sim|path|nenhum|ID da tarefa|
|revision|inteiro|sim|query|nenhum|Revisão|

#### Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## GetSubTask
Obter subtarefa: recupera uma subtarefa específica

```GET: /subtasks/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|ID da subtarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## UpdateSubTask
Atualizar uma subtarefa: atualiza uma subtarefa específica

```PATCH: /subtasks/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|inteiro|sim|path|nenhum|ID da subtarefa|
|post| |sim|corpo|nenhum|Detalhes da subtarefa|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## DeleteSubTask
Excluir uma subtarefa: exclui uma subtarefa específica

```DELETE: /subtasks/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|inteiro|sim|path|nenhum|ID da subtarefa|
|revision|inteiro|sim|query|nenhum|Revisão|

#### Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## GetNote
Obter uma anotação: recupera uma anotação específica

```GET: /notes/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|ID da anotação|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## UpdateNote
Atualizar uma anotação: atualiza uma anotação específica

```PATCH: /notes/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|inteiro|sim|path|nenhum|ID da anotação|
|post| |sim|corpo|nenhum|Detalhes da anotação|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## DeleteNote
Excluir uma anotação: exclui uma anotação específica

```DELETE: /notes/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|inteiro|sim|path|nenhum|ID da anotação|
|revision|inteiro|sim|query|nenhum|Revisão|

#### Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## GetComment
Obter comentários de tarefa: recupera um comentário de tarefa específico

```GET: /task_comments/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|ID de Comentário|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## UpdateReminder
Atualizar um lembrete: atualiza um lembrete específico

```PATCH: /reminders/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|inteiro|sim|path|nenhum|ID de lembrete|
|post| |sim|corpo|nenhum|Detalhes de lembrete|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|


## DeleteReminder
Excluir um lembrete: exclui um lembrete específico

```DELETE: /reminders/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|inteiro|sim|path|nenhum|ID do lembrete.|
|revision|inteiro|sim|query|nenhum|Revisão|

#### Resposta

|Nome|Descrição|
|---|---|
|204|Sem conteúdo|


## Definições de objeto 

### Listar


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|created\_at|string|Não |
|título|string|Não |
|list\_type|string|Não |
|type|string|Não |
|revision|inteiro|Não |



### CreatedList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|created\_at|string|Não |
|título|string|Não |
|revision|inteiro|Não |
|type|string|Não |



### Tarefa


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|assignee\_id|inteiro|Não |
|assigner\_id|inteiro|Não |
|created\_at|string|Não |
|created\_by\_id|inteiro|Não |
|due\_date|string|Não |
|list\_id|inteiro|Não |
|revision|inteiro|Não |
|starred|booleano|Não |
|título|string|Não |



### Subtask


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|task\_id|inteiro|Não |
|created\_at|string|Não |
|created\_by\_id|inteiro|Não |
|revision|string|Não |
|título|string|Não |



### Observação


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|task\_id|inteiro|Não |
|conteúdo|string|Não |
|created\_at|string|Não |
|updated\_at|string|Não |
|revision|inteiro|Não |



### Comentário


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|task\_id|inteiro|Não |
|revision|inteiro|Não |
|texto|string|Não |
|type|string|Não |
|created\_at|string|Não |



### Reminder


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|data|string|Não |
|task\_id|inteiro|Não |
|revision|inteiro|Não |
|type|string|Não |
|created\_at|string|Não |
|updated\_at|string|Não |



### CreatedReminder


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|data|string|Não |
|task\_id|inteiro|Não |
|revision|inteiro|Não |
|created\_at|string|Não |
|updated\_at|string|Não |



### Arquivo


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|url|string|Não |
|task\_id|inteiro|Não |
|list\_id|inteiro|Não |
|user\_id|inteiro|Não |
|file\_name|string|Não |
|content\_type|string|Não |
|file\_size|inteiro|Não |
|local\_created\_at|string|Não |
|created\_at|string|Não |
|updated\_at|string|Não |
|type|string|Não |
|revision|inteiro|Não |



### NewTask


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|list\_id|inteiro|Sim |
|título|string|Sim |
|assignee\_id|inteiro|Não |
|concluído|booleano|Não |
|recurrence\_type|string|Não |
|recurrence\_count|inteiro|Não |
|due\_date|string|Não |
|starred|booleano|Não |



### NewList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|título|string|Sim |



### NewSubtask


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|list\_id|inteiro|Sim |
|task\_id|inteiro|Sim |
|título|string|Sim |
|concluído|booleano|Não |



### NewNote


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|list\_id|inteiro|Sim |
|task\_id|inteiro|Sim |
|conteúdo|string|Sim |



### NewComment


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|list\_id|inteiro|Sim |
|task\_id|inteiro|Sim |
|texto|string|Sim |



### NewReminder


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|list\_id|inteiro|Sim |
|task\_id|inteiro|Sim |
|data|string|Sim |



### UpdateTask


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|revision|inteiro|Não |
|título|string|Não |
|assignee\_id|inteiro|Não |
|concluído|booleano|Não |
|recurrence\_type|string|Não |
|recurrence\_count|inteiro|Não |
|due\_date|string|Não |
|starred|booleano|Não |



### UpdateList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|revision|inteiro|Não |
|título|string|Não |



### UpdateSubtask


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|revision|inteiro|Não |
|título|string|Não |
|concluído|booleano|Não |



### UpdateNote


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|revision|inteiro|Não |
|conteúdo|string|Não |



### UpdateReminder


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|data|string|Não |
|revision|inteiro|Não |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->