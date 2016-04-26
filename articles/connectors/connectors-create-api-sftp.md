<properties
	pageTitle="Adicionar a API do SFTP aos seus Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da API do SFTP com os parâmetros da API REST"
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
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Introdução à API do SFTP
Conecte-se a um servidor SFTP para gerenciar seus arquivos. Você pode realizar tarefas diferentes no servidor do SFTP, como carregar arquivos, excluí-los e muito mais. A API do SFTP pode ser usada em:

- Aplicativos lógicos

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

Com o SFTP, você pode:

- Criar seu fluxo de negócios com base nos dados obtidos do SFTP. 
- Usar um gatilho quando um arquivo for atualizado.
- Usar ações para criar arquivos, excluí-los e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, você pode obter o conteúdo de um arquivo e então atualizar um banco de dados SQL. 

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Gatilhos e ações
A API do SFTP tem os gatilhos e as ações disponíveis a seguir.

Gatilhos | Ações
--- | ---
<ul><li>Quando um arquivo é criado ou modificado </li></ul> | <ul><li>Criar arquivo</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Extrair pasta</li><li>Obter conteúdo do arquivo</li><li>Obter o conteúdo do arquivo usando o caminho</li><li>Obter metadados do arquivo</li><li>Obter metadados do arquivo usando o caminho</li><li>Atualizar arquivo</li><li>Quando um arquivo é criado ou modificado </li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.


## Criar uma conexão com o SFTP
Ao adicionar essa API aos seus aplicativos lógicos, insira os seguintes valores:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Endereço do Servidor Host| Sim | Insira o FQDN (domínio totalmente qualificado) ou o endereço IP do servidor SFTP.|
|Nome de usuário| Sim | Insira o nome de usuário para se conectar ao Servidor SFTP.|
|Senha | Sim | Insira a senha do nome de usuário.|
|Impressão Digital da Chave de Host do Servidor SSH | Sim | Insira a impressão digital da chave pública de host para o servidor SSH. <br/><br/>Normalmente, o administrador do servidor pode dar a você essa chave. Você também pode usar as ferramentas ```WinSCP``` ou ```ssh-keygen-g3 -F``` para obter a impressão digital da chave. | 

Depois de criar a conexão, insira as propriedades do SFTP, como o caminho da pasta ou o arquivo. A **referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão do SFTP em outros aplicativos lógicos.


## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Criar arquivo
Carrega um arquivo no SFTP.```POST: /datasets/default/files```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|sim|query|nenhum |Caminho exclusivo da pasta no SFTP|
|name|string|sim|query| nenhum|Nome do arquivo|
|corpo|string(binary) |sim|corpo|nenhum |Conteúdo do arquivo a ser criado no SFTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

### Copiar arquivo
Copia um arquivo no SFTP.```POST: /datasets/default/copyFile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query| nenhum|Caminho para o arquivo de origem|
|destino|string|sim|query|nenhum |Caminho para o arquivo de destino, incluindo o nome do arquivo|
|substituir|booleano|não|query|nenhum|Substitui o arquivo de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|

### Excluir arquivo 
Exclui um arquivo no SFTP.```DELETE: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo no SFTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|

### Extrair pasta
Extrai um arquivo morto para uma pasta usando o SFTP (exemplo: .zip).```POST: /datasets/default/extractFolderV2```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum |Caminho para o arquivo morto|
|destino|string|sim|query|nenhum |Caminho para a pasta de destino|
|substituir|booleano|não|query|nenhum|Substitui os arquivos de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|

### Obter conteúdo do arquivo
Recupera o conteúdo do arquivo do SFTP usando a ID.```GET: /datasets/default/files/{id}/content```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo no SFTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando o caminho
Recupera o conteúdo do arquivo do SFTP usando o caminho.```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query| nenhum|Caminho exclusivo do arquivo SFTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter Metadados do Arquivo 
Recupera os metadados do arquivo do SFTP usando a ID do arquivo.```GET: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path| nenhum|Identificador exclusivo do arquivo no SFTP|

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 | OK | 
| padrão | Falha na Operação.


### Obter Metadados do Arquivo usando o caminho
Recupera os metadados do arquivo do SFTP usando o caminho.```GET: /datasets/default/GetFileByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho exclusivo do arquivo SFTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Atualizar arquivo
Atualiza o conteúdo do arquivo usando o SFTP.```PUT: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo no SFTP|
|corpo|string(binary) |sim|corpo| nenhum|Conteúdo do arquivo para atualizar no SFTP|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Quando um arquivo é criado ou modificado 
Dispara um fluxo quando um arquivo é modificado no SFTP.```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|string|sim|query|nenhum |Identificador exclusivo da pasta|

#### Resposta
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

#### BlobMetadata

| Nome | Tipo de Dados | Obrigatório|
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

<!---HONumber=AcomDC_0413_2016-->