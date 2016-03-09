<properties
pageTitle="Usar a API do SharePoint Server em seus Aplicativos Lógicos | Microsoft Azure"
description="Introdução ao uso da API do SharePoint Server do Serviço de Aplicativo do Azure com seus aplicativos lógicos e seus Power apps."
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
ms.date="02/18/2016"
ms.author="deonhe"/>

# Introdução à API do SharePoint

O provedor de conexão do SharePoint fornece uma API para trabalhar com as Listas do SharePoint.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [SharePoint](../app-service-logic/app-service-logic-connector-SharePoint.md).

Com o SharePoint, você pode:

* Usá-lo para criar aplicativos lógicos
* Usá-lo para criar power apps  

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Vamos conversar sobre gatilhos e ações

A API do SharePoint pode ser usada como uma ação. Ela tem gatilhos. Todas as APIs dão suporte a dados nos formatos JSON e XML.

A API do SharePoint possui as seguintes ações e/ou gatilhos disponíveis:

### Ações do SharePoint
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|GetFileMetadata|Usada para obter metadados de um arquivo da Biblioteca de Documentos|
|UpdateFile|Usada para atualizar um arquivo na Biblioteca de Documentos|
|DeleteFile|Usada para excluir um arquivo na Biblioteca de Documentos|
|GetFileMetadataByPath|Usada para obter metadados de um arquivo da Biblioteca de Documentos|
|GetFileContentByPath|Usada para obter um arquivo na Biblioteca de Documentos|
|GetFileContent|Usada para obter um arquivo na Biblioteca de Documentos|
|CreateFile|Usada para carregar um arquivo na biblioteca de documentos|
|CopyFile|Usada para copiar um arquivo na Biblioteca de Documentos|
|ExtractFolderV2|Usada para extrair uma pasta na Biblioteca de Documentos|
|PostItem|Cria um novo item em uma lista do SharePoint|
|GetItem|Recupera um único item de uma lista do SharePoint|
|DeleteItem|Exclui um item de uma lista do SharePoint|
|PatchItem|Atualiza um item em uma lista do SharePoint|
### Gatilhos do SharePoint
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|OnNewFile|Dispara um fluxo quando um novo arquivo é criado em uma pasta do SharePoint|
|OnUpdatedFile|Dispara um fluxo quando um arquivo é modificado em uma pasta do SharePoint|
|GetOnNewItems|Quando um novo item é criado em uma lista do SharePoint|
|GetOnUpdatedItems|Quando um item existente é modificado em uma lista do SharePoint|


## Criar uma conexão com o SharePoint
Para usar a API do SharePoint, crie primeiro uma **conexão** e então forneça os detalhes dessas propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer credenciais do SharePoint|  

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos, em power apps ou em ambos.

## Referência da API REST do SharePoint
#### Esta documentação destina-se à versão: 1.0


