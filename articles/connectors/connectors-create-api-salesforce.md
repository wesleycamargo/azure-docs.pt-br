<properties
pageTitle="Adicionar a API do Salesforce ao PowerApps Enterprise e a seus Aplicativos Lógicos | Microsoft Azure"
description="Visão geral da API do Salesforce com os parâmetros da API REST"
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
ms.date="03/16/2016"
ms.author="deonhe"/>

# Introdução à API do Salesforce
Conecte-se ao Salesforce e crie objetos, obtenha objetos muito mais. A API do Salesforce pode ser usada em:

- Aplicativos lógicos 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

&nbsp;

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

Com o Salesforce, você pode:

- Crie seu fluxo de negócios baseado nos dados obtidos do SalesForce. 
- Usar gatilhos para quando um objeto for criado ou atualizado.
- Use ações para criar um objeto, excluí-lo e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um novo objeto é criado no Salesforce, você pode enviar um email usando o Office 365.
- Adicione a API do Salesforce ao PowerApps Enterprise. Assim, seus usuários poderão usar essa API em seus próprios aplicativos. 

Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação aos aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
A API do Salesforce inclui o gatilho e ações a seguir.

| Gatilhos | Ações|
| --- | --- |
|<ul><li>Quando um objeto é criado</li><li>Quando um objeto é modificado</li></ul> | <ul><li>Criar objeto</li><li>Obter objetos</li><li>Quando um objeto é criado</li><li>Quando um objeto é modificado</li><li>Excluir objeto</li><li>Obter objeto</li><li>Obter tipos de objeto (SObjects)</li><li>Atualizar objeto</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o Salesforce 

Quando você adiciona essa API aos seus aplicativos lógicos, precisa autorizar que os aplicativos lógicos se conectem ao Salesforce.

1. Entre em sua conta do Salesforce.
2. Permitir que seus aplicativos lógicos se conectem e usem sua conta do Salesforce. 

Depois de criar a conexão, insira as propriedades do Salesforce, como o nome da tabela. A **Referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão em outros aplicativos lógicos.

## Referência da API REST do Swaggers
Aplica-se à versão: 1.0.


### Criar objeto
Cria um objeto do Salesforce.```POST: /datasets/default/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Tipo de SObject do Salesforce (exemplo: 'Lead')|
|item| |sim|corpo|nenhum|Objeto do Salesforce a ser criado|

### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Obter objeto
Recupera um objeto do Salesforce.```GET: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Tipo de SObject do Salesforce (exemplo: 'Lead')|
|ID|string|sim|path|nenhum|Identificador exclusivo do objeto do Salesforce a ser recuperado|

### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Excluir objeto
Exclui um objeto do Salesforce.```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Tipo de SObject do Salesforce (exemplo: 'Lead')|
|ID|string|sim|path|nenhum|Identificador exclusivo do objeto do Salesforce a excluir|

### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Atualizar objeto
Atualiza um objeto do Salesforce.```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Tipo de SObject do Salesforce (exemplo: 'Lead')|
|ID|string|sim|path|nenhum|Identificador exclusivo do objeto do Salesforce a ser atualizado|
|item| |sim|corpo|nenhum|Objeto do Salesforce com propriedades alteradas|

### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Quando um objeto é criado
Dispara um fluxo quando um objeto é criado no Salesforce.```GET: /datasets/default/tables/{table}/onnewitems```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Tipo de SObject do Salesforce (exemplo: 'Lead')|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|

### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Quando um objeto é modificado 
Dispara um fluxo quando um objeto é modificado no Salesforce.```GET: /datasets/default/tables/{table}/onupdateditems```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Tipo de SObject do Salesforce (exemplo: 'Lead')|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|

### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|



## Definições de objeto 

#### DataSetsMetadata

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|tabular|não definido|não|
|blob|não definido|não|


#### TabularDataSetsMetadata

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|
|tableDisplayName|string|não|
|tablePluralName|string|não|


#### BlobDataSetsMetadata

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|


#### TableMetadata

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|name|string|não|
|título|string|não|
|x-ms-permission|string|não|
|schema|não definido|não|


#### DataSetsList

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|value|array|não|


#### DataSet

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|Nome|string|
|DisplayName|string|não|


#### Tabela

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|


#### Item

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|ItemInternalId|string|não|


#### ItemsList

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|value|array|não|


#### TablesList

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|value|array|não|


## Próximas etapas

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

Voltar para a [Lista de APIs](apis-list.md).


[5]: https://developer.salesforce.com
[6]: ./media/connectors-create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/connectors-create-api-salesforce/salesforce-create-app.png
[8]: ./media/connectors-create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0413_2016-->