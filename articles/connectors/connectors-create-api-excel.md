<properties
pageTitle="Adicionar o conector do Excel ao PowerApps Enterprise | Microsoft Azure"
description="Visão geral do Conector do Excel com os parâmetros da API REST"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# Introdução ao conector do Excel

Conecte-se ao Excel para inserir uma linha, excluir uma linha e muito mais. O conector do Excel pode ser usado por meio de:

- PowerApps

Com o Excel, você pode:

- Adicionar o conector do Excel ao PowerApps Enterprise Assim, seus usuários poderão usar esse conector em seus próprios aplicativos. 

Para saber mais sobre como adicionar um conector ao PowerApps Enterprise, acesse [Registrar um conector no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

## Gatilhos e ações
O Excel inclui a ação a seguir. Não há nenhum gatilho.

|Gatilho|Ações|
|--- | ---|
|Nenhum | <ul><li>Obter linhas</li><li>Inserir linha</li><li>Excluir linha</li><li>Obter linha</li><li>Obter tabelas</li><li>Atualizar linha</li></ul>

Todos os conectores dão suporte a dados nos formatos JSON e XML.

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Insere uma nova linha em uma tabela do Excel
```POST: /datasets/{dataset}/tables/{table}/items```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome do arquivo do Excel|
|tabela|string|sim|path|nenhum|Nome da tabela do Excel|
|item| |sim|corpo|nenhum|Linha para inserir na tabela do Excel especificada|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|




### Recupera uma única linha de uma tabela do Excel
```GET: /datasets/{dataset}/tables/{table}/items/{id}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome do arquivo do Excel|
|tabela|string|sim|path|nenhum|Nome da tabela do Excel|
|ID|string|sim|path|nenhum|O identificador exclusivo da linha a ser recuperado|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|




### Exclui uma linha de uma tabela do Excel
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome do arquivo do Excel|
|tabela|string|sim|path|nenhum|Nome da tabela do Excel|
|ID|string|sim|path|nenhum|Identificador exclusivo da linha a ser excluída|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|




### Atualiza uma linha existente em uma tabela do Excel
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome do arquivo do Excel|
|tabela|string|sim|path|nenhum|Nome da tabela do Excel|
|ID|string|sim|path|nenhum|Identificador exclusivo da linha a ser atualizada|
|item| |sim|corpo|nenhum|Linhas com valores atualizados|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|




## Definições de objeto

#### DataSetsMetadata

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|tabular|não definido|não|
|blob|não definido|não|

#### TabularDataSetsMetadata

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|
|tableDisplayName|string|não|
|tablePluralName|string|não|

#### BlobDataSetsMetadata

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|

#### TableMetadata

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|name|string|não|
|título|string|não|
|x-ms-permission|string|não|
|schema|não definido|não|

#### DataSetsList

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|

#### DataSet

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|

#### Tabela

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|

#### Item

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|ItemInternalId|string|não|

#### TablesList

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|

#### ItemsList

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) [Criar um PowerApp](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0525_2016-->