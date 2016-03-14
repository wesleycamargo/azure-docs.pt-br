<properties
	pageTitle="Adicionar a API do Google Drive ao PowerApps ou aplicativos lógicos | Microsoft Azure"
	description="Visão geral da API do Google Drive com parâmetros da API REST"
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
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Introdução à API do Google Drive
Conecte-se ao Google Drive para criar arquivos, obter linhas e muito mais. A API do Google Drive pode ser usada em:

- PowerApps 
- Aplicativos lógicos 

Com o Google Drive, você pode:

- Crie seu fluxo de negócios baseado nos dados que você obtém da pesquisa. 
- Use ações para pesquisar imagens, notícias e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, você pode pesquisar por um vídeo e usar o Twitter para publicar esse vídeo em um feed do Twitter.
- Adicione a API do Google Drive ao PowerApps Enterprise. Assim, seus usuários poderão usar essa API em seus próprios aplicativos. 

Para obter mais informações sobre como adicionar uma API no PowerApps Enterprise, vá para [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação nos aplicativos lógicos, veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Gatilhos e ações
O Google Drive inclui as ações a seguir. Não há gatilhos.

Gatilhos | Ações
--- | ---
Nenhum | <ul><li>Criar arquivo</li><li>Inserir linha</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Excluir linha</li><li>Extrair o arquivo morto para a pasta</li><li>Obter conteúdo do arquivo usando a ID</li><li>Obter conteúdo do arquivo usando o caminho</li><li>Obter metadados do arquivo usando a ID</li><li>Obter metadados do arquivo usando o caminho</li><li>Obter linha</li><li>Atualizar arquivo</li><li>Atualizar linha</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.


## Criar a conexão com o Google Drive

### Adicionar um configuração adicional no PowerApps
Ao adicionar o Google Drive ao PowerApps Enterprise, insira os valores de **Chave do Aplicativo** e **Segredo do Aplicativo** de seu aplicativo do Google Drive. O valor da **URL de Redirecionamento** também é usado em seu aplicativo do Google. Se você não tiver um aplicativo do Google, poderá usar as seguintes etapas para criar o aplicativo:

1. Entre no [Console de Desenvolvedores do Google][5] e selecione **Criar um projeto vazio**: ![Console de desenvolvedor do google][6]

2. Insira as propriedades do aplicativo e selecione **Criar**.
3. Selecione **Usar APIs do Google**: ![Usar apis do google][8]  
4. Na visão geral, selecione **API do Drive**: ![visualização geral da API do Google Drive][9]  
5. Selecione **Habilitar API**: ![Habilitar API do Google Drive][10]  
6. Após habilitar a API de unidade, selecione **Credenciais**, e selecione **ID do cliente OAuth 2.0**: ![Adicionar credenciais][12]  
7. Selecione **Configurar tela de consentimento**.
8. Na **Tela de consentimento do OAuth**, insira um **Nome do Produto** e selecione **Salvar**: ![Configurar tela de consentimento][13]  
9. Clique na página criar id de cliente:  

	1. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.
	2. Insira um nome para o cliente.
	3. Insira o valor de URL de redirecionamento exibido quando você adicionou a API do Google Drive no Portal do Azure.
	4. Selecione **Criar**.  

	![Criar id de cliente][14]

11. A id do cliente e o segredo do cliente do aplicativo registrado serão exibidos.

Agora copie/cole esses valores de **Chave do Aplicativo** e **Segredo do Aplicativo** em sua configuração da API do Google Drive no portal do Azure.


### Adicionar configuração adicional em aplicativos lógicos
Quando você adiciona essa API aos seus aplicativos lógicos, precisa autorizar que os aplicativos lógicos se conectem ao Google Drive.

1. Entre em sua conta do Google Drive.
2. Permita que seus aplicativos lógicos se conectem e usem o Google Drive. 

Depois de criar a conexão, insira as propriedades do Google Drive, como o caminho da pasta ou o nome do arquivo. A **referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão do Google Drive em outros aplicativos lógicos.


## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Criar arquivo    
Carrega um arquivo no Google Drive.```POST: /datasets/default/files```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|sim|query|nenhum |Caminho da pasta para carregar o arquivo no Google Drive|
|name|string|sim|query|nenhum |Nome do arquivo a ser criado no Google Drive|
|corpo|string(binary) |sim|corpo| nenhum|Conteúdo do arquivo para carregar no Google Drive|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Inserir linha    
Insere uma linha em uma Planilha Google.```POST: /datasets/{dataset}/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path| nenhum|Identificador exclusivo do arquivo de Planilha Google|
|tabela|string|sim|path|nenhum |Identificador exclusivo da planilha|
|item|ItemInternalId: cadeia de caracteres |sim|corpo|nenhum |Linha para inserir na planilha especificada|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Copiar arquivo    
Copia um arquivo no Google Drive.```POST: /datasets/default/copyFile```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query| nenhum|URL para o arquivo de origem|
|destino|string|sim|query|nenhum |Caminho do arquivo de destino no Google Drive, incluindo nome do arquivo de destino|
|substituir|booleano|não|query|nenhum |Substitui o arquivo de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Excluir arquivo    
Exclui um arquivo do Google Drive.```DELETE: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo a ser excluído do Google Drive|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Excluir linha    
Exclui uma linha de uma Planilha Google.```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum |Identificador exclusivo do arquivo de Planilha Google|
|tabela|string|sim|path|nenhum |Identificador exclusivo da planilha|
|ID|string|sim|path|nenhum |Identificador exclusivo da linha a ser excluída|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Extrair o arquivo morto para a pasta    
Extrai um arquivo morto para uma pasta no Google Drive (exemplo: .zip).```POST: /datasets/default/extractFolderV2```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fonte|string|sim|query|nenhum |Caminho para o arquivo morto|
|destino|string|sim|query|nenhum |Caminho no Google Drive para extrair o conteúdo do arquivo morto|
|substituir|booleano|não|query|nenhum |Substitui os arquivos de destino se estiver definido como "true"|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando a ID    
Recupera o conteúdo do arquivo do Google Drive usando a ID.```GET: /datasets/default/files/{id}/content```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo a ser recuperado no Google Drive|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter o conteúdo do arquivo usando o caminho    
Recupera o conteúdo do arquivo no Google Drive usando o caminho.```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho do arquivo no Google Drive|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter metadados de arquivo usando a id    
Recupera os metadados do arquivo do Google Drive usando a ID.```GET: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo no Google Drive|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Obter metadados do arquivo usando o caminho    
Recupera os metadados do arquivo do Google Drive usando o caminho.```GET: /datasets/default/GetFileByPath```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|path|string|sim|query|nenhum |Caminho do arquivo no Google Drive|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter linha    
Recupera uma única linha de uma Planilha Google.```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum |Identificador exclusivo do arquivo de Planilha Google|
|tabela|string|sim|path|nenhum |Identificador exclusivo da planilha|
|ID|string|sim|path| nenhum|O identificador exclusivo da linha a ser recuperado|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Atualizar arquivo    
Atualiza um arquivo no Google Drive.```PUT: /datasets/default/files/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|ID|string|sim|path|nenhum |Identificador exclusivo do arquivo a ser atualizado no Google Drive|
|corpo|string(binary) |sim|corpo| nenhum|Conteúdo do arquivo para carregar no Google Drive|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Atualizar linha    
Atualiza uma linha em uma Planilha Google.```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|dataset|string|sim|path|nenhum |Identificador exclusivo do arquivo de Planilha Google|
|tabela|string|sim|path| nenhum|Identificador exclusivo da planilha|
|ID|string|sim|path|nenhum |Identificador exclusivo da linha a ser atualizada|
|item|ItemInternalId: cadeia de caracteres |sim|corpo|nenhum |Linhas com valores atualizados|

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

#### TableMetadata

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|name|string|não|
|título|string|não|
|x-ms-permission|string|não|
|schema|não definido|não|

#### TablesList

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|

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


## Próximas etapas
Depois de adicionar o Google Drive ao PowerApps Enterprise, [conceda permissões aos usuários](../power-apps/powerapps-manage-api-connection-user-access.md) para que eles usem a API em seus aplicativos.

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/create-api-googledrive/google-developers-console.png
[8]: ./media/create-api-googledrive/use-google-apis.png
[9]: ./media/create-api-googledrive/googledrive-api-overview.png
[10]: ./media/create-api-googledrive/enable-googledrive-api.png
[12]: ./media/create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/create-api-googledrive/configure-consent-screen.png
[14]: ./media/create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0302_2016-->