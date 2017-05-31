---
title: "Limites e configuração do Aplicativo Lógico | Microsoft Docs"
description: "Visão geral dos limites de serviço e dos valores de configuração disponíveis para Aplicativos Lógicos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 859f9a1ca9dbe166fd514c8eba868b466dbc4d2c
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---
# <a name="logic-app-limits-and-configuration"></a>Limites e configuração do Aplicativo Lógico

Veja a seguir as informações sobre os limites atuais e detalhes de configuração para Aplicativos Lógicos do Azure.

## <a name="limits"></a>Limites

### <a name="http-request-limits"></a>Limites de solicitação HTTP

Esses são os limites para uma única solicitação e/ou chamada de conector HTTP

#### <a name="timeout"></a>Tempo limite

|Nome|Limite|Observações|
|----|----|----|
|Tempo Limite da Solicitação|120 segundos|Um [padrão assíncrono](../logic-apps/logic-apps-create-api-app.md) ou [loop until](logic-apps-loops-and-scopes.md) pode compensar, conforme necessário|

#### <a name="message-size"></a>Tamanho da mensagem

|Nome|Limite|Observações|
|----|----|----|
|Tamanho da mensagem|100 MB|Talvez alguns conectores e APIs não ofereçam suporte para 100 MB |
|Limite de avaliação da expressão|131.072 caracteres|`@concat()`, `@base64()`, `string` não pode ser maior do que isso|

#### <a name="retry-policy"></a>Política de repetição

|Nome|Limite|Observações|
|----|----|----|
|Tentativas de repetição|4|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Atraso máximo de nova tentativa|1 hora|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Atraso mínimo de nova tentativa|5 segundos|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Retenção e duração da execução

Estes são os limites de execução de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Duração da execução|90 dias||
|Retenção de armazenamento|90 dias|A partir da hora de início de execução|
|Intervalo de recorrência mín.|1 s|| 15 segundos para aplicativos lógicos com Plano do Serviço de Aplicativo
|Intervalo de recorrência máx.|500 dias||


### <a name="looping-and-debatching-limits"></a>Limites de loop e debatching

Estes são os limites de execução de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Itens ForEach|100.000|Você pode usar a [ação de consulta](../connectors/connectors-native-query.md) para filtrar matrizes maiores, conforme o necessário|
|Iterações Until|5.000||
|Itens SplitOn|100.000||
|Paralelismo de ForEach|20|Você pode definir um foreach sequencial adicionando `"operationOptions": "Sequential"` à ação `foreach`|


### <a name="throughput-limits"></a>Limites de taxa de transferência

Estes são os limites para uma instância única de aplicativo lógico. 

|Nome|Limite|Observações|
|----|----|----|
|Execuções de ações a cada cinco minutos |100.000|Pode distribuir carga de trabalho entre vários aplicativos conforme necessário|
|Chamadas de saída simultâneas a ações |~2.500|Diminuir o número de solicitações simultâneas ou reduzir a duração conforme necessário|
|Chamadas de entrada simultâneas do ponto de extremidade no tempo de execução |~1,000|Diminuir o número de solicitações simultâneas ou reduzir a duração conforme necessário|
|Ponto de extremidade de tempo de execução lê chamadas por 5 minutos |60.000|Pode distribuir carga de trabalho entre vários aplicativos conforme necessário|
|Ponto de extremidade de tempo de execução invoca chamadas por 5 minutos |45,000|Pode distribuir carga de trabalho entre vários aplicativos conforme necessário|

