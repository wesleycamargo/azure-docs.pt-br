---
title: Wunderlist | Microsoft Docs
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Wunderlist fornece um gerenciador de tarefas e de lista de tarefas pendentes para ajudar as pessoas a realizar seu trabalho.  Se você estiver compartilhando uma lista de compras com alguém da família, trabalhando em um projeto ou planejando suas férias, o Wunderlist facilitará a captura, o compartilhamento e a realização de suas listas de tarefas pendentes. O Wunderlist é sincronizado instantaneamente com seu telefone, tablet e computador, para que você possa acessar todas as tarefas de qualquer lugar."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 084ac505003e926e9c4b75352ec96a77f8c3584a


---
# <a name="get-started-with-the-wunderlist-connector"></a>Introdução ao conector do Wunderlist
O Wunderlist fornece um gerenciador de tarefas e de lista de tarefas pendentes para ajudar as pessoas a realizar seu trabalho.  Se você estiver compartilhando uma lista de compras com alguém da família, trabalhando em um projeto ou planejando suas férias, o Wunderlist facilitará a captura, o compartilhamento e a realização de suas listas de tarefas pendentes. O Wunderlist é sincronizado instantaneamente com seu telefone, tablet e computador, para que você possa acessar todas as tarefas de qualquer lugar.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do Wunderlist pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do Wunderlist contém as seguintes ações e/ou gatilhos disponíveis:

