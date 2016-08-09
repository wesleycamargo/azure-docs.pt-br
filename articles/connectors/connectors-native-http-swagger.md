<properties
	pageTitle="Adicionar a ação de HTTP + Swagger em Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral das operações e da ação de HTTP + Swagger"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introdução à ação de HTTP + Swagger

Com a ação de HTTP + Swagger, você pode criar um conector de primeira classe para qualquer ponto de extremidade REST por meio de um [documento Swagger](https://swagger.io).

- Estender um aplicativo lógico para chamar qualquer ponto de extremidade REST com uma experiência de designer de primeira classe

Para começar a usar a ação HTTP + Swagger em um aplicativo lógico, confira [criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usar HTTP + Swagger como um gatilho ou uma ação

As funções de gatilho e ação de HTTP + Swagger funcionam da mesma forma que a [ação de HTTP](connectors-native-http.md), porém, proporcionam uma melhor experiência de design, mostrando a forma da API e as saídas no designer dos [metadados swagger](https://swagger.io). Além disso, você pode usar HTTP + Swagger como um gatilho e, se quiser implementar um gatilho de sondagem, deve seguir o padrão de sondagem [descrito neste documento](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).
	
[Saiba mais sobre gatilhos e ações de Aplicativo Lógico.](connectors-overview.md)

Veja um exemplo de como usar a operação HTTP + Swagger como uma ação em um fluxo de trabalho.

1. Escolha o botão **Nova Etapa**
1. Escolha **Adicionar uma ação**
1. Na caixa de pesquisa de ação, digite "swagger" para listar a ação de HTTP + Swagger

	![Selecionar ação de HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)

1. Insira a URL para um documento do swagger
	- A URL deve ser um ponto de extremidade HTTPS e ter CORS habilitado para trabalhar no designer. Se o documento do swagger cumprir esses pré-requisitos, você poderá usar [o armazenamento do Azure com o CORS habilitado](#hosting-swagger-from-storage) para armazenar o documento.
1. Clique em Avançar para leitura e renderização do documento do swagger
1. Adicione todos os parâmetros necessários para a chamada HTTP

	![Concluir a ação HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Clique em Salvar no canto superior esquerdo da barra de ferramentas e seu aplicativo lógico será salvo e publicado (ativado)

### Hospedando o Swagger no armazenamento

Você pode querer fazer referência a um documento do swagger que não esteja hospedado ou que não atenda aos requisitos de segurança entre origens necessários para uso no designer. Uma solução alternativa é armazenar o documento do swagger no armazenamento do Azure e habilitar o CORS para fazer referência ao documento. Veja as etapas para criar, configurar e armazenar o swagger no armazenamento do Azure:

1. [Crie uma conta de armazenamento do Azure com o armazenamento de blobs](../storage/storage-create-storage-account.md): defina as permissões para "Acesso Público"
1. Habilitar o CORS no Blob
	- Você pode usar [este script do PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) para definir a configuração automaticamente.
1. Carregue o arquivo do swagger nos Blobs do Azure
	- Você pode fazer isso no [Portal do Azure](https://portal.azure.com) ou em uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/)
1. Faça referência a um link HTTPS para o documento nos Blobs do Azure (segue o formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`)

---

## Detalhes técnicos

Veja abaixo os detalhes dos gatilhos e as ações que esse conector permite.

## Gatilhos de HTTP + Swagger

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos.](connectors-overview.md) O conector de HTTP + Swagger tem 1 gatilho.

|Gatilho|Descrição|
|---|---|
|HTTP + Swagger|Faz uma chamada HTTP e retorna o conteúdo da resposta|

## Ações de HTTP + Swagger

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md) O conector HTTP + Swagger tem uma ação possível.

|Ação|Descrição|
|---|---|
|HTTP + Swagger|Faz uma chamada HTTP e retorna o conteúdo da resposta|

### Detalhes da ação

O conector HTTP + Swagger apresenta uma ação possível. Veja abaixo as informações sobre cada uma das ações, seus campos de entrada obrigatórios e opcionais, bem como os detalhes da saída correspondente associada ao uso delas.

#### HTTP + Swagger

Faça uma solicitação de saída HTTP com a assistência dos metadados do swagger. Um * significa que o campo é obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Método*|estático|Verbo HTTP a ser usado|
|URI*|uri|URI para a solicitação HTTP|
|Cabeçalhos|headers|Um objeto JSON de cabeçalhos HTTP a serem incluídos|
|Corpo|corpo|O corpo da solicitação HTTP|
|Autenticação|autenticação|Autenticação a ser usada para solicitação – [veja HTTP para obter detalhes](./connectors-native-http.md#authentication)|
<br>

**Detalhes da saída**

Resposta HTTP

|Nome da Propriedade|Tipo de Dados|Descrição|
|---|---|---|
|Cabeçalhos|objeto|Cabeçalhos de resposta|
|Corpo|objeto|Objeto de resposta|
|Código de status|int|Código de status HTTP|

### Respostas HTTP

Ao fazer chamadas a várias ações, você pode obter determinadas respostas. Veja abaixo uma tabela que descreve respostas e descrições correspondentes.

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|

---

## Próximas etapas

Veja abaixo os detalhes sobre como seguir adiante com os aplicativos lógicos e nossa comunidade.

## Criar um aplicativo lógico

Teste a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) agora mesmo. Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->