<properties
pageTitle="Conector SQL | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Conector SQL fornece uma API para trabalhar com Bancos de Dados SQL."
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

# Introdução ao conector do Conector SQL



O conector do Conector SQL pode ser usado por meio de:

- [Aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Fluxos](http://flows.microsoft.com)  

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do Conector SQL pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do Conector SQL contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do Conector SQL
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[GetTables](connectors-create-api-sqlconnector.md#gettables)|Recupera as tabelas de um banco de dados SQL|
|[PostItem](connectors-create-api-sqlconnector.md#postitem)|Insere uma nova linha em uma tabela SQL|
|[GetItem](connectors-create-api-sqlconnector.md#getitem)|Recupera uma única linha de uma tabela SQL|
|[DeleteItem](connectors-create-api-sqlconnector.md#deleteitem)|Exclui uma linha de uma tabela SQL|
|[PatchItem](connectors-create-api-sqlconnector.md#patchitem)|Atualiza uma linha existente em uma tabela SQL|
### Gatilhos do Conector SQL
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|


## Criar uma conexão com o Conector SQL
Para criar Aplicativos Lógicos com o Conector SQL, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|SqlConnectionString|Sim|Fornecer a cadeia de conexão SQL|
Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência do Conector SQL
Aplica-se à versão: 1.0

## GetTables
Obter tabelas: recupera as tabelas de um banco de dados SQL

```GET: /datasets/default/tables```

Não há parâmetros para essa chamada
#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## PostItem
Inserir linha: insere uma nova linha em uma tabela SQL

```POST: /datasets/default/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|item| |sim|corpo|nenhum|Linha para inserir na tabela especificada no SQL|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## GetItem
Obter linha: recupera uma única linha de uma tabela SQL

```GET: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|ID|string|sim|path|nenhum|O identificador exclusivo da linha a ser recuperado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## DeleteItem
Excluir linha: exclui uma linha de uma tabela SQL

```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|ID|string|sim|path|nenhum|Identificador exclusivo da linha a ser excluída|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## PatchItem
Atualizar linha: atualiza uma linha existente em uma tabela SQL

```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|ID|string|sim|path|nenhum|Identificador exclusivo da linha a ser atualizada|
|item| |sim|corpo|nenhum|Linhas com valores atualizados|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## Definições de objeto 

### DataSetsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|tabular|não definido|Não |
|blob|não definido|Não |



### TabularDataSetsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|fonte|string|Não |
|displayName|string|Não |
|urlEncoding|string|Não |
|tableDisplayName|string|Não |
|tablePluralName|string|Não |



### BlobDataSetsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|fonte|string|Não |
|displayName|string|Não |
|urlEncoding|string|Não |



### TableMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|name|string|Não |
|título|string|Não |
|x-ms-permission|string|Não |
|x-ms-capabilities|não definido|Não |
|schema|não definido|Não |



### TableCapabilitiesMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|sortRestrictions|não definido|Não |
|filterRestrictions|não definido|Não |
|filterFunctions|array|Não |



### Objeto


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|



### TableSortRestrictionsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|sortable|booleano|Não |
|unsortableProperties|array|Não |
|ascendingOnlyProperties|array|Não |



### TableFilterRestrictionsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|filterable|booleano|Não |
|nonFilterableProperties|array|Não |
|requiredProperties|array|Não |



### DataSetsList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### DataSet


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Nome|string|Não |
|DisplayName|string|Não |



### Tabela


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Nome|string|Não |
|DisplayName|string|Não |



### Item


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ItemInternalId|string|Não |



### TablesList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### ItemsList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->