<properties
	pageTitle="Adicionar a ação HTTP em Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da ação HTTP com propriedades"
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
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# Introdução à ação HTTP

Com a ação HTTP, você pode estender os fluxos de trabalho para a sua organização e se comunicar com qualquer ponto de extremidade por HTTP.

Você pode:

- Crie fluxos de trabalho de aplicativo lógico que são ativados (disparam) quando um site que você gerencia é desativado.
- Comunique-se com qualquer ponto de extremidade por HTTP para estender seus fluxos de trabalho para outros serviços.

Para começar a usar a ação HTTP em um aplicativo lógico, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Usar o gatilho HTTP

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](connectors-overview.md).

Veja uma sequência de exemplo de como configurar um gatilho HTTP no Designer de Aplicativo Lógico.

1. Adicione o gatilho HTTP no seu aplicativo lógico.
2. Preencha os parâmetros do ponto de extremidade HTTP que deseja sondar.
3. Modifique o intervalo de recorrência quanto à frequência que ele deve ser sondado.
4. Agora, o aplicativo lógico é disparado com qualquer conteúdo retornado durante cada verificação.

![Gatilho HTTP](./media/connectors-native-http/using-trigger.png)

### Como o gatilho HTTP funciona

O gatilho HTTP faz uma chamada para um ponto de extremidade HTTP em um intervalo recorrente. Por padrão, qualquer código de resposta HTTP inferior a 300 resultará em uma execução do aplicativo lógico. Você pode adicionar uma condição na exibição de código que será avaliada após a chamada HTTP para determinar se o aplicativo lógico deve ser disparado. Veja um exemplo de um gatilho HTTP que será disparado sempre que o código de status retornado for maior ou igual a `400`.

```javascript
"Http":
{
	"conditions": [
		{
			"expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
		}
	],
	"inputs": {
		"method": "GET",
		"uri": "https://blogs.msdn.microsoft.com/logicapps/",
		"headers": {
			"accept-language": "en"
		}
	},
	"recurrence": {
		"frequency": "Second",
		"interval": 15
	},
	"type": "Http"
}
```

Os detalhes completos sobre os parâmetros de gatilho HTTP estão disponíveis no [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## Usar a ação HTTP

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](connectors-overview.md).

1. Selecione o botão **Nova Etapa**.
2. Escolha **Adicionar uma ação**.
3. Na caixa de pesquisa de ação, digite **http** para listar a ação HTTP.

	![Selecionar a ação HTTP](./media/connectors-native-http/using-action-1.png)

4. Adicione todos os parâmetros necessários para a chamada HTTP.

	![Concluir a ação HTTP](./media/connectors-native-http/using-action-2.png)

5. Clique no canto superior esquerdo da barra de ferramentas para salvar. Seu aplicativo lógico será salvo e publicado (ativado).

## Gatilho HTTP

Veja os detalhes do gatilho com suporte deste conector. O conector HTTP tem um gatilho.

|Gatilho|Descrição|
|---|---|
|HTTP|Faz uma chamada HTTP e retorna o conteúdo da resposta.|

## Ação HTTP

Veja os detalhes da ação com suporte deste conector. O conector HTTP tem uma ação possível.

|Ação|Descrição|
|---|---|
|HTTP|Faz uma chamada HTTP e retorna o conteúdo da resposta.|

## Detalhes do HTTP

As tabelas a seguir descrevem os campos de entrada obrigatórios e opcionais para a ação e os detalhes de saída correspondentes associados ao uso da ação.


#### Solicitação HTTP
Estes são os campos de entrada para a ação, o que cria uma solicitação HTTP de saída. Um * significa que é um campo obrigatório.

|Nome de exibição|Nome da propriedade|Descrição|
|---|---|---|
|Método*|estático|Verbo HTTP a ser usado|
|URI*|uri|O URI para a solicitação HTTP|
|Cabeçalhos|headers|Um objeto JSON de cabeçalhos HTTP a serem incluídos|
|Corpo|body|O corpo da solicitação HTTP|
|Autenticação|autenticação|Os detalhes na seção [Autenticação](#authentication)|
<br>

#### Detalhes de saída

A seguir, os detalhes de saída para a resposta HTTP.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Cabeçalhos|objeto|Cabeçalhos de resposta|
|Corpo|objeto|Objeto de resposta|
|Código de status|int|Código de status HTTP|

## Autenticação

O recurso Aplicativos Lógicos do Serviço de Aplicativo do Azure permite que você use diferentes tipos de autenticação em pontos de extremidade HTTP. Você pode usar essa autenticação com os conectores **HTTP**, **[HTTP + Swagger](./connectors-native-http-swagger.md)** e **[Webhook HTTP](./connectors-native-webhook.md)**. Os seguintes tipos de autenticação são configuráveis:

* [Autenticação básica](#basic-authentication)
* [Autenticação de certificado de cliente](#client-certificate-authentication)
* [Autenticação OAuth do Azure AD (Azure Active Directory)](#azure-active-directory-oauth-authentication)

#### Autenticação básica

O seguinte objeto de autenticação é necessário para a autenticação básica. Um * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Type*|type|Tipo de autenticação (deve ser `Basic` para a autenticação básica)|
|Username*|Nome de Usuário|Nome de usuário para autenticar|
|Password*|Senha|Senha para autenticação|

>[AZURE.TIP] Se quiser usar uma senha que não possa ser recuperada da definição, use um parâmetro `securestring` e a [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs) `@parameters()`.

Desse modo, você cria um objeto como este no campo de autenticação:

```javascript
{
	"type": "Basic",
	"username": "user",
	"password": "test"
}
```

#### Autenticação de certificado de cliente

O seguinte objeto de autenticação é necessário para a autenticação de certificado de cliente. Um * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Type*|type|O tipo de autenticação (deve ser `ClientCertificate` para certificados de cliente SSL)|
|PFX*|pfx|O conteúdo codificado na Base64 do arquivo Personal Information Exchange (PFX)|
|Password*|Senha|A senha para acessar o arquivo PFX|

>[AZURE.TIP] Você pode usar um parâmetro `securestring` e a [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs) `@parameters()` para usar um parâmetro que não poderá ser lido na definição depois de salvar o aplicativo lógico.

Por exemplo:

```javascript
{
	"type": "ClientCertificate",
	"pfx": "aGVsbG8g...d29ybGQ=",
	"password": "@parameters('myPassword')"
}
```

#### Autenticação OAuth do Azure AD

O seguinte objeto de autenticação é necessário para a autenticação OAuth do Azure AD. Um * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Type*|type|O tipo de autenticação (deve ser `ActiveDirectoryOAuth` para a autenticação OAuth do Azure AD)|
|Tenant*|locatário|O identificador do locatário para o locatário do Azure AD|
|Audience*|audiência|Definida como `https://management.core.windows.net/`|
|Client ID*|clientId|O identificador de cliente para o aplicativo do Azure AD|
|Secret*|segredo|O segredo do cliente que está solicitando o token|

>[AZURE.TIP] Você pode usar um parâmetro `securestring` e a [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs) `@parameters()` para usar um parâmetro que não poderá ser lido na definição depois de salvo.

Por exemplo:

```javascript
{
	"type": "ActiveDirectoryOAuth",
	"tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
	"audience": "https://management.core.windows.net/",
	"clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
	"secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## Próximas etapas

Agora, experimente a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md). Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->