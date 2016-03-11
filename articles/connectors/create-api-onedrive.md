<properties
pageTitle="Adicionar a API do OneDrive a Aplicativos Lógicos e ao PowerApps Enterprise | Microsoft Azure"
description="Visão geral da API do OneDrive com os parâmetros da API REST"
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
ms.date="02/22/2016"
ms.author="deonhe"/>

# Introdução à API do OneDrive

Conecte-se ao OneDrive para gerenciar seus arquivos. Você pode executar várias ações, como carregar, atualizar, obter e excluir arquivos no OneDrive.

A API do OneDrive pode ser usada em aplicativos PowerApps Enterprise e em aplicativos lógicos.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [API do OneDrive](../app-service-logic/app-service-logic-connector-onedrive.md).

Com o OneDrive, você pode:

- Crie seu fluxo de negócios baseado nos dados obtidos do OneDrive. 
- Usar gatilhos para quando um arquivo for criado ou atualizado.
- Usar ações para criar um arquivo, excluir um arquivo e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um novo arquivo é criado no OneDrive, você pode enviar esse arquivo por email usando o Office 365.
- Adicione a API do OneDrive ao PowerApps Enterprise. Assim, seus usuários poderão usar essa API em seus próprios aplicativos. 

Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
A API do OneDrive inclui o gatilho e ações a seguir.

| Gatilhos | Ações|
| --- | --- |
|<ul><li>Quando um arquivo é criado</li><li>Quando um arquivo é modificado</li></ul> | <ul><li>Criar arquivo</li><li>Listar arquivos em uma pasta</li><li>Quando um arquivo é criado</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Extrair pasta</li><li>Obter o conteúdo do arquivo usando a ID</li><li>Obter conteúdo do arquivo usando o caminho</li><li>Obter metadados do arquivo usando a ID</li><li>Obter metadados do arquivo usando o caminho</li><li>Listar pasta raiz</li><li>Atualizar arquivo</li><li>Quando um arquivo é modificado</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o OneDrive

### Adicionar um configuração adicional no PowerApps
Quando você adiciona o OneDrive ao PowerApps Enterprise, você insere os valores de **Chave do Aplicativo** e **Segredo do Aplicativo** de seu aplicativo do OneDrive. O valor **URL de redirecionamento** também é usado em seu aplicativo do OneDrive. Se você não tiver um aplicativo do OneDrive, poderá usar as seguintes etapas para criar o aplicativo:

1. Vá para a [página de criação de aplicativo][5] no _Centro do desenvolvedor da conta da Microsoft_ e entre com sua _Conta da Microsoft_.

2. Insira seu **Nome do aplicativo** e aceite o contrato:  
![Novo aplicativo do OneDrive][6]

3. Em configurações:

	1. Selecione **Configurações da API**.  
	2. Defina a **URL de redirecionamento** como o valor mostrado quando você adiciona a nova API do OneDrive ao Portal do Azure.  
	3. **Salve** suas alterações.  

	![Configurações da API do aplicativo do OneDrive][7]

Agora, copie/cole os valores de **Chave do Aplicativo** e **Segredo do Aplicativo** em sua configuração do OneDrive no portal do Azure.

### Adicionar configuração adicional em aplicativos lógicos
Quando você adiciona essa API aos seus aplicativos lógicos, precisa autorizar que os aplicativos lógicos se conectem ao OneDrive.

1. Entre em sua conta do OneDrive.
2. Permitir que seus aplicativos lógicos se conectem e usem o OneDrive. 

Depois de criar a conexão, insira as propriedades do OneDrive, como o caminho da pasta ou o nome do arquivo. A **referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão em outros aplicativos lógicos.

## Referência da API REST do Swagger
#### Esta documentação destina-se à versão: 1.0


### Obter metadados de arquivo usando a id
Recupera os metadados de um arquivo no OneDrive usando a ID. ```GET: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo no OneDrive|

### Respostas
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Atualizar arquivo
Atualiza um arquivo no OneDrive. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo a ser atualizado no OneDrive|
|corpo| |sim|corpo|nenhum|Conteúdo do arquivo a ser atualizado no OneDrive|


### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|

### Excluir arquivo
Exclui um arquivo do OneDrive. ```DELETE: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo a ser excluído do OneDrive|


### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter metadados do arquivo usando o caminho
Recupera os metadados de um arquivo no OneDrive usando o caminho. ```GET: /datasets/default/GetFileByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum|Caminho exclusivo para o arquivo no OneDrive|


### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|




### Obter o conteúdo do arquivo usando o caminho
Recupera o conteúdo de um arquivo no OneDrive usando o caminho. ```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum|Caminho exclusivo para o arquivo no OneDrive|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|




### Obter o conteúdo do arquivo usando a ID
Recupera o conteúdo de um arquivo no OneDrive usando a ID. ```GET: /datasets/default/files/{id}/content```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo no OneDrive|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|




### Criar arquivo
Carrega um arquivo no OneDrive. ```POST: /datasets/default/files```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|sim|query|nenhum|Caminho da pasta para carregar o arquivo no OneDrive|
|name|string|sim|query|nenhum|Nome do arquivo a ser criado no OneDrive|
|corpo| |sim|corpo|nenhum|Conteúdo do arquivo para carregar no OneDrive|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Copiar arquivo
Copia um arquivo para o OneDrive. ```POST: /datasets/default/copyFile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum|URL para o arquivo de origem|
|destino|string|sim|query|nenhum|Caminho do arquivo de destino no OneDrive, incluindo nome do arquivo de destino|
|substituir|booleano|não|query|false|Substitui o arquivo de destino se estiver definido como "true"|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Quando um arquivo é criado
Dispara um fluxo quando um novo arquivo é criado em uma pasta do OneDrive. ```GET: /datasets/default/triggers/onnewfile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|string|sim|query|nenhum|Identificador exclusivo da pasta no OneDrive|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Dispara um fluxo quando um arquivo é modificado em uma pasta do OneDrive.
Dispara um fluxo quando um arquivo é modificado em uma pasta do OneDrive. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|string|sim|query|nenhum|Identificador exclusivo da pasta no OneDrive|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|



### Extrair pasta
Extrai uma pasta para o OneDrive. ```POST: /datasets/default/extractFolderV2```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum|Caminho para o arquivo morto|
|destino|string|sim|query|nenhum|Caminho no OneDrive para extrair o conteúdo do arquivo morto|
|substituir|booleano|não|query|false|Substitui os arquivos de destino se estiver definido como "true"|


### Resposta

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



#### BlobMetadata

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|
|Nome|string|não|
|DisplayName|string|não|
|Caminho|string|não|
|LastModified|string|não|
|Tamanho|inteiro|não|
|MediaType|string|não|
|IsFolder|booleano|não|
|ETag|string|não|
|FileLocator|string|não|


## Próximas etapas
Depois de adicionar a API do OneDrive para PowerApps Enterprise, [dê aos usuários permissões](../power-apps/powerapps-manage-api-connection-user-access.md) usar a API em seus aplicativos.

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


[5]: https://account.live.com/developers/applications/create
[6]: ./media/create-api-onedrive/onedrive-new-app.png
[7]: ./media/create-api-onedrive/onedrive-app-api-settings.png

<!---HONumber=AcomDC_0224_2016-->