### <a name="wunderlist-actions"></a>Ações do Wunderlist
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [RetrieveLists](connectors-create-api-wunderlist.md#retrievelists) |Recupera as listas associadas à sua conta. |
| [CreateList](connectors-create-api-wunderlist.md#createlist) |Cria uma lista. |
| [ListTasks](connectors-create-api-wunderlist.md#listtasks) |Recupera tarefas de uma lista específica. |
| [CreateTask](connectors-create-api-wunderlist.md#createtask) |Cria uma tarefa |
| [ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks) |Recupera as subtarefas de uma lista ou tarefa específica. |
| [CreateSubTask](connectors-create-api-wunderlist.md#createsubtask) |Cria uma subtarefa em uma tarefa específica |
| [ListNotes](connectors-create-api-wunderlist.md#listnotes) |Recupera as anotações de uma lista ou tarefa específica. |
| [CreateNote](connectors-create-api-wunderlist.md#createnote) |Adiciona uma anotação a uma tarefa específica |
| [ListComments](connectors-create-api-wunderlist.md#listcomments) |Recupera os comentários de tarefa de uma lista ou tarefa específica. |
| [CreateComment](connectors-create-api-wunderlist.md#createcomment) |Adiciona um comentário a uma tarefa específica |
| [RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders) |Recupera os lembretes de uma lista ou tarefa específica. |
| [CreateReminder](connectors-create-api-wunderlist.md#createreminder) |Define um lembrete. |
| [RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles) |Recupera arquivos de uma lista ou tarefa específica. |
| [GetList](connectors-create-api-wunderlist.md#getlist) |Recupera uma lista específica |
| [DeleteList](connectors-create-api-wunderlist.md#deletelist) |Exclui uma lista |
| [UpdateList](connectors-create-api-wunderlist.md#updatelist) |Atualiza uma lista específica |
| [GetTask](connectors-create-api-wunderlist.md#gettask) |Recupera uma tarefa específica |
| [UpdateTask](connectors-create-api-wunderlist.md#updatetask) |Atualiza uma tarefa específica |
| [DeleteTask](connectors-create-api-wunderlist.md#deletetask) |Exclui uma tarefa específica |
| [GetSubTask](connectors-create-api-wunderlist.md#getsubtask) |Recupera uma subtarefa específica |
| [UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask) |Atualiza uma subtarefa específica |
| [DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask) |Exclui uma subtarefa específica |
| [GetNote](connectors-create-api-wunderlist.md#getnote) |Recupera uma anotação específica |
| [UpdateNote](connectors-create-api-wunderlist.md#updatenote) |Atualiza uma anotação específica |
| [DeleteNote](connectors-create-api-wunderlist.md#deletenote) |Exclui uma anotação específica |
| [GetComment](connectors-create-api-wunderlist.md#getcomment) |Recupera um comentário de tarefa específico |
| [UpdateReminder](connectors-create-api-wunderlist.md#updatereminder) |Atualiza um lembrete específico |
| [DeleteReminder](connectors-create-api-wunderlist.md#deletereminder) |Exclui um lembrete específico |

### <a name="wunderlist-triggers"></a>Gatilhos do Wunderlist
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| Quando uma tarefa está pendente |Dispara um novo fluxo quando uma tarefa na lista está pendente |
| Quando uma nova tarefa é criada |Dispara um novo fluxo quando uma nova tarefa é criada na lista |
| Quando ocorre um lembrete |Dispara um novo fluxo quando ocorre um lembrete |

## <a name="create-a-connection-to-wunderlist"></a>Criar uma conexão com o Wunderlist
Para criar Aplicativos lógicos com o Wunderlist, primeiro você deve criar uma **conexão**, em seguida, forneça os detalhes para as seguintes propriedades:

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer credenciais do Wunderlist |

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## <a name="reference-for-wunderlist"></a>Referência do Wunderlist
Aplica-se à versão: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
Quando uma tarefa está pendente: dispara um novo fluxo quando uma tarefa na lista está pendente

```GET: /trigger/tasksdue```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |

## <a name="triggertasknew"></a>TriggerTaskNew
Quando uma nova tarefa é criada: dispara um novo fluxo quando uma nova tarefa é criada na lista

```GET: /trigger/tasksnew```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |

## <a name="triggerreminder"></a>TriggerReminder
Quando ocorre um lembrete: dispara um novo fluxo quando ocorre um lembrete

```GET: /trigger/reminders```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| task_id |inteiro |não |query |nenhum |ID da tarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |

## <a name="retrievelists"></a>RetrieveLists
Obter listas: recupera as listas associadas à sua conta.

```GET: /lists```

#### <a name="response"></a>Response
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="createlist"></a>CreateList
Criar uma lista: cria uma lista.

```POST: /lists```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| post | |sim |corpo |nenhum |Nova lista a ser criada |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| padrão |Falha na Operação. |

## <a name="listtasks"></a>ListTasks
Obter tarefas: recupera tarefas de uma lista específica.

```GET: /tasks```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| concluído |Booliano |não |query |nenhum |Concluído |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="createtask"></a>CreateTask
Criar uma tarefa: cria uma tarefa

```POST: /tasks```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| post | |sim |corpo |nenhum |Nova tarefa a ser criada |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 201 |Criado |

## <a name="listsubtasks"></a>ListSubTasks
Obter subtarefas: recupera subtarefas de uma lista ou tarefa específica.

```GET: /subtasks```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| task_id |inteiro |não |query |nenhum |ID da tarefa |
| concluído |Booliano |não |query |nenhum |Concluído |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="createsubtask"></a>CreateSubTask
Criar uma subtarefa: cria uma subtarefa em uma tarefa específica

```POST: /subtasks```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| post | |sim |corpo |nenhum |Nova subtarefa a ser criada |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 201 |Criado |

## <a name="listnotes"></a>ListNotes
Obter anotações: recupera as anotações de uma lista ou tarefa específica.

```GET: /notes```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| task_id |inteiro |não |query |nenhum |ID da tarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="createnote"></a>CreateNote
Criar uma nota: adiciona uma anotação a uma tarefa específica

```POST: /notes```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| post | |sim |corpo |nenhum |Nova anotação a ser criada |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 201 |Criado |

## <a name="listcomments"></a>ListComments
Obter comentários de tarefa: recupera os comentários de tarefa de uma lista ou tarefa específica.

```GET: /task_comments```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| task_id |inteiro |não |query |nenhum |ID da tarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="createcomment"></a>CreateComment
Adicionar um comentário a uma tarefa: adiciona um comentário a uma tarefa específica

```POST: /task_comments```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| post | |sim |corpo |nenhum |Novo comentário de tarefa a ser criado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 201 |Criado |

## <a name="retrievereminders"></a>RetrieveReminders
Obter lembretes: recupera os lembretes de uma lista ou tarefa específica.

```GET: /reminders```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| task_id |inteiro |não |query |nenhum |ID da tarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="createreminder"></a>CreateReminder
Definir um lembrete: define um lembrete.

```POST: /reminders```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| post | |sim |corpo |nenhum |Novo lembrete a ser criado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| padrão |Falha na Operação. |

## <a name="retrievefiles"></a>RetrieveFiles
Obter arquivos: recupera os arquivos de uma lista ou tarefa específica.

```GET: /files```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| task_id |inteiro |não |query |nenhum |ID da tarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="getlist"></a>GetList
Obter lista: recupera uma lista específica

```GET: /lists/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |nenhum |ID da lista |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="deletelist"></a>DeleteList
Excluir lista: exclui uma lista

```DELETE: /lists/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |inteiro |Sim |path |nenhum |ID da lista |
| revision |inteiro |sim |query |nenhum |Revisão |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 204 |Sem conteúdo |

## <a name="updatelist"></a>UpdateList
Atualizar uma lista: atualiza uma lista específica

```PATCH: /lists/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |inteiro |Sim |path |nenhum |ID da lista |
| post | |sim |corpo |nenhum |Lista detalhes |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="gettask"></a>GetTask
Obter tarefa: recupera uma tarefa específica

```GET: /tasks/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| ID |inteiro |Sim |path |nenhum |ID da tarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="updatetask"></a>UpdateTask
Atualizar uma tarefa: atualiza uma tarefa específica

```PATCH: /tasks/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| ID |inteiro |Sim |path |nenhum |ID da tarefa |
| post | |sim |corpo |nenhum |Detalhes de tarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="deletetask"></a>DeleteTask
Excluir tarefa: exclui uma tarefa específica

```DELETE: /tasks/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| list_id |inteiro |sim |query |nenhum |ID da lista |
| ID |inteiro |Sim |path |nenhum |ID da tarefa |
| revision |inteiro |sim |query |nenhum |Revisão |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 204 |Sem conteúdo |

## <a name="getsubtask"></a>GetSubTask
Obter subtarefa: recupera uma subtarefa específica

```GET: /subtasks/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |nenhum |ID da subtarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="updatesubtask"></a>UpdateSubTask
Atualizar uma subtarefa: atualiza uma subtarefa específica

```PATCH: /subtasks/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |inteiro |Sim |path |nenhum |ID da subtarefa |
| post | |sim |corpo |nenhum |Detalhes da subtarefa |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="deletesubtask"></a>DeleteSubTask
Excluir uma subtarefa: exclui uma subtarefa específica

```DELETE: /subtasks/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |inteiro |Sim |path |nenhum |ID da subtarefa |
| revision |inteiro |sim |query |nenhum |Revisão |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 204 |Sem conteúdo |

## <a name="getnote"></a>GetNote
Obter uma anotação: recupera uma anotação específica

```GET: /notes/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |nenhum |ID da anotação |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="updatenote"></a>UpdateNote
Atualizar uma anotação: atualiza uma anotação específica

```PATCH: /notes/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |inteiro |Sim |path |nenhum |ID da anotação |
| post | |sim |corpo |nenhum |Detalhes da anotação |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="deletenote"></a>DeleteNote
Excluir uma anotação: exclui uma anotação específica

```DELETE: /notes/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |inteiro |Sim |path |nenhum |ID da anotação |
| revision |inteiro |sim |query |nenhum |Revisão |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 204 |Sem conteúdo |

## <a name="getcomment"></a>GetComment
Obter comentários de tarefa: recupera um comentário de tarefa específico

```GET: /task_comments/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |nenhum |ID de Comentário |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="updatereminder"></a>UpdateReminder
Atualizar um lembrete: atualiza um lembrete específico

```PATCH: /reminders/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |inteiro |Sim |path |nenhum |ID de lembrete |
| post | |sim |corpo |nenhum |Detalhes de lembrete |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |

## <a name="deletereminder"></a>DeleteReminder
Excluir um lembrete: exclui um lembrete específico

```DELETE: /reminders/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |inteiro |Sim |path |nenhum |ID do lembrete. |
| revision |inteiro |sim |query |nenhum |Revisão |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 204 |Sem conteúdo |

## <a name="object-definitions"></a>Definições de objeto
### <a name="list"></a>Listar
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| created_at |string |Não |
| título |string |Não |
| list_type |string |Não |
| type |string |Não |
| revision |inteiro |Não |

### <a name="createdlist"></a>CreatedList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| created_at |string |Não |
| título |string |Não |
| revision |inteiro |Não |
| type |string |Não |

### <a name="task"></a>Tarefa
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| assignee_id |inteiro |Não |
| assigner_id |inteiro |Não |
| created_at |string |Não |
| created_by_id |inteiro |Não |
| due_date |string |Não |
| list_id |inteiro |Não |
| revision |inteiro |Não |
| starred |Booliano |Não |
| título |string |Não |

### <a name="subtask"></a>Subtask
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| task_id |inteiro |Não |
| created_at |string |Não |
| created_by_id |inteiro |Não |
| revision |string |Não |
| título |string |Não |

### <a name="note"></a>Observação
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| task_id |inteiro |Não |
| conteúdo |string |Não |
| created_at |string |Não |
| updated_at |string |Não |
| revision |inteiro |Não |

### <a name="comment"></a>Comentário
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| task_id |inteiro |Não |
| revision |inteiro |Não |
| texto |string |Não |
| type |string |Não |
| created_at |string |Não |

### <a name="reminder"></a>Reminder
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| data |string |Não |
| task_id |inteiro |Não |
| revision |inteiro |Não |
| type |string |Não |
| created_at |string |Não |
| updated_at |string |Não |

### <a name="createdreminder"></a>CreatedReminder
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| data |string |Não |
| task_id |inteiro |Não |
| revision |inteiro |Não |
| created_at |string |Não |
| updated_at |string |Não |

### <a name="file"></a>Arquivo
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| url |string |Não |
| task_id |inteiro |Não |
| list_id |inteiro |Não |
| user_id |inteiro |Não |
| file_name |string |Não |
| content_type |string |Não |
| file_size |inteiro |Não |
| local_created_at |string |Não |
| created_at |string |Não |
| updated_at |string |Não |
| type |string |Não |
| revision |inteiro |Não |

### <a name="newtask"></a>NewTask
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| list_id |inteiro |Sim |
| título |string |Sim |
| assignee_id |inteiro |Não |
| concluído |Booliano |Não |
| recurrence_type |string |Não |
| recurrence_count |inteiro |Não |
| due_date |string |Não |
| starred |Booliano |Não |

### <a name="newlist"></a>NewList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| título |string |Sim |

### <a name="newsubtask"></a>NewSubtask
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| list_id |inteiro |Sim |
| task_id |inteiro |Sim |
| título |string |Sim |
| concluído |Booliano |Não |

### <a name="newnote"></a>NewNote
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| list_id |inteiro |Sim |
| task_id |inteiro |Sim |
| conteúdo |string |Sim |

### <a name="newcomment"></a>NewComment
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| list_id |inteiro |Sim |
| task_id |inteiro |Sim |
| texto |string |Sim |

### <a name="newreminder"></a>NewReminder
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| list_id |inteiro |Sim |
| task_id |inteiro |Sim |
| data |string |Sim |

### <a name="updatetask"></a>UpdateTask
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| revision |inteiro |Não |
| título |string |Não |
| assignee_id |inteiro |Não |
| concluído |Booliano |Não |
| recurrence_type |string |Não |
| recurrence_count |inteiro |Não |
| due_date |string |Não |
| starred |Booliano |Não |

### <a name="updatelist"></a>UpdateList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| revision |inteiro |Não |
| título |string |Não |

### <a name="updatesubtask"></a>UpdateSubtask
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| revision |inteiro |Não |
| título |string |Não |
| concluído |Booliano |Não |

### <a name="updatenote"></a>UpdateNote
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| revision |inteiro |Não |
| conteúdo |string |Não |

### <a name="updatereminder"></a>UpdateReminder
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| data |string |Não |
| revision |inteiro |Não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


