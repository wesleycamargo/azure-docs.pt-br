---
title: Aprenda a usar o Conector do Salesforce em seus aplicativos lógicos | Microsoft Docs
description: Crie aplicativos lógicos com o serviço de Aplicativo do Azure. O Conector do Salesforce fornece uma API para trabalhar com objetos do Salesforce.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: deonhe

---
# <a name="get-started-with-the-salesforce-connector"></a>Introdução ao conector do Salesforce
O Conector do Salesforce fornece uma API para trabalhar com objetos do Salesforce.

Para usar [qualquer conector](apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-salesforce-connector"></a>Ligar-se ao conector do Salesforce
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](connectors-overview.md) fornece uma conectividade entre um aplicativo lógico e outro serviço.  

### <a name="create-a-connection-to-salesforce-connector"></a>Criar uma conexão com o conector do Salesforce
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Usar um gatilho do conector do Salesforce
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Adicionar uma condição
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Usar uma ação do conector do Salesforce
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="technical-details"></a>Detalhes técnicos
Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## <a name="salesforce-connector-triggers"></a>Gatilhos do conector do Salesforce
O Conector do Salesforce tem os seguintes gatilhos:  

| Gatilho | Descrição |
| --- | --- |
| [Quando um objeto é criado](connectors-create-api-salesforce.md#when-an-object-is-created) |Esta operação dispara um fluxo quando um objeto é criado. |
| [Quando um objeto é modificado](connectors-create-api-salesforce.md#when-an-object-is-modified) |Esta operação dispara um fluxo quando um objeto é modificado. |

## <a name="salesforce-connector-actions"></a>Ações do conector do Salesforce
O Conector do Salesforce tem as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [Obter objetos](connectors-create-api-salesforce.md#get-objects) |Esta operação obtém objetos de um determinado tipo, como 'Lead'. |
| [Criar objeto](connectors-create-api-salesforce.md#create-object) |Esta operação cria um objeto. |
| [Obter objeto](connectors-create-api-salesforce.md#get-object) |Esta operação obtém um objeto. |
| [Excluir objeto](connectors-create-api-salesforce.md#delete-object) |Esta operação exclui um objeto. |
| [Atualizar objeto](connectors-create-api-salesforce.md#update-object) |Esta operação atualiza um objeto. |
| [Obter tipos de objeto](connectors-create-api-salesforce.md#get-object-types) |Esta operação lista os tipos de objeto disponíveis. |

### <a name="action-details"></a>Detalhes da ação
Veja abaixo os detalhes das ações e dos gatilhos para esse conector, com suas respostas:

### <a name="get-objects"></a>Obter objetos
Esta operação obtém objetos de um determinado tipo, como 'Lead'. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto do Salesforce, como 'Lead' |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a serem recuperadas (padrão = 256) |

Um * indica que uma propriedade é obrigatória

#### <a name="output-details"></a>Detalhes de saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

### <a name="create-object"></a>Criar objeto
Esta operação cria um objeto. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Lead' |
| item* |Objeto |Objeto a ser criado |

Um * indica que uma propriedade é obrigatória

#### <a name="output-details"></a>Detalhes de saída
Item

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ItemInternalId |string |

### <a name="get-object"></a>Obter objeto
Esta operação obtém um objeto. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto do Salesforce, como 'Lead' |
| id* |ID do objeto |Identificador do objeto a ser obtido |

Um * indica que uma propriedade é obrigatória

#### <a name="output-details"></a>Detalhes de saída
Item

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-object"></a>Excluir objeto
Esta operação exclui um objeto. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Lead' |
| id* |ID do objeto |Identificador do objeto a ser excluído |

Um * indica que uma propriedade é obrigatória

### <a name="update-object"></a>Atualizar objeto
Esta operação atualiza um objeto. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Lead' |
| id* |ID do objeto |Identificador do objeto a ser atualizado |
| item* |Objeto |Objeto com propriedades alteradas |

Um * indica que uma propriedade é obrigatória

#### <a name="output-details"></a>Detalhes de saída
Item

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ItemInternalId |string |

### <a name="when-an-object-is-created"></a>Quando um objeto é criado
Esta operação dispara um fluxo quando um objeto é criado. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Lead' |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a serem recuperadas (padrão = 256) |

Um * indica que uma propriedade é obrigatória

#### <a name="output-details"></a>Detalhes de saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

### <a name="when-an-object-is-modified"></a>Quando um objeto é modificado
Esta operação dispara um fluxo quando um objeto é modificado. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Lead' |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a serem recuperadas (padrão = 256) |

Um * indica que uma propriedade é obrigatória

#### <a name="output-details"></a>Detalhes de saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

### <a name="get-object-types"></a>Obter tipos de objeto
Esta operação lista os tipos de objeto disponíveis. 

Não há parâmetros para essa chamada

#### <a name="output-details"></a>Detalhes de saída
TablesList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Respostas HTTP
As ações e os gatilhos acima podem retornar um ou mais dos seguintes códigos de status HTTP: 

| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido. |
| padrão |Falha na Operação. |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!--HONumber=Oct16_HO2-->


