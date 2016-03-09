<properties
pageTitle="Adicionar a API do Dynamic CRM Online no PowerApps Enterprise ou para seus aplicativos lógicos | Microsoft Azure"
description="Visão geral da API do CRM Online com os parâmetros de API REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/23/2016"
ms.author="deonhe"/>

# Introdução à API do CRM
Conecte-se ao Dynamics CRM Online para criar um novo registro, atualizar um item e muito mais.

A API do CRM pode ser usada em aplicativos PowerApps Enterprise e em aplicativos lógicos.

Com o CRM Online, você pode:

- Compilar seu fluxo de negócios baseado nos dados que obtém do CRM Online. 
- Usar ações que excluem um registro, obtêm entidades e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um item é atualizado no CRM, você pode enviar um email usando o Office 365.


Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
A API do CRM inclui as seguintes ações. Não há gatilhos.

| Gatilhos | Ações|
| --- | --- |
|Nenhum.| <ul><li>Criar um novo registro</li><li>Obter registros</li><li>Excluir um registro</li><li>Obter um registro</li><li>Obter entidades</li><li>Atualizar um item</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o CRM Online

### Adicionar um configuração adicional no PowerApps
Quando você adiciona o CRM Online ao PowerApps Enterprise, você insere os valores de **ID do Cliente** e **Chave do Aplicativo** de seu aplicativo AAD (Active Directory do Azure) do Dynamics CRM Online. O valor **URL de Redirecionamento** também é usado em seu aplicativo do CRM Online. Se você não tiver um aplicativo, use as seguintes etapas para criar o aplicativo:

1. No [Portal do Azure](https://portal.azure.com), abra **Active Directory** e selecione o nome de locatário de sua organização.
2. Na guia Aplicativos, selecione **Adicionar**. Em **Adicionar aplicativo**:  

	1. Digite um **Nome** para o aplicativo.  
	2. Deixe o tipo de aplicativo como **Web**.  
	3. Selecione **Avançar**.

	![Adicionar aplicativo do AAD - informações do aplicativo][9]

3. Em **Propriedades do aplicativo**:

	1. Digite a **URL DE LOGON** de seu aplicativo. Uma vez que você pretende autenticar com o AAD para PowerApps, defina a URL de logon para \__https://login.windows.net_.
2. Digite um **URI da ID de APLICATIVO** válido para seu aplicativo.  
	3. Selecione **OK**.  

	![Adicionar aplicativo do AAD - propriedades do aplicativo][10]

4. No novo aplicativo, escolha **Configurar**.
5. Em _OAuth 2_, defina a **URL de Resposta** como o valor da URL de redirecionamento mostrado quando você adiciona a API do CRM Online no Portal do Azure: ![Configure o aplicativo Contoso do AAD][12]

Agora, copie/cole os valores de **ID do Cliente** e **Chave do Aplicativo** em sua configuração do CRM Online no Portal do Azure.

### Adicionar configuração adicional em aplicativos lógicos
Ao adicionar essa API aos seus aplicativos lógicos, você precisa entrar no Dynamic CRM Online.

Depois de criar a conexão, insira as propriedades do CRM Online, como a tabela ou conjunto de dados. A **Referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência da API REST do Swagger
#### Esta documentação destina-se à versão: 1.0

### Criar um novo registro 
Crie um novo registro em uma entidade. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|
|tabela|string|sim|path|nenhum|O nome da entidade|
|item| |sim|body|nenhum|Registro para criação|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter registros 
 Obtém registros para uma entidade. ```GET: /datasets/{dataset}/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|
|tabela|string|sim|path|nenhum|O nome da entidade|
|$skip|inteiro|não|query|nenhum|Número de entradas para ignorar. O padrão é 0.|
|$top|inteiro|não|query|nenhum|Número máximo de entradas para buscar. O padrão é 100.|
|$filter|string|não|query|nenhum|Consulta de filtro ODATA para restringir o número de entradas.|
|$orderby|string|não|query|nenhum|Consulta orderBy ODATA para especificar a ordem das entradas.|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|




### Retornar os conjuntos de dados 
 Retorna os conjuntos de dados. ```GET: /datasets```

Não há parâmetros para esta chamada.

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|



### Obter um item de tabela 
Usado para obter um determinado registro presente para uma entidade CRM. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|
|tabela|string|sim|path|nenhum|O nome da entidade|
|ID|string|sim|path|nenhum|Identificador do registro|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

### Excluir um item de uma lista 
Exclui um item de uma lista. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|
|tabela|string|sim|path|nenhum|O nome da entidade|
|ID|string|sim|path|nenhum|Identificador do registro|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|



### Corrigir um item de tabela existente 
Usado para atualizar parcialmente um registro existente para uma entidade CRM. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|
|tabela|string|sim|path|nenhum|O nome da entidade|
|ID|string|sim|path|nenhum|Identificador do registro|
|item| |sim|body|nenhum|Registro para atualização|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

### Obter entidades 
Usado para obter a lista de entidades presentes em uma instância do CRM. ```GET: /datasets/{dataset}/tables```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


## Definições de objeto

#### DataSetsMetadata

|Nome da Propriedade | Tipo de Dados | Obrigatório|
|---|---|---|
|tabular|não definido|não|
|blob|não definido|não|

#### TabularDataSetsMetadata

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|
|tableDisplayName|string|não|
|tablePluralName|string|não|

#### BlobDataSetsMetadata

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|


#### TableMetadata

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|name|string|não|
|título|string|não|
|x-ms-permission|string|não|
|schema|não definido|não|

#### DataSetsList

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|

#### DataSet

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|


#### Tabela

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|

#### Item

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ItemInternalId|string|não|

#### ItemsList

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


#### TablesList

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


## Próximas etapas
Depois de adicionar a API do CRM Online para PowerApps Enterprise, [dê aos usuários permissões](../power-apps/powerapps-manage-api-connection-user-access.md) usar a API em seus aplicativos.

[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)


[9]: ./media/create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/create-api-crmonline/contoso-aad-app-configure.png

<!---HONumber=AcomDC_0224_2016-->