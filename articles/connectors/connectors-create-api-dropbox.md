<properties
	pageTitle="Adicionar a API do Dropbox a aplicativos lógicos e ao PowerApps Enterprise | Microsoft Azure"
	description="Visão geral da API do Dropbox com os parâmetros da API REST"
	services=""
    suite=""
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
   ms.date="03/16/2016"
   ms.author="mandia"/>

# Introdução à API do Dropbox 
Conecte-se ao Dropbox para gerenciar arquivos, por exemplo, criar e obter arquivos, e muito mais. A API do Dropbox pode ser usada em:

- Aplicativos lógicos 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

&nbsp;

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para obter a versão do esquema 2014-12-01-preview, clique em [Conector do Dropbox](../app-service-logic/app-service-logic-connector-dropbox.md).


Com o Dropbox, você pode:

- Criar seu fluxo de negócios baseado nos dados que obtém do Dropbox. 
- Usar gatilhos para quando um arquivo for criado ou atualizado.
- Usar ações para criar um arquivo, excluir um arquivo e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um novo arquivo é criado no Dropbox, você pode enviar esse arquivo por email usando o Office 365.
- Adicione a API do Dropbox ao PowerApps Enterprise. Assim, seus usuários poderão usar essa API em seus próprios aplicativos. 

Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação aos aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O Dropbox inclui os gatilhos e as ações a seguir.

Gatilhos | Ações
--- | ---
<ul><li>Quando um arquivo é criado</li><li>Quando um arquivo é modificado</li></ul> | <ul><li>Criar arquivo</li><li>Quando um arquivo é criado</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Extrair o arquivo para a pasta</li><li>Obter conteúdo do arquivo usando a ID</li><li>Obter o arquivo usando o caminho</li><li>Obter metadados do arquivo usando a ID</li><li>Obter metadados do arquivo usando o caminho</li><li>Atualizar arquivo</li><li>Quando um arquivo é modificado</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar a conexão com o Dropbox

Quando você adiciona essa API a seus aplicativos lógicos, precisa autorizar os aplicativos lógicos a se conectarem ao Dropbox.

1. Entre na sua conta do Dropbox.
2. Selecione **Autorizar** e permita que seus aplicativos lógicos se conectem e usem o Dropbox. 

Depois de criar a conexão, insira as propriedades do Dropbox, como o caminho da pasta ou o nome do arquivo. A **referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão do Dropbox em outros aplicativos lógicos.

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Criar arquivo    
Carrega um arquivo no Dropbox.```POST: /datasets/default/files```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|sim|query|nenhum |Caminho da pasta para carregar o arquivo no Dropbox|
|name|string|sim|query|nenhum |Nome do arquivo a ser criado no Dropbox|
|body|string(binary) |sim|corpo|nenhum |Conteúdo do arquivo para carregar no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Quando um arquivo é criado    
Dispara um fluxo quando um novo arquivo é criado em uma pasta do Dropbox.```GET: /datasets/default/triggers/onnewfile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|string|sim|query|nenhum |Identificador exclusivo da pasta no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Copiar arquivo    
Copia um arquivo no Dropbox.```POST: /datasets/default/copyFile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum |URL para o arquivo de origem|
|destino|string|sim|query| nenhum|Caminho do arquivo de destino no Dropbox, incluindo nome do arquivo de destino|
|overwrite|booleano|não|query|nenhum |Substitui o arquivo de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Excluir arquivo    
Exclui um arquivo do Dropbox.```DELETE: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo a ser excluído do Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Extrair o arquivo morto para a pasta    
Extrai um arquivo morto para uma pasta no Dropbox (exemplo: .zip).**```POST: /datasets/default/extractFolderV2```**

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum |Caminho para o arquivo morto|
|destino|string|sim|query|nenhum |Caminho no Dropbox para extrair o conteúdo do arquivo morto|
|overwrite|booleano|não|query|nenhum |Substitui os arquivos de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando a ID    
Recupera o conteúdo do arquivo do Dropbox usando a ID.```GET: /datasets/default/files/{id}/content```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando o caminho    
Recupera o conteúdo do arquivo do Dropbox usando o caminho.```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho exclusivo para o arquivo no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter metadados de arquivo usando a id    
Recupera os metadados do arquivo do Dropbox usando a ID do arquivo.```GET: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter metadados de arquivo usando o caminho    
Recupera os metadados do arquivo do Dropbox usando o caminho.```GET: /datasets/default/GetFileByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho exclusivo para o arquivo no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Atualizar arquivo    
Atualiza um arquivo no Dropbox.```PUT: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path| nenhum|Identificador exclusivo do arquivo a ser atualizado no Dropbox|
|body|string(binary) |sim|corpo|nenhum |Conteúdo do arquivo a ser atualizado no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Quando um arquivo é modificado    
Dispara um fluxo quando um arquivo é modificado em uma pasta do Dropbox.```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|string|sim|query|nenhum |Identificador exclusivo da pasta no Dropbox|

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

[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/connectors-create-api-dropbox/dropbox-developer-site.png
[9]: ./media/connectors-create-api-dropbox/dropbox-create-app.png
[10]: ./media/connectors-create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/connectors-create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0323_2016-->