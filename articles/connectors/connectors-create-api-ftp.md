<properties
	pageTitle="Adicionar a API do FTP aos seus Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da API do FTP com os parâmetros da API REST"
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
   ms.date="05/16/2016"
   ms.author="mandia"/>

# Introdução à API do FTP
Conecte-se a um servidor FTP para gerenciar seus arquivos, incluindo carregar, excluir arquivos e muito mais. A API do FTP pode ser usada em:

- Aplicativos lógicos (discutidos neste tópico)
- PowerApps (consulte a [lista de conexões de PowerApps](https://powerapps.microsoft.com/tutorials/connections-list/) para obter uma lista completa)

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

Com o FTP, você pode:

- Compile seu fluxo de negócios baseado nos dados obtidos do FTP. 
- Use um gatilho quando um arquivo for atualizado.
- Use ações para criar arquivos, obter o conteúdo do arquivo e muito mais. Essas ações obtém uma resposta e disponibilizam a saída para outras ações. Por exemplo, você pode obter o conteúdo de um arquivo e então atualizar um banco de dados SQL. 

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Gatilhos e ações
O FTP tem os seguintes gatilhos e ações disponíveis.

Gatilhos | Ações
--- | ---
<ul><li>Obtém um arquivo atualizado</li></ul> | <ul><li>Criar arquivo</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Extrair pasta</li><li>Obter conteúdo do arquivo</li><li>Obter conteúdo do arquivo usando o caminho</li><li>Obter metadados de arquivo</li><li>Obter metadados de arquivo usando o caminho</li><li>Obtém um arquivo atualizado</li><li>Atualizar arquivo</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o FTP
Ao adicionar essa API aos seus aplicativos lógicos, insira os seguintes valores:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Endereço do servidor| Sim | Insira o FQDN (domínio totalmente qualificado) ou o endereço IP do servidor FTP.|
|Nome de usuário| Sim | Insira o nome de usuário para se conectar ao Servidor FTP.|
|Senha | Sim | Insira a senha do nome de usuário.|

Depois de criar a conexão, insira as propriedades do FTP, como o arquivo de origem ou a pasta de destino. A **Referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão do FTP em outros aplicativos lógicos.

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Criar arquivo
Carrega um arquivo no servidor FTP.```POST: /datasets/default/files```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|sim|query|nenhum |Caminho da pasta para carregar o arquivo para o servidor FTP|
|name|string|sim|query| nenhum|Nome do arquivo a ser criado no servidor FTP|
|corpo| |sim|corpo|nenhum |Conteúdo do arquivo para carregar para o servidor FTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

### Copiar arquivo
Copia um arquivo no servidor FTP.```POST: /datasets/default/copyFile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum |URL para o arquivo de origem|
|destino|string|sim|query|nenhum |Caminho do arquivo de destino no servidor FTP, incluindo nome do arquivo de destino|
|substituir|booleano|não|query|nenhum |Substitui o arquivo de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

### Excluir arquivo 
Exclui um arquivo do servidor FTP.```DELETE: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo a ser excluído do servidor FTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|

### Extrair pasta
Extrai um arquivo morto para uma pasta no servidor FTP (exemplo: .zip).```POST: /datasets/default/extractFolderV2```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query| nenhum|Caminho para o arquivo morto|
|destino|string|sim|query| nenhum|Caminho para a pasta de destino|
|substituir|booleano|não|query|nenhum|Substitui os arquivos de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|

### Obter o conteúdo do arquivo
Recupera o conteúdo do arquivo do Servidor FTP usando a ID.```GET: /datasets/default/files/{id}/content```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando o caminho
Recupera o conteúdo do arquivo do servidor FTP usando o caminho.```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho exclusivo para o arquivo no servidor FTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter Metadados de Arquivo 
Recupera os metadados do arquivo do servidor FTP usando a ID do arquivo.```GET: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo|

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 | OK | 
| padrão | Falha na Operação.


### Obter Metadados de Arquivo usando o caminho
Recupera os metadados do arquivo do servidor FTP usando o caminho.```GET: /datasets/default/GetFileByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query| nenhum|Caminho exclusivo para o arquivo no servidor FTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obtém um arquivo atualizado
Obtém um arquivo atualizado. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|string|sim|query|nenhum |ID da pasta na qual buscar um arquivo atualizado|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Atualizar arquivo 
Atualiza um arquivo no servidor FTP. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path| nenhum|Identificador exclusivo do arquivo a ser atualizado no servidor FTP|
|corpo| |sim|corpo|nenhum |Conteúdo do arquivo para atualizar no servidor FTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## Definições de objeto

#### DataSetsMetadata

| Nome | Tipo de Dados | Obrigatório |
|---|---|---|
|tabular|não definido|não|
|blob|não definido|não|

#### TabularDataSetsMetadata

| Nome | Tipo de Dados | Obrigatório |
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|
|tableDisplayName|string|não|
|tablePluralName|string|não|

#### BlobDataSetsMetadata

| Nome | Tipo de Dados | Obrigatório |
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|

#### BlobMetadata

| Nome | Tipo de Dados | Obrigatório |
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

<!---HONumber=AcomDC_0518_2016-->