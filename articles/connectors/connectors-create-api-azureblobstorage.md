<properties
	pageTitle="Adicionar a API do armazenamento de blobs do Azure a seus Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da API do armazenamento de blobs do Azure com parâmetros da API REST"
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
   ms.date="03/16/2016"
   ms.author="mandia"/>

# Introdução à API de armazenamento de blobs do Azure
Conecte-se a um Blob do Azure para gerenciar arquivos em um contêiner de blob, criando os arquivos, excluindo os arquivos e muito mais. A API do armazenamento de blobs do Azure pode ser usada em:

- Aplicativos lógicos 

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para obter a versão do esquema 2014-12-01-preview, clique em [Conector do Blob de Armazenamento do Azure](../app-service-logic/app-service-logic-connector-azurestorageblob.md).

Com o armazenamento de blobs do Azure, você pode:

- Criar seu fluxo de negócios com base nos dados obtidos do blob. 
- Usar as ações que permitem que você crie um arquivo, obter seu conteúdo e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, você pode procurar por "urgente" em um arquivo no seu blob e enviar todos os arquivos com "urgente" em um email usando o Office 365. 

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O blob do Azure inclui as ações a seguir. Não há gatilhos.

| Gatilhos | Ações|
| --- | --- |
| Nenhum. | <ul><li>Criar arquivo</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Extrair arquivo morto para a pasta</li><li>Obter conteúdo do arquivo</li><li>Obter conteúdo do arquivo usando o caminho</li><li>Obter metadados do arquivo</li><li>Obter metadados do arquivo usando o caminho</li><li>Atualizar arquivo</li></ul> |

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o blob do Azure
Ao adicionar essa API aos seus aplicativos lógicos, insira os seguintes valores da conta de armazenamento:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Nome da conta de armazenamento do Azure | sim | O nome da sua conta de armazenamento de blobs|
|Chave de acesso da conta de armazenamento do Azure | sim | A chave de acesso para sua conta de armazenamento de blobs|

Depois de criar a conexão, insira as propriedades do blob, como o caminho da pasta ou o nome do arquivo. A **referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão de blob em outros aplicativos lógicos.
 

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Criar arquivo
Carrega um arquivo no Armazenamento de Blobs do Azure.```POST: /datasets/default/files```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|sim|query| nenhum |Caminho da pasta para carregar o arquivo no Armazenamento de Blobs do Azure|
|name|string|sim|query|nenhum |Nome do arquivo a ser criado no Armazenamento de Blobs do Azure|
|corpo|string(binary) |sim|corpo|nenhum|Conteúdo do arquivo a ser carregado no Armazenamento de Blobs do Azure|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

### Copiar arquivo
Copia um arquivo no Armazenamento de Blobs do Azure.```POST: /datasets/default/copyFile```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum |URL para o arquivo de origem|
|destino|string|sim|query| nenhum|Caminho do arquivo de destino no Armazenamento de Blobs do Azure, incluindo o nome do arquivo de destino|
|substituir|booleano|não|query|nenhum |Substitui o arquivo de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Excluir arquivo
Exclui um arquivo do Armazenamento de Blobs do Azure.```DELETE: /datasets/default/files/{id}```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo a ser excluído do Armazenamento de Blobs do Azure|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Extrair o arquivo morto para a pasta
Extrai um arquivo morto para uma pasta no Armazenamento de Blobs do Azure (exemplo: .zip).```POST: /datasets/default/ExtractFolderV2```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query| nenhum|Caminho para o arquivo morto|
|destino|string|sim|query|nenhum |Caminho no Armazenamento de Blobs do Azure para extrair o conteúdo do arquivo morto|
|substituir|booleano|não|query|nenhum |Substitui os arquivos de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter conteúdo do arquivo
Recupera o conteúdo do arquivo do Armazenamento de Blobs do Azure usando a ID.```GET: /datasets/default/files/{id}/content```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando o caminho
Recupera o conteúdo do arquivo do Armazenamento de Blobs do Azure usando o caminho.```GET: /datasets/default/GetFileContentByPath```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho exclusivo para o arquivo no Armazenamento de Blobs do Azure|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter Metadados do Arquivo
Recupera os metadados do arquivo do Armazenamento de Blobs do Azure usando a ID do arquivo.```GET: /datasets/default/files/{id}```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter metadados do arquivo usando o caminho
Recupera os metadados do arquivo do Armazenamento de Blobs do Azure usando o caminho.```GET: /datasets/default/GetFileByPath```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum|Caminho exclusivo para o arquivo no Armazenamento de Blobs do Azure|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Atualizar arquivo
Atualiza um arquivo no Armazenamento de Blobs do Azure.```PUT: /datasets/default/files/{id}```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo a ser atualizado no Armazenamento de Blobs do Azure|
|corpo|string(binary) |sim|corpo|nenhum |Conteúdo do arquivo a ser atualizado no Armazenamento de Blobs do Azure|

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

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0323_2016-->