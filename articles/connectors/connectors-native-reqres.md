<properties
	pageTitle="Usar ações de solicitação e resposta | Microsoft Azure"
	description="Pelo gatilho e ação de solicitação e resposta em um Aplicativo Lógico do Azure"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introdução aos componentes de solicitação e resposta

Com os componentes de solicitação e resposta em um aplicativo lógico, você pode responder em tempo real a eventos como:

- Responder a uma solicitação HTTP com dados de um banco de dados local por meio de um aplicativo lógico.
- Dispare um aplicativo lógico de um evento de webhook externo.
- Chamar um aplicativo lógico com uma ação de solicitação e resposta de dentro de outro aplicativo lógico

Para começar a usar as ações de solicitação e resposta em um aplicativo lógico, confira [criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usar o gatilho de Solicitação HTTP

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](connectors-overview.md).

Veja uma sequência de exemplo de como configurar uma solicitação HTTP no designer de aplicativo lógico.

1. Adicione o gatilho **Solicitação — quando uma solicitação HTTP é recebida** no seu aplicativo lógico
	- Como opção, você pode fornecer um esquema JSON (usando uma ferramenta como [jsonschema.net](http://jsonschema.net)) para o corpo da solicitação. Isso permitirá que o designer gere tokens para propriedades na Solicitação HTTP.
1. Adicione outra ação para que seja possível salvar o aplicativo lógico
1. Depois de salvar, você pode obter a URL da Solicitação HTTP do cartão de solicitação
1. Um HTTP POST (você pode usar uma ferramenta como [Postman](https://www.getpostman.com/)) para a URL que vai disparar o aplicativo lógico

>[AZURE.NOTE] Se você não definir uma ação de resposta, uma resposta `202 ACCEPTED` será retornada imediatamente ao chamador. Você pode usar a ação de resposta para personalizar uma resposta.

![Gatilho de resposta](./media/connectors-native-reqres/using-trigger.png)

## Usar a ação de Resposta HTTP
	
A ação de Resposta HTTP é válida somente quando usada em um fluxo de trabalho disparado por uma Solicitação HTTP. Se você não definir uma ação de resposta, uma resposta `202 ACCEPTED` será retornada imediatamente ao chamador. Uma ação de resposta pode ser adicionada em qualquer etapa do fluxo de trabalho. O aplicativo lógico manterá a solicitação de entrada aberta apenas por 1 minuto para uma resposta. Após 1 minuto, se nenhuma resposta for enviada do fluxo de trabalho (e existir uma ação de resposta na definição) um `504 GATEWAY TIMEOUT` será retornado ao chamador. Veja como adicionar uma ação de resposta HTTP:

1. Selecione o botão **Nova Etapa**
1. Escolha **Adicionar uma ação**
1. Na caixa de pesquisa de ação, digite "Resposta" para listar a ação de resposta

	![Escolher ação de resposta](./media/connectors-native-reqres/using-action-1.png)

1. Adicione todos os parâmetros necessários para a mensagem de resposta HTTP

	![Concluir ação de resposta](./media/connectors-native-reqres/using-action-2.png)

1. Clique em Salvar no canto superior esquerdo da barra de ferramentas e seu aplicativo lógico será salvo e publicado (ativado)

---

## Detalhes técnicos

Veja abaixo os detalhes dos gatilhos e as ações que esse conector permite.

## Gatilho de solicitação

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos.](connectors-overview.md) Há um único gatilho de solicitação.

|Gatilho|Descrição|
|---|---|
|Solicitação|Quando uma solicitação HTTP é recebida|

## Ação de resposta

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md) Há uma única ação de resposta que pode ser usada apenas quando acompanhada por um gatilho de solicitação.

|Ação|Descrição|
|---|---|
|Resposta|Retornar uma resposta à solicitação HTTP correlacionada|

### Detalhes da ação

Veja abaixo as informações sobre cada uma das ações, seus campos de entrada obrigatórios e opcionais, bem como os detalhes da saída correspondente associada ao uso delas.

#### Gatilho de solicitação
Gatilho de uma solicitação HTTP de entrada. Um * significa campo obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Esquema JSON|schema|O esquema JSON do corpo da solicitação HTTP|
<br>

**Detalhes da saída**

Solicitação

|Nome da Propriedade|Tipo de Dados|Descrição|
|---|---|---|
|Cabeçalhos|objeto|Cabeçalhos da solicitação|
|Corpo|objeto|Objeto da solicitação|

#### Ação de resposta

Resposta HTTP. Um * significa um campo obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Código de status*|statusCode|O código de status HTTP|
|Cabeçalhos|headers|Um objeto JSON de cabeçalhos de resposta a serem incluídos|
|Corpo|corpo|O corpo da resposta|

## Próximas etapas

Veja abaixo os detalhes sobre como seguir adiante com os aplicativos lógicos e nossa comunidade.

## Criar um aplicativo lógico

Teste a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) agora mesmo. Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->