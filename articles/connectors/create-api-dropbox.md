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
   ms.date="02/11/2016"
   ms.author="mandia"/>

# Introdução à API do Dropbox 
Conecte-se ao Dropbox para gerenciar arquivos, por exemplo, criar e obter arquivos, e muito mais.

A API do Dropbox pode ser usada em aplicativos PowerApps Enterprise e em aplicativos lógicos.

Com o Dropbox, você pode:

- Criar seu fluxo de negócios baseado nos dados que obtém do Dropbox. 
- Usar gatilhos para quando um arquivo for criado ou atualizado.
- Usar ações para criar um arquivo, excluir um arquivo e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um novo arquivo é criado no Dropbox, você pode enviar esse arquivo por email usando o Office 365.
- Adicione a API do Dropbox ao PowerApps Enterprise. Assim, seus usuários poderão usar essa API em seus próprios aplicativos. 

Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O Dropbox inclui os gatilhos e as ações a seguir.

Gatilhos | Ações
--- | ---
<ul><li>Quando um arquivo é criado</li><li>Quando um arquivo é modificado</li></ul> | <ul><li>Criar arquivo</li><li>Quando um arquivo é criado</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Extrair o arquivo para a pasta</li><li>Obter conteúdo do arquivo usando id</li><li>Obter arquivo usando caminho</li><li>Obter metadados do arquivo usando a id</li><li>Obter metadados de arquivo usando o caminho</li><li>Atualizar arquivo</li><li>Quando um arquivo é modificado</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar a conexão com o Dropbox

### Adicionar um configuração adicional no PowerApps
Quando você adiciona o Dropbox ao PowerApps Enterprise, você insere os valores de **Chave do Aplicativo** e **Segredo do Aplicativo** de seu aplicativo Dropbox. O valor **URL de redirecionamento** também é usado em seu aplicativo do Dropbox. Se você não tiver um aplicativo do Dropbox, poderá usar as seguintes etapas para criar o aplicativo:

1. Entre no [Dropbox][1].
2. Vá para o site do desenvolvedor do Dropbox e selecione **Meus aplicativos**: ![Site de desenvolvedor do Dropbox][8]  
3. Selecione **Criar**: ![aplicativo de criação de Dropbox][9]  
4. Em **Criar um novo aplicativo na plataforma Dropbox**:  

	1. Em **Escolher API**, escolha **API do Dropbox**.
	2. Em **Escolha o tipo de acesso necessário**, escolha **Dropbox Completo...**.  
	3. Digite um nome para seu aplicativo  

	![aplicativo de criação de Dropbox página 1][10]

5. Na página de configurações do aplicativo:

	1. Em **OAuth 2**, insira o valor **URL de Redirecionamento** mostrado quando você adiciona a API do Dropbox ao Portal do Azure. Selecione **Adicionar**.  
	2. Selecione o link **Mostrar** para revelar a **chave secreta do aplicativo**:  

	![aplicativo de criação de Dropbox página 2][11]

Agora, copie/cole os valores de **Chave do Aplicativo** e **Segredo do Aplicativo** em sua configuração do Dropbox no Portal do Azure.

### Adicionar configuração adicional em aplicativos lógicos
Quando você adiciona essa API a seus aplicativos lógicos, precisa autorizar os aplicativos lógicos a se conectarem ao Dropbox.

1. Entre na sua conta do Dropbox.
2. Selecione **Autorizar** e permita que seus aplicativos lógicos se conectem e usem o Dropbox. 

Depois de criar a conexão, insira as propriedades do Dropbox, como o caminho da pasta ou o nome do arquivo. A **Referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão do Dropbox em outros aplicativos lógicos.

## Referência da API REST do Swagger

### Criar arquivo    
Carrega um arquivo no Dropbox. ```POST: /datasets/default/files```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|sim|query|nenhum |Caminho da pasta para carregar o arquivo no Dropbox|
|name|string|sim|query|nenhum |Nome do arquivo a ser criado no Dropbox|
|body|string(binary) |sim|body|nenhum |Conteúdo do arquivo para carregar no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Quando um arquivo é criado    
Dispara um fluxo quando um novo arquivo é criado em uma pasta do Dropbox. ```GET: /datasets/default/triggers/onnewfile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderId|string|sim|query|nenhum |Identificador exclusivo da pasta no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Copiar arquivo    
Copia um arquivo no Dropbox. ```POST: /datasets/default/copyFile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum |URL para o arquivo de origem|
|destino|string|sim|query| nenhum|Caminho do arquivo de destino no Dropbox, incluindo nome do arquivo de destino|
|overwrite|booleano|não|query|nenhum |Substitui o arquivo de destino se estiver definido como “true”|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Excluir arquivo    
Exclui um arquivo do Dropbox. ```DELETE: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum|Identificador exclusivo do arquivo a ser excluído do Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Extrair o arquivo morto para a pasta    
Extrai um arquivo morto em uma pasta na pasta no Dropbox (exemplo: .zip). **```POST: /datasets/default/extractFolderV2```**

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum |Caminho para o arquivo morto|
|destino|string|sim|query|nenhum |Caminho no Dropbox para extrair o conteúdo do arquivo morto|
|overwrite|booleano|não|query|nenhum |Substitui os arquivos de destino se estiver definido como ‘true’|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando a ID    
Recupera o conteúdo do arquivo do Dropbox usando a id. ```GET: /datasets/default/files/{id}/content```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando o caminho    
Recupera o conteúdo do arquivo do Dropbox usando um caminho. ```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho exclusivo para o arquivo no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter metadados de arquivo usando a id    
Recupera os metadados do arquivo do Dropbox usando uma id do arquivo. ```GET: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter metadados de arquivo usando o caminho    
Recupera os metadados do arquivo do Dropbox usando um caminho. ```GET: /datasets/default/GetFileByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho exclusivo para o arquivo no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Atualizar arquivo    
Atualiza um arquivo no Dropbox. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path| nenhum|Identificador exclusivo do arquivo a ser atualizado no Dropbox|
|body|string(binary) |sim|body|nenhum |Conteúdo do arquivo a ser atualizado no Dropbox|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Quando um arquivo é modificado    
Dispara um fluxo quando um arquivo é modificado em uma pasta do Dropbox. ```GET: /datasets/default/triggers/onupdatedfile```

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
Depois de adicionar a API do Dropbox para PowerApps Enterprise, [dê aos usuários permissões](../power-apps/powerapps-manage-api-connection-user-access.md) usar a API em seus aplicativos.

[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0224_2016-->