Se você pretende exceder esse limite de processamento normal, ou se quiser executar testes de carga que podem ultrapassar esse limite para um período de tempo, [entre em contato conosco](mailto://logicappsemail@microsoft.com) para que possamos ajudar com suas necessidades.

### <a name="definition-limits"></a>Limites de definição

Estes são os limites de definição de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Ações por fluxo de trabalho|250|Você pode adicionar fluxos de trabalho aninhados para estendê-lo conforme necessário|
|Profundidade de aninhamento de ação permitida|5|Você pode adicionar fluxos de trabalho aninhados para estendê-lo conforme necessário|
|Fluxos de trabalho por região e assinatura|1000||
|Gatilhos por fluxo de trabalho|10||
|Máximo de caracteres por expressão|8.192||
|Tamanho máximo de `trackedProperties` em caracteres|16.000|
|`action`/`trigger` |80||
|`description` |256||
|`parameters` limit|50||
|`outputs` limit|10||

### <a name="integration-account-limits"></a>Limites da conta de integração

Estes são limites para artefatos adicionados à conta de integração

|Nome|Limite|Observações|
|----|----|----|
|Esquema|8 MB|Você pode usar o [URI do blob](logic-apps-enterprise-integration-schemas.md) para carregar arquivos maiores que 2 MB |
|Mapa (arquivo XSLT)|2 MB| |
|Ponto de extremidade de tempo de execução lê chamadas por 5 minutos |60.000|Pode distribuir carga de trabalho entre várias contas conforme necessário|
|Ponto de extremidade de tempo de execução invoca chamadas por 5 minutos |45,000|Pode distribuir carga de trabalho entre várias contas conforme necessário|
|Ponto de extremidade do tempo de execução acompanhando chamadas por 5 minutos |45,000|Pode distribuir carga de trabalho entre várias contas conforme necessário|
|Ponto de extremidade de tempo de execução bloqueando chamadas simultâneas |~1,000|Diminuir o número de solicitações simultâneas ou reduzir a duração conforme necessário|

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Tamanho da mensagem dos protocolos B2B (AS2, X12, EDIFACT)

Estes são os limites para os protocolos B2B

|Nome|Limite|Observações|
|----|----|----|
|AS2|50 MB|Aplicável ao decodificar e codificar|
|X12|50 MB|Aplicável ao decodificar e codificar|
|EDIFACT|50 MB|Aplicável ao decodificar e codificar|

## <a name="configuration"></a>Configuração

### <a name="ip-address"></a>Endereço IP

#### <a name="logic-app-service"></a>Serviço de Aplicativo lógico

Chamadas feitas diretamente de um aplicativo lógico (por exemplo, via [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) ou outras solicitações HTTP virão do endereço IP especificado abaixo:

|Região do aplicativo Lógico|IP de Saída|
|-----|----|
|Leste da Austrália|13.75.153.66, 104.210.89.222, 104.210.89.244, 13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sudeste da Austrália|13.73.115.153, 40.115.78.70, 40.115.78.237, 13.73.114.207, 13.77.3.139, 13.70.159.205|
|Sul do Brasil|191.235.86.199, 191.235.95.229, 191.235.94.220, 191.235.82.221, 191.235.91.7, 191.234.182.26|
|Canadá Central|52.233.29.92,52.228.39.241,52.228.39.244|
|Leste do Canadá|52.232.128.155,52.229.120.45,52.229.126.25|
|Índia Central|52.172.157.194, 52.172.184.192, 52.172.191.194, 52.172.154.168, 52.172.186.159, 52.172.185.79|
|Centro dos EUA|13.67.236.76, 40.77.111.254, 40.77.31.87, 13.67.236.125, 104.208.25.27, 40.122.170.198|
|Ásia Oriental|168.63.200.173, 13.75.89.159, 23.97.68.172, 13.75.94.173, 40.83.127.19, 52.175.33.254|
|Leste dos EUA|137.135.106.54, 40.117.99.79, 40.117.100.228, 13.92.98.111, 40.121.91.41, 40.114.82.191|
|Leste dos EUA 2|40.84.25.234, 40.79.44.7, 40.84.59.136, 40.84.30.147, 104.208.155.200, 104.208.158.174|
|Leste do Japão|13.71.146.140, 13.78.84.187, 13.78.62.130, 13.71.158.3, 13.73.4.207, 13.71.158.120|
|Oeste do Japão|40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.140.4, 104.214.137.243, 138.91.26.45|
|Centro-Norte dos EUA|168.62.249.81, 157.56.12.202, 65.52.211.164, 168.62.248.37, 157.55.210.61, 157.55.212.238|
|Norte da Europa|13.79.173.49, 52.169.218.253, 52.169.220.174, 40.113.12.95, 52.178.165.215, 52.178.166.21|
|Centro-Sul dos Estados Unidos|13.65.98.39, 13.84.41.46, 13.84.43.45, 104.210.144.48, 13.65.82.17, 13.66.52.232|
|Sudeste Asiático|52.163.93.214, 52.187.65.81, 52.187.65.155, 13.76.133.155, 52.163.228.93, 52.163.230.166|
|Sul da Índia|52.172.9.47, 52.172.49.43, 52.172.51.140, 52.172.50.24, 52.172.55.231, 52.172.52.0|
|Europa Ocidental|13.95.155.53, 52.174.54.218, 52.174.49.6, 40.68.222.65, 40.68.209.23, 13.95.147.65|
|Índia Ocidental|104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.164.80, 104.211.162.205, 104.211.164.136|
|Oeste dos EUA|52.160.90.237, 138.91.188.137, 13.91.252.184, 52.160.92.112, 40.118.244.241, 40.118.241.243|

#### <a name="connectors"></a>Conectores

Chamadas feitas a partir de um [conector](../connectors/apis-list.md) virão do endereço IP especificado abaixo:

|Região do aplicativo Lógico|IP de Saída|
|-----|----|
|Leste da Austrália|40.126.251.213|
|Sudeste da Austrália|40.127.80.34|
|Sul do Brasil|191.232.38.129|
|Canadá Central|52.233.31.197,52.228.42.205,52.228.33.76,52.228.34.13|
|Leste do Canadá|52.229.123.98,52.229.120.178,52.229.126.202,52.229.120.52|
|Índia Central|104.211.98.164|
|Centro dos EUA|40.122.49.51|
|Ásia Oriental|23.99.116.181|
|Leste dos EUA|191.237.41.52|
|Leste dos EUA 2|104.208.233.100|
|Leste do Japão|40.115.186.96|
|Oeste do Japão|40.74.130.77|
|Centro-Norte dos EUA|65.52.218.230|
|Norte da Europa|104.45.93.9|
|Centro-Sul dos Estados Unidos|104.214.70.191|
|Sudeste Asiático|13.76.231.68|
|Sul da Índia|104.211.227.225|
|Europa Ocidental|40.115.50.13|
|Índia Ocidental|104.211.161.203|
|Oeste dos EUA|104.40.51.248|


## <a name="next-steps"></a>Próximas etapas  

- Para começar com aplicativos lógicos, siga o tutorial [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) .  
- [Veja exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
- [Você pode automatizar processos de negócios com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Aprenda a integrar seus sistemas com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/P462)

