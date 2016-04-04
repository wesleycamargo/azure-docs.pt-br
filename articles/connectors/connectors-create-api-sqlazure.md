<properties
	pageTitle="Adicionar a API do SQL Azure os seus Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da API do SQL Azure com os parâmetros da API REST"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>


# Introdução à API do SQL Azure
Conecte-se ao SQL Azure para gerenciar suas tabelas e linhas, como ao inserir linhas, obter tabelas e muito mais.

A API do SQL Azure pode ser usada em:

- Aplicativos lógicos 

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para obter a versão do esquema 2014-12-01-preview, clique em [Conector do SQL](../app-service-logic/app-service-logic-connector-sql.md).

Com o SQL Azure, é possível:

- Compile seu fluxo de negócios baseado nos dados obtidos do SQL Azure. 
- Use as ações para obter uma linha, insira uma linha e muito mais. Essas ações obtém uma resposta e disponibilizam a saída para outras ações. Por exemplo, você pode obter uma linha de dados do SQL Azure e, em seguida, adicionar esses dados ao Excel. 

Para adicionar uma operação nos aplicativos lógicos, veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Gatilhos e ações
O SQL inclui as seguintes ações. Não há nenhum gatilho.

Gatilhos | Ações
--- | ---
Nenhum | <ul><li>Obter linha</li><li>Obter linhas</li><li>Inserir linha</li><li>Excluir linha</li><li>Obter tabelas</li><li>Atualizar linha</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar a conexão com o SQL
Ao adicionar essa API aos seus aplicativos lógicos, insira os seguintes valores:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Cadeia de Conexão SQL|Sim|Insira a cadeia de conexão do SQL Azure|

Depois de criar a conexão, insira as propriedades do SQL, como o nome da tabela. A **referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Obter linha 
Recupera uma única linha de uma tabela SQL.```GET: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|ID|string|sim|path|nenhum|O identificador exclusivo da linha a ser recuperado|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter linhas 
Recupera linhas de uma tabela SQL.```GET: /datasets/default/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Inserir linha 
Insere uma nova linha em uma tabela SQL.```POST: /datasets/default/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|item|ItemInternalId: cadeia de caracteres|sim|corpo|nenhum|Linha para inserir na tabela especificada no SQL|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Excluir linha 
Exclui uma linha de uma tabela SQL.```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|ID|string|sim|path|nenhum|Identificador exclusivo da linha a ser excluída|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter tabelas 
Recupera as tabelas de um banco de dados SQL.```GET: /datasets/default/tables```

Não existem parâmetros para esta chamada.

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Atualizar linha 
Atualiza uma linha existente em uma tabela SQL.```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Nome da tabela SQL|
|ID|string|sim|path|nenhum|Identificador exclusivo da linha a ser atualizada|
|item|ItemInternalId: cadeia de caracteres|sim|corpo|nenhum|Linhas com valores atualizados|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

## Definições de objeto

#### DataSetsMetadata

|Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|tabular|não definido|não|
|blob|não definido|não|

#### TabularDataSetsMetadata

|Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|
|tableDisplayName|string|não|
|tablePluralName|string|não|

#### BlobDataSetsMetadata

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|

#### TableMetadata

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|name|string|não|
|título|string|não|
|x-ms-permission|string|não|
|schema|não definido|não|

#### DataSetsList

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|value|array|não|

#### DataSet

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|

#### Tabela

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|

#### Item

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|ItemInternalId|string|não|

#### ItemsList

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|value|array|não|

#### TablesList

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|value|array|não|


## Próximas etapas

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0323_2016-->