### Usada para obter metadados de um arquivo da Biblioteca de Documentos
**```GET: /datasets/{dataset}/files/{id}```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Usada para atualizar um arquivo na Biblioteca de Documentos
**```PUT: /datasets/{dataset}/files/{id}```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo|
|corpo| |sim|corpo|nenhum|O conteúdo do arquivo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Usada para excluir um arquivo na Biblioteca de Documentos
**```DELETE: /datasets/{dataset}/files/{id}```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Usada para obter metadados de um arquivo da Biblioteca de Documentos
**```GET: /datasets/{dataset}/GetFileByPath```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|path|string|sim|query|nenhum|Caminho do arquivo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Usada para obter um arquivo na Biblioteca de Documentos
**```GET: /datasets/{dataset}/GetFileContentByPath```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|path|string|sim|query|nenhum|Caminho do arquivo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Usada para obter um arquivo na Biblioteca de Documentos
**```GET: /datasets/{dataset}/files/{id}/content```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Usada para carregar um arquivo na biblioteca de documentos
**```POST: /datasets/{dataset}/files```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|folderPath|string|sim|query|nenhum|O caminho para a pasta|
|name|string|sim|query|nenhum|Nome do arquivo|
|corpo| |sim|corpo|nenhum|O conteúdo do arquivo|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Usada para copiar um arquivo na Biblioteca de Documentos
**```POST: /datasets/{dataset}/copyFile```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|fonte|string|sim|query|nenhum|Caminho para o arquivo de origem|
|destino|string|sim|query|nenhum|Caminho para o arquivo de destino|
|substituir|booleano|não|query|false|Se deseja ou não substituir um arquivo existente|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Dispara um fluxo quando um novo arquivo é criado em uma pasta do SharePoint
**```GET: /datasets/{dataset}/triggers/onnewfile```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint|
|folderId|string|sim|query|nenhum|Identificador exclusivo da pasta no SharePoint|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Dispara um fluxo quando um arquivo é modificado em uma pasta do SharePoint
**```GET: /datasets/{dataset}/triggers/onupdatedfile```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint|
|folderId|string|sim|query|nenhum|Identificador exclusivo da pasta no SharePoint|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Usada para extrair uma pasta na Biblioteca de Documentos
**```POST: /datasets/{dataset}/extractFolderV2```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint. Por exemplo, http://contoso.sharepoint.com/sites/mysite|
|fonte|string|sim|query|nenhum|Caminho para o arquivo de origem|
|destino|string|sim|query|nenhum|Caminho para a pasta de destino|
|substituir|booleano|não|query|false|Se deseja ou não substituir um arquivo existente|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Quando um novo item é criado em uma lista do SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|tabela|string|sim|path|nenhum|Nome de lista do SharePoint|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Quando um item existente é modificado em uma lista do SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|tabela|string|sim|path|nenhum|Nome de lista do SharePoint|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Cria um novo item em uma lista do SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|tabela|string|sim|path|nenhum|Nome de lista do SharePoint|
|item| |sim|corpo|nenhum|Item a ser criado|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Recupera um único item de uma lista do SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|tabela|string|sim|path|nenhum|Nome de lista do SharePoint|
|ID|inteiro|sim|path|nenhum|Identificador exclusivo do item a ser recuperado|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Exclui um item de uma lista do SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|tabela|string|sim|path|nenhum|Nome de lista do SharePoint|
|ID|inteiro|sim|path|nenhum|Identificador exclusivo do item a ser excluído|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Atualiza um item em uma lista do SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|tabela|string|sim|path|nenhum|Nome de lista do SharePoint|
|ID|inteiro|sim|path|nenhum|Identificador exclusivo do item a ser atualizado|
|item| |sim|corpo|nenhum|Item com propriedades alteradas|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



## Definições de objeto: 

 **DataSetsMetadata**:

Propriedades obrigatórias para DataSetsMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|tabular|não definido|
|blob|não definido|



 **TabularDataSetsMetadata**:

Propriedades obrigatórias para TabularDataSetsMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|fonte|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

Propriedades obrigatórias para BlobDataSetsMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|fonte|string|
|displayName|string|
|urlEncoding|string|



 **BlobMetadata**:

Propriedades obrigatórias para BlobMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|string|



 **Objeto**:

Propriedades obrigatórias para o objeto:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|



 **TableMetadata**:

Propriedades obrigatórias para TableMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|name|string|
|título|string|
|x-ms-permission|string|
|schema|não definido|



 **DataSetsList**:

Propriedades obrigatórias para DataSetsList:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|



 **DataSet**:

Propriedades obrigatórias para DataSet:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Nome|string|
|DisplayName|string|



 **Tabela**:

Propriedades necessárias para Table:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Nome|string|
|DisplayName|string|



 **Item**:

Propriedades obrigatórias para Item:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ItemInternalId|string|



 **ItemsList**:

Propriedades obrigatórias para ItemsList:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|



 **TablesList**:

Propriedades obrigatórias para TablesList:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) [Criar um power app](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0224_2016-->