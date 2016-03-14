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
ms.date="02/25/2016"
ms.author="deonhe"/>

# Introdução à API do Salesforce
Conecte-se ao Salesforce e crie objetos, obtenha objetos muito mais. A API do Salesforce pode ser usada em:

- PowerApps 
- Aplicativos lógicos 

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para obter a versão do esquema 2014-12-01-preview, clique em [API do Salesforce](../app-service-logic/app-service-logic-connector-salesforce.md).

Com o Salesforce, você pode:

- Crie seu fluxo de negócios baseado nos dados obtidos do SalesForce. 
- Usar gatilhos para quando um objeto for criado ou atualizado.
- Use ações para criar um objeto, excluí-lo e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um novo objeto é criado no Salesforce, você pode enviar um email usando o Office 365.
- Adicione a API do Salesforce ao PowerApps Enterprise. Assim, seus usuários poderão usar essa API em seus próprios aplicativos. 

Para obter mais informações sobre como adicionar uma API no PowerApps Enterprise, vá para [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação nos aplicativos lógicos, veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
A API do Salesforce inclui o gatilho e ações a seguir.

| Gatilhos | Ações|
| --- | --- |
|<ul><li>Quando um objeto é criado</li><li>Quando um objeto é modificado</li></ul> | <ul><li>Criar objeto</li><li>Obter objetos</li><li>Quando um objeto é criado</li><li>Quando um objeto é modificado</li><li>Excluir objeto</li><li>Obter objeto</li><li>Obter tipos de objeto (SObjects)</li><li>Atualizar objeto</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o Salesforce 

### Adicionar um configuração adicional no PowerApps
Ao adicionar o Salesforce ao PowerApps Enterprise, insira os valores de **Chave do Aplicativo** e **Segredo do Aplicativo** de seu aplicativo do Salesforce. O valor da **URL de Redirecionamento** também é usado em seu aplicativo do Salesforce. Se você não tiver um aplicativo do Salesforce, poderá usar as seguintes etapas para criar o aplicativo:

1. [Entre na home page do desenvolvedor do Salesforce][5], selecione seu perfil e selecione **Configuração**: ![Salesforce página inicial][6]

3. Selecione **Criar** e selecione **Aplicativos**. Na página **Aplicativos**, em **Aplicativos Conectados**, selecione **Novo**: ![Salesforce criar aplicativo][7]

4. Em **Novo Aplicativo Conectado**:

	1. Insira o valor de **Nome do Aplicativo Conectado**.  
	2. Insira o valor de **Nome da API**.  
	3. Insira o valor de **Email de Contato**.  
	4. Em _API (Habilitar Configurações do OAuth)_, selecione **Habilitar Configurações do OAuth** e defina a **URL de Retorno de Chamada** como o valor mostrado ao adicionar a nova API do Salesforce no Portal do Azure.  

5. Em _Escopos de OAuth selecionados_, adicione os seguintes escopos para os **Escopos de OAuth selecionados**:

	- Acessar e gerenciar os dados de Chatter (chatter\_api)
	- Acessar e gerenciar seus dados (api)
	- Permitir o acesso ao seu identificador exclusivo (openid)
	- Executar solicitações no seu nome a qualquer momento (refresh\_token, offline\_access)

6. **Salve** suas alterações: ![Salesforce novo aplicativo][8]

Agora copie/cole os valores de **Chave do Aplicativo** e **Segredo do Aplicativo** em sua configuração do Salesforce no portal do Azure.

### Adicionar configuração adicional em aplicativos lógicos
Quando você adiciona essa API aos seus aplicativos lógicos, precisa autorizar que os aplicativos lógicos se conectem ao Salesforce.

1. Entre em sua conta do Salesforce.
2. Permitir que seus aplicativos lógicos se conectem e usem sua conta do Salesforce. 

Depois de criar a conexão, insira as propriedades do Salesforce, como o nome da tabela. A **referência da API REST** neste tópico descreve essas propriedades.

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
Depois de adicionar a API do Salesforce ao PowerApps Enterprise, [conceda permissões aos usuários](../power-apps/powerapps-manage-api-connection-user-access.md) para que eles usem a API em seus aplicativos.

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


[5]: https://developer.salesforce.com
[6]: ./media/create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/create-api-salesforce/salesforce-create-app.png
[8]: ./media/create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0302_2016-->