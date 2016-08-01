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
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# Introdução à ação HTTP

Com a ação HTTP, você pode estender os fluxos de trabalho para a sua organização e se comunicar com qualquer ponto de extremidade por HTTP.

- Crie fluxos de trabalho de aplicativo lógico que são ativados (disparam) quando um site que você gerencia é desativado.
- Comunique-se com qualquer ponto de extremidade por HTTP para estender seus fluxos de trabalho para outros serviços.

Para começar a usar a ação HTTP em um aplicativo lógico, confira [criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usar um gatilho HTTP

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](connectors-overview.md).

Veja uma sequência de exemplo de como configurar um gatilho HTTP no designer de aplicativo lógico.

1. Adicione o gatilho HTTP no seu aplicativo lógico
1. Preencha os parâmetros do ponto de extremidade HTTP que deseja sondar
1. Modifique o intervalo de recorrência quanto à frequência que ele deve ser sondado
1. Agora, o aplicativo lógico será disparado com qualquer conteúdo retornado durante cada verificação.

![Gatilho de HTTP](./media/connectors-native-http/using-trigger.png)

### Como o gatilho HTTP funciona

O gatilho HTTP fará uma chamada para um ponto de extremidade HTTP em um intervalo recorrente. Por padrão, qualquer código de resposta HTTP inferior a 300 resultará em uma execução do aplicativo lógico. Você pode adicionar uma condição na exibição de código que será avaliada após a chamada HTTP para determinar se o aplicativo lógico deve ser disparado. Veja um exemplo de um gatilho HTTP que será disparado sempre que o código de status retornado for maior que ou igual `400`.

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

Os detalhes completos sobre os parâmetros do gatilho HTTP [podem ser encontrados no MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## Usar uma ação HTTP
	
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md)

1. Selecione o botão **Nova Etapa**
1. Escolha **Adicionar uma ação**
1. Na caixa de pesquisa de ação, digite "HTTP" para listar a ação HTTP

	![Selecionar ação HTTP](./media/connectors-native-http/using-action-1.png)

1. Adicione todos os parâmetros necessários para a chamada HTTP

	![Concluir a ação HTTP](./media/connectors-native-http/using-action-2.png)

1. Clique em Salvar no canto superior esquerdo da barra de ferramentas e seu aplicativo lógico será salvo e publicado (ativado)

---

## Detalhes técnicos

Veja abaixo os detalhes dos gatilhos e as ações que esse conector permite.

## Gatilhos HTTP

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos.](connectors-overview.md) O conector HTTP tem 1 gatilho.

|Gatilho|Descrição|
|---|---|
|HTTP|Faz uma chamada HTTP e retorna o conteúdo da resposta|

## Ações HTTP

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações.](connectors-overview.md) O conector HTTP tem 1 ação possível.

|Ação|Descrição|
|---|---|
|HTTP|Faz uma chamada HTTP e retorna o conteúdo da resposta|

### Detalhes da ação

O conector HTTP vem acompanhado de 1 ação possível. Veja abaixo as informações sobre cada uma das ações, seus campos de entrada obrigatórios e opcionais, bem como os detalhes da saída correspondente associada ao uso delas.

#### Solicitação HTTP
Faça uma solicitação de saída HTTP. Um * significa que o campo é obrigatório.

|Nome de exibição|Nome da Propriedade|Descrição|
|---|---|---|
|Método*|estático|Verbo HTTP a ser usado|
|URI*|uri|URI para a solicitação HTTP|
|Cabeçalhos|headers|Um objeto JSON de cabeçalhos HTTP a serem incluídos|
|Corpo|corpo|O corpo da solicitação HTTP|
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
|padrão|Falha na Operação.|

---

## Próximas etapas

Veja abaixo os detalhes sobre como seguir adiante com os aplicativos lógicos e nossa comunidade.

## Criar um aplicativo lógico

Teste a plataforma e [crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) agora mesmo. Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->