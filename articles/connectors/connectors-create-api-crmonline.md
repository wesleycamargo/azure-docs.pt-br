<properties
pageTitle="Adicionar o conector do Dynamic CRM Online no PowerApps Enterprise ou para seus aplicativos lógicos | Microsoft Azure"
description="Visão geral do conector do CRM Online com os parâmetros de API REST"
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
ms.date="05/18/2016"
ms.author="deonhe"/>

# Introdução ao conector do CRM
Conecte-se ao Dynamics CRM Online para criar um novo registro, atualizar um item e muito mais. O conector do CRM Online pode ser usado por meio de:

- Aplicativos lógicos
- PowerApps

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

Com o CRM Online, você pode:

- Compilar seu fluxo de negócios baseado nos dados que obtém do CRM Online. 
- Usar ações que excluem um registro, obtêm entidades e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um item é atualizado no CRM, você pode enviar um email usando o Office 365.


Para saber mais sobre como adicionar um conector ao PowerApps Enterprise, acesse [Registrar um conector no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação nos aplicativos lógicos, veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O Conector do CRM inclui as ações a seguir. Não há nenhum gatilho.

| Gatilhos | Ações|
| --- | --- |
|Nenhum| <ul><li>Criar um novo registro</li><li>Obtém registros</li><li>Excluir um registro</li><li>Obtém um registro</li><li>Obtém entidades</li><li>Atualizar um item</li></ul>

Todos os conectores dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o CRM Online

Ao adicionar esse conector aos seus aplicativos lógicos, é necessário entrar no Dynamic CRM Online. Siga estas etapas para entrar no CRM online e concluir a configuração da **conexão** em seu aplicativo lógico:

1. Em seu aplicativo lógico, selecione **Adicionar uma ação**: ![Configurar o CRM Online][13]
4. Insira CRM na caixa de pesquisa e aguarde até que a pesquisa retorne todas as entradas com CRM no nome.
5. Selecione **Dynamics CRM Online - Criar um novo registro**.
6. Selecione **Entrar no Dynamics CRM Online**: ![Configurar o CRM Online][14]
7. Forneça suas credenciais do CRM Online para entrar e autorizar o aplicativo: ![Configurar o CRM Online][15]  
8. Depois de entrar, retorne ao aplicativo lógico para concluí-lo adicionando outros gatilhos e outras ações necessárias.
9. Salve seu trabalho selecionando **Salvar** na barra de menus acima.

Depois de criar a conexão, insira as propriedades do CRM Online, como a tabela ou conjunto de dados. A **referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Criar um novo registro 
Crie um novo registro em uma entidade.```POST: /datasets/{dataset}/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|
|tabela|string|sim|path|nenhum|O nome da entidade|
|item| |sim|corpo|nenhum|Registro para criação|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter registros 
 Obtenha registros para uma entidade.```GET: /datasets/{dataset}/tables/{table}/items```

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
 Retorne os conjuntos de dados.```GET: /datasets```

Não há parâmetros para esta chamada.

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|



### Obter um item de tabela 
Usado para obter um registro específico presente para uma entidade do CRM.```GET: /datasets/{dataset}/tables/{table}/items/{id}```

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
Excluir um Item de uma Lista.```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

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
Usado para atualizar parcialmente um registro existente para uma entidade do CRM.```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|
|tabela|string|sim|path|nenhum|O nome da entidade|
|ID|string|sim|path|nenhum|Identificador do registro|
|item| |sim|corpo|nenhum|Registro para atualização|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

### Obter entidades 
Usado para obter a lista de entidades presentes em uma instância do CRM.```GET: /datasets/{dataset}/tables```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|Nome exclusivo da organização CRM contoso.crm|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


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

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).


[9]: ./media/connectors-create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/connectors-create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/connectors-create-api-crmonline/contoso-aad-app-configure.png
[13]: ./media/connectors-create-api-crmonline/crmconfig1.png
[14]: ./media/connectors-create-api-crmonline/crmconfig2.png
[15]: ./media/connectors-create-api-crmonline/crmconfig3.png

<!---HONumber=AcomDC_0525_2016-->