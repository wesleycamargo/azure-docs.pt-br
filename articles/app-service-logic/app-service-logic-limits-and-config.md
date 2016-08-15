<properties
	pageTitle="Limites e configuração do Aplicativo Lógico | Microsoft Azure"
	description="Visão geral dos limites de serviço e dos valores de configuração disponíveis para Aplicativos Lógicos."
	services="logic-apps"
	documentationCenter=".net,nodejs,java"
	authors="jeffhollan"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="jehollan"/>

# Limites e configuração do Aplicativo Lógico

Veja a seguir as informações sobre os limites atuais e detalhes de configuração para Aplicativos Lógicos do Azure.

## Limites

### Limites de solicitação HTTP

Esses são os limites para uma única solicitação e/ou chamada de conector HTTP

#### Tempo limite

|Nome|Limite|Observações|
|----|----|----|
|Tempo Limite da Solicitação|1 minuto|Um [padrão assíncrono](app-service-logic-create-api-app.md) ou [loop until](app-service-logic-loops-and-scopes.md) pode compensar, conforme necessário|

#### Tamanho da mensagem

|Nome|Limite|Observações|
|----|----|----|
|Tamanho da mensagem|50 MB|Talvez, alguns conectores e APIs não ofereçam suporte a 50 MB. O gatilho de solicitação oferece suporte a até 25 MB|
|Limite de avaliação da expressão|131\.072 caracteres|`@concat()`, `@base64()`, `string` não pode ser maior do que isso|

#### Política de repetição

|Nome|Limite|Observações|
|----|----|----|
|Tentativas de repetição|4|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/pt-BR/library/azure/mt643939.aspx)|
|Atraso máximo de nova tentativa|1 hora|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/pt-BR/library/azure/mt643939.aspx)|
|Atraso mínimo de nova tentativa|20 min.|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/pt-BR/library/azure/mt643939.aspx)|

### Retenção e duração da execução

Estes são os limites de execução de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Duração da execução|90 dias||
|Retenção de armazenamento|90 dias|A partir da hora de início de execução|
|Intervalo de recorrência mín.|15 s||
|Intervalo de recorrência máx.|500 dias||


### Limites de loop e debatching

Estes são os limites de execução de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Itens ForEach|10\.000|Você pode usar a [ação de consulta](../connectors/connectors-native-query.md) para filtrar matrizes maiores, conforme o necessário|
|Iterações Until|10\.000||
|Itens SplitOn|10\.000||
|Paralelismo de ForEach|20|Você pode definir um foreach sequencial adicionando `"operationOptions": "Sequential"` à ação `foreach`|


### Limites de taxa de transferência

Estes são os limites para uma instância única de aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Gatilhos por segundo|100|Pode distribuir fluxos de trabalho entre vários aplicativos conforme o necessário|

### Limites de definição

Estes são os limites de definição de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Ações no ForEach|1|Você pode adicionar fluxos de trabalho aninhados para estendê-lo conforme necessário|
|Ações por fluxo de trabalho|60|Você pode adicionar fluxos de trabalho aninhados para estendê-lo conforme necessário|
|Profundidade de aninhamento de ação permitida|5|Você pode adicionar fluxos de trabalho aninhados para estendê-lo conforme necessário|
|Fluxos por região e assinatura|1000||
|Gatilhos por fluxo de trabalho|10||
|Máximo de caracteres por expressão|8\.192||
|Tamanho máximo de `trackedProperties` em caracteres|16\.000|
|Limite de nome de `action`/`trigger`|80||
|Limite de tamanho de `description`|256||
|Limite de `parameters`|50||
|Limite de `outputs`|10||

## Configuração

### Endereço IP

Chamadas feitas a partir de um [conector](../connectors/apis-list.md) virão do endereço IP especificado abaixo.

Chamadas feitas diretamente de um aplicativo lógico (isto é, por meio de [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) podem vir de qualquer [Intervalo de IP de Datacenter do Azure](https://www.microsoft.com/pt-BR/download/details.aspx?id=41653).

|Região do aplicativo Lógico|IP de Saída|
|-----|----|
|Leste da Austrália|40\.126.251.213|
|Sudeste da Austrália|40\.127.80.34|
|Sul do Brasil|191\.232.38.129|
|Índia Central|104\.211.98.164|
|Centro dos EUA|40\.122.49.51|
|Ásia Oriental|23\.99.116.181|
|Leste dos EUA|191\.237.41.52|
|Leste dos EUA 2|104\.208.233.100|
|Leste do Japão|40\.115.186.96|
|Oeste do Japão|40\.74.130.77|
|Centro-Norte dos EUA|65\.52.218.230|
|Norte da Europa|104\.45.93.9|
|Centro-Sul dos Estados Unidos|104\.214.70.191|
|Sudeste Asiático|13\.76.231.68|
|Sul da Índia|104\.211.227.225|
|Europa Ocidental|40\.115.50.13|
|Índia Ocidental|104\.211.161.203|
|Oeste dos EUA|104\.40.51.248|


## Próximas etapas  

- Para começar com aplicativos lógicos, siga o tutorial [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md).
- [Veja exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
- [Você pode automatizar processos de negócios com aplicativos lógicos](http://channel9.msdn.com/Events/Build/2016/T694)
- [Aprenda a integrar seus sistemas com aplicativos lógicos](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0803_2016-->