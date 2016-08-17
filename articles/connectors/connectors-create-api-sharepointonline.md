<properties
pageTitle="Saiba como usar o conector do SharePoint Online em aplicativos lógicos| Microsoft Azure"
description="Crie aplicativos lógicos com o conector do SharePoint Online para gerenciar listas no SharePoint."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/19/2016"
ms.author="deonhe"/>

# Introdução ao conector do SharePoint Online

Use o conector do SharePoint Online para gerenciar listas do SharePoint.

Para usar [qualquer conector](./apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar-se ao SharePoint Online

Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](./connectors-overview.md) fornece conectividade entre um aplicativo lógico e outro serviço.

### Criar uma conexão com o SharePoint Online

>[AZURE.INCLUDE [Etapas para criar uma conexão com o SharePoint](../../includes/connectors-create-api-sharepointonline.md)]

## Usar um gatilho do SharePoint Online

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Etapas para criar um gatilho SharePoint Online](../../includes/connectors-create-api-sharepointonline-trigger.md)]

## Usar uma ação do SharePoint Online

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Etapas para criar uma ação do SharePoint Online](../../includes/connectors-create-api-sharepointonline-action.md)]

## Detalhes técnicos

Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## Gatilhos do SharePoint Online

O SharePoint tem os seguintes gatilhos:

|Gatilho | Descrição|
|--- | ---|
|[Quando um arquivo é criado](connectors-create-api-sharepointonline.md#when-a-file-is-created)|Esta operação dispara um fluxo quando um novo arquivo é criado em uma pasta do SharePoint.|
|[Quando um arquivo é modificado](connectors-create-api-sharepointonline.md#when-a-file-is-modified)|Esta operação dispara um fluxo quando um arquivo é modificado em uma pasta do SharePoint.|
|[Quando um novo item é criado](connectors-create-api-sharepointonline.md#when-a-new-item-is-created)|Esta operação dispara um fluxo quando um novo item é criado em uma lista do SharePoint.|
|[Quando um item existente é modificado](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified)|Esta operação dispara um fluxo quando um item existente é modificado em uma lista do SharePoint.|


## Ações do SharePoint Online

O SharePoint tem as seguintes ações:


|Ação|Descrição|
|--- | ---|
|[Obter metadados do arquivo](connectors-create-api-sharepointonline.md#get-file-metadata)|Esta operação obtém os metadados do arquivo usando a ID do arquivo.|
|[Atualizar arquivo](connectors-create-api-sharepointonline.md#update-file)|Esta operação atualiza o conteúdo do arquivo.|
|[Excluir arquivo](connectors-create-api-sharepointonline.md#delete-file)|Esta operação exclui um arquivo.|
|[Obter metadados do arquivo usando o caminho](connectors-create-api-sharepointonline.md#get-file-metadata-using-path)|Esta operação obtém os metadados do arquivo usando o caminho do arquivo.|
|[Obter o conteúdo do arquivo usando o caminho](connectors-create-api-sharepointonline.md#get-file-content-using-path)|Esta operação obtém o conteúdo do arquivo usando o caminho do arquivo.|
|[Obter conteúdo do arquivo](connectors-create-api-sharepointonline.md#get-file-content)|Esta operação obtém o conteúdo do arquivo usando a ID do arquivo.|
|[Criar arquivo](connectors-create-api-sharepointonline.md#create-file)|Esta operação carrega um arquivo em um site do SharePoint.|
|[Copiar arquivo](connectors-create-api-sharepointonline.md#copy-file)|Esta operação copia um arquivo em um site do SharePoint.|
|[Listar pasta](connectors-create-api-sharepointonline.md#list-folder)|Esta operação obtém os arquivos contidos em uma pasta do SharePoint.|
|[Listar pasta raiz](connectors-create-api-sharepointonline.md#list-root-folder)|Esta operação obtém os arquivos na pasta raiz do SharePoint.|
|[Extrair pasta](connectors-create-api-sharepointonline.md#extract-folder)|Esta operação extrai um arquivo morto para uma pasta do SharePoint (exemplo: .zip).|
|[Obter itens](connectors-create-api-sharepointonline.md#get-items)|Esta operação obtém os itens de uma lista do SharePoint.|
|[Criar item](connectors-create-api-sharepointonline.md#create-item)|Esta operação cria um novo item em uma lista do SharePoint.|
|[Obter item](connectors-create-api-sharepointonline.md#get-item)|Esta operação obtém um único item por sua ID em uma lista do SharePoint.|
|[Excluir item](connectors-create-api-sharepointonline.md#delete-item)|Esta operação exclui um item de uma lista do SharePoint.|
|[Atualizar item](connectors-create-api-sharepointonline.md#update-item)|Esta operação atualiza um item em uma lista do SharePoint.|
|[Obter valores de entidade](connectors-create-api-sharepointonline.md#get-entity-values)|Esta operação obtém os valores possíveis para uma entidade do SharePoint.|
|[Obter listas](connectors-create-api-sharepointonline.md#get-lists)|Esta operação obtém as listas do SharePoint em um site.|
### Detalhes da ação

Veja abaixo os detalhes das ações e dos gatilhos para esse conector, com suas respostas:



### Obter metadados do arquivo
Esta operação obtém os metadados do arquivo usando a ID do arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador do arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
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




### Atualizar arquivo
Esta operação atualiza o conteúdo do arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador do arquivo|Selecionar um arquivo|
|body*|Conteúdo do arquivo|O conteúdo do arquivo|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
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




### Excluir arquivo
Esta operação exclui um arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador do arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória




### Obter metadados do arquivo usando o caminho
Esta operação obtém os metadados do arquivo usando o caminho do arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|path*|Caminho do arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
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




### Obter o conteúdo do arquivo usando o caminho
Esta operação obtém o conteúdo do arquivo usando o caminho do arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|path*|Caminho do arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória




### Obter conteúdo do arquivo
Esta operação obtém o conteúdo do arquivo usando a ID do arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador do arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória




### Criar arquivo
Esta operação carrega um arquivo em um site do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|folderPath*|Caminho da pasta|Selecionar um arquivo|
|name*|Nome do arquivo|Nome do arquivo|
|body*|Conteúdo do arquivo|O conteúdo do arquivo|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
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




### Copiar arquivo
Esta operação copia um arquivo em um site do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|source*|Caminho do arquivo de origem|Caminho para o arquivo de origem|
|destination*|Caminho do arquivo de destino|Caminho para o arquivo de destino|
|substituir|Sinalizador de substituição|Se o arquivo de destino existir, se ele deve ou não ser substituído|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
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




### Quando um arquivo é criado
Esta operação dispara um fluxo quando um novo arquivo é criado em uma pasta do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint|
|folderId*|ID da pasta|Selecionar uma pasta|

Um * indica que uma propriedade é obrigatória




### Quando um arquivo é modificado
Esta operação dispara um fluxo quando um arquivo é modificado em uma pasta do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint|
|folderId*|ID da pasta|Selecionar uma pasta|

Um * indica que uma propriedade é obrigatória




### Listar pasta
Esta operação obtém os arquivos contidos em uma pasta do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador do arquivo|Identificador exclusivo da pasta|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
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




### Listar pasta raiz
Esta operação obtém os arquivos na pasta raiz do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
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




### Extrair pasta
Esta operação extrai um arquivo morto para uma pasta do SharePoint (exemplo: .zip).


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint, como http://contoso.sharepoint.com/sites/mysite|
|source*|Caminho do arquivo de origem|Caminho para o arquivo de origem|
|destination*|Caminho da pasta de destino|Caminho para a pasta de destino|
|substituir|Sinalizador de substituição|Se o arquivo de destino existir, se ele deve ou não ser substituído|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
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




### Quando um novo item é criado
Esta operação dispara um fluxo quando um novo item é criado em uma lista do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nome da lista|Nome de lista do SharePoint|
|$filter|Consulta de filtro|Uma consulta de filtro ODATA para restringir as entradas retornadas|
|$orderby|Ordenar por|Uma consulta orderBy do ODATA para especificar a ordem das entradas|
|$skip|Ignorar contagem|Número de entradas a serem ignoradas (padrão = 0)|
|$top|Obter Contagem Máxima|Número máximo de entradas a serem recuperadas (padrão = 256)|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

ItemsList


| Nome da Propriedade | Tipo de Dados | 
|---|---|
|value|array|




### Quando um item existente é modificado
Esta operação dispara um fluxo quando um item existente é modificado em uma lista do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nome da lista|Nome de lista do SharePoint|
|$filter|Consulta de filtro|Uma consulta de filtro ODATA para restringir as entradas retornadas|
|$orderby|Ordenar por|Uma consulta orderBy do ODATA para especificar a ordem das entradas|
|$skip|Ignorar contagem|Número de entradas a serem ignoradas (padrão = 0)|
|$top|Obter Contagem Máxima|Número máximo de entradas a serem recuperadas (padrão = 256)|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

ItemsList


| Nome da Propriedade | Tipo de Dados |
|---|---|
|value|array|




### Obter itens
Esta operação obtém os itens de uma lista do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nome da lista|Nome de lista do SharePoint|
|$filter|Consulta de filtro|Uma consulta de filtro ODATA para restringir as entradas retornadas|
|$orderby|Ordenar por|Uma consulta orderBy do ODATA para especificar a ordem das entradas|
|$skip|Ignorar contagem|Número de entradas a serem ignoradas (padrão = 0)|
|$top|Obter Contagem Máxima|Número máximo de entradas a serem recuperadas (padrão = 256)|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

ItemsList


| Nome da Propriedade | Tipo de Dados |
|---|---|
|value|array|




### Criar item
Esta operação cria um novo item em uma lista do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nome da lista|Nome de lista do SharePoint|
|item*|Item|Item a ser criado|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

Item


| Nome da Propriedade | Tipo de Dados |
|---|---|
|ItemInternalId|string|




### Obter item
Esta operação obtém um único item por sua ID em uma lista do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nome da lista|Nome de lista do SharePoint|
|id*|ID|Identificador exclusivo do item a ser recuperado|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

Item


| Nome da Propriedade | Tipo de Dados |
|---|---|
|ItemInternalId|string|




### Excluir item
Esta operação exclui um item de uma lista do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nome da lista|Nome de lista do SharePoint|
|id*|ID|Identificador exclusivo do item a ser excluído|

Um * indica que uma propriedade é obrigatória




### Atualizar item
Esta operação atualiza um item em uma lista do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nome da lista|Nome de lista do SharePoint|
|id*|ID|Identificador exclusivo do item a ser atualizado|
|item*|Item|Item com propriedades alteradas|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

Item


| Nome da Propriedade | Tipo de Dados |
|---|---|
|ItemInternalId|string|




### Obter valores de entidade
Esta operação obtém os valores possíveis para uma entidade do SharePoint.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site do SharePoint|URL do site do SharePoint|
|table*|nome da tabela|nome da tabela|
|id*|id da entidade|id da entidade|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída





### Obter listas
Esta operação obtém as listas do SharePoint em um site.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|dataset*|URL do site|URL do site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite)|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

TablesList


| Nome da Propriedade | Tipo de Dados |
|---|---|
|value|array|



## Respostas HTTP

As ações e os gatilhos acima podem retornar um ou mais dos seguintes códigos de status HTTP:

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido.|
|padrão|Falha na Operação.|















## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->