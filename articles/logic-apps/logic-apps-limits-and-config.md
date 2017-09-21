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
ms.date: 08/18/2017
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 5d905d410e70c5b635a3f6221e7e0c0bda7ad140
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="logic-app-limits-and-configuration"></a>Limites e configuração do Aplicativo Lógico

A seguir, as informações sobre os limites atuais e detalhes de configuração para Aplicativos Lógicos do Azure.

## <a name="limits"></a>limites

### <a name="http-request-limits"></a>Limites de solicitação HTTP

A seguir, os limites para uma única solicitação e/ou chamada de conector HTTP.

#### <a name="timeout"></a>Tempo limite

|Nome|Limite|Observações|
|----|----|----|
|Tempo Limite da Solicitação|120 segundos|Um [padrão assíncrono](../logic-apps/logic-apps-create-api-app.md) ou [loop until](logic-apps-loops-and-scopes.md) pode compensar, conforme necessário|

#### <a name="message-size"></a>Tamanho da mensagem

|Nome|Limite|Observações|
|----|----|----|
|Tamanho da mensagem|100 MB|Alguns conectores e APIs não oferecem suporte a 100 MB |
|Limite de avaliação da expressão|131.072 caracteres|`@concat()`, `@base64()`, `string` não podem ser maiores do que esse limite|

#### <a name="retry-policy"></a>Política de repetição

|Nome|Limite|Observações|
|----|----|----|
|Tentativas de repetição|10| O padrão é 4. Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Atraso máximo de nova tentativa|1 hora|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Atraso mínimo de nova tentativa|5 segundos|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Retenção e duração da execução

A seguir, os limites de execução de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Duração da execução|90 dias||
|Retenção de armazenamento|90 dias|A partir da hora de início de execução|
|Intervalo de recorrência mín.|1 s|| 15 segundos para aplicativos lógicos com Plano do Serviço de Aplicativo
|Intervalo de recorrência máx.|500 dias||

Se você pretende exceder executar os limites de retenção de duração ou armazenamento no fluxo de processamento normal [entre em contato conosco](mailto://logicappsemail@microsoft.com) para que podemos ajudar com suas necessidades.


### <a name="looping-and-debatching-limits"></a>Limites de loop e debatching

A seguir, os limites de execução de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Itens ForEach|100.000|Você pode usar a [ação de consulta](../connectors/connectors-native-query.md) para filtrar matrizes maiores, conforme o necessário|
|Iterações Until|5.000||
|Itens SplitOn|100.000||
|Paralelismo de ForEach|50| O padrão é 20. Você pode definir para um foreach sequencial adicionando `"operationOptions": "Sequential"` à ação `foreach` ou especifique o nível de paralelismo usando `runtimeConfiguration`|


### <a name="throughput-limits"></a>Limites de taxa de transferência

A seguir, os limites para uma instância única de aplicativo lógico. 

|Nome|Limite|Observações|
|----|----|----|
|Execuções de ações a cada cinco minutos |100.000|Pode distribuir carga de trabalho entre vários aplicativos conforme necessário|
|Chamadas de saída simultâneas a ações |~2.500|Diminuir o número de solicitações simultâneas ou reduzir a duração conforme necessário|
|Chamadas de entrada simultâneas do ponto de extremidade no tempo de execução |~1,000|Diminuir o número de solicitações simultâneas ou reduzir a duração conforme necessário|
|Ponto de extremidade de tempo de execução lê chamadas por 5 minutos |60.000|Pode distribuir carga de trabalho entre vários aplicativos conforme necessário|
|Ponto de extremidade de tempo de execução invoca chamadas por 5 minutos |45,000|Pode distribuir carga de trabalho entre vários aplicativos conforme necessário|

Se você pretende exceder esse limite de processamento normal, ou se quiser executar testes de carga que podem ultrapassar esse limite para um período de tempo, [entre em contato conosco](mailto://logicappsemail@microsoft.com) para que possamos ajudar com suas necessidades.

### <a name="definition-limits"></a>Limites de definição

A seguir, os limites de definição de um único aplicativo lógico.

|Nome|Limite|Observações|
|----|----|----|
|Ações por fluxo de trabalho|500|Você pode adicionar fluxos de trabalho aninhados para estender esse limite conforme necessário|
|Profundidade de aninhamento de ação permitida|8|Você pode adicionar fluxos de trabalho aninhados para estender esse limite conforme necessário|
|Fluxos de trabalho por região e assinatura|1000||
|Gatilhos por fluxo de trabalho|10||
|Limite de casos de escopo do comutador|25||
|Número de variáveis por fluxo de trabalho|250||
|Máximo de caracteres por expressão|8.192||
|Tamanho máximo de `trackedProperties` em caracteres|16.000|
|`action`/`trigger` |80||
|`description` |256||
|`parameters` limit|50||
|`outputs` limit|10||

### <a name="integration-account-limits"></a>Limites da conta de integração

Estes são os limites para os artefatos que podem ser adicionados a uma Conta de Integração.

|Nome|Limite|Observações|
|----|----|----|
|Esquema|8 MB|Você pode usar o [URI do blob](logic-apps-enterprise-integration-schemas.md) para carregar arquivos maiores que 2 MB |
|Mapa (arquivo XSLT)|2 MB| |
|Ponto de extremidade de tempo de execução lê chamadas por 5 minutos |60.000|Pode distribuir carga de trabalho entre várias contas conforme necessário|
|Ponto de extremidade de tempo de execução invoca chamadas por 5 minutos |45,000|Pode distribuir carga de trabalho entre várias contas conforme necessário|
|Ponto de extremidade do tempo de execução acompanhando chamadas por 5 minutos |45,000|Pode distribuir carga de trabalho entre várias contas conforme necessário|
|Ponto de extremidade de tempo de execução bloqueando chamadas simultâneas |~1,000|Diminuir o número de solicitações simultâneas ou reduzir a duração conforme necessário|

Estes são os limites no número de artefatos que podem ser adicionados a uma Conta de Integração.

Tipo de preço Gratuito

|Nome|Limite|Observações|
|----|----|----|
|Contratos|10||
|Outros tipos de artefato|25|Os tipos incluem parceiros, esquemas, certificados e mapas. Cada tipo pode conter até o número máximo de artefatos.|

Tipo de preço Standard

|Nome|Limite|Observações|
|----|----|----|
|Qualquer tipo de artefato|500|Os tipos incluem contratos, parceiros, esquemas, certificados e mapas. Cada tipo pode conter até o número máximo de artefatos.|

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Tamanho da mensagem dos protocolos B2B (AS2, X12, EDIFACT)

A seguir, os limites para os protocolos B2B

|Nome|Limite|Observações|
|----|----|----|
|AS2|50 MB|Aplicável ao decodificar e codificar|
|X12|50 MB|Aplicável ao decodificar e codificar|
|EDIFACT|50 MB|Aplicável ao decodificar e codificar|

## <a name="configuration"></a>Configuração

### <a name="ip-address"></a>Endereço IP

#### <a name="logic-app-service"></a>Serviço de Aplicativo lógico

As chamadas feitas diretamente de um aplicativo lógico (ou seja, via [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) ou outras solicitações HTTP virão do endereço IP especificado nesta lista:

|Região do aplicativo Lógico|IP de Saída|
|-----|----|
|Leste da Austrália|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sudeste da Austrália|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Sul do Brasil|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Canadá Central|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Leste do Canadá|52.232.128.155, 52.229.120.45, 52.229.126.25|
|Índia Central|52.172.154.168, 52.172.186.159, 52.172.185.79|
|Centro dos EUA|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Ásia Oriental|13.75.94.173, 40.83.127.19, 52.175.33.254|
|Leste dos EUA|13.92.98.111, 40.121.91.41, 40.114.82.191|
|Leste dos EUA 2|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Leste do Japão|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Oeste do Japão|40.74.140.4, 104.214.137.243, 138.91.26.45|
|Centro-Norte dos EUA|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Norte da Europa|40.113.12.95, 52.178.165.215, 52.178.166.21|
|Centro-Sul dos Estados Unidos|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Sudeste Asiático|13.76.133.155, 52.163.228.93, 52.163.230.166|
|Sul da Índia|52.172.50.24, 52.172.55.231, 52.172.52.0|
|Centro-Oeste dos EUA|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Europa Ocidental|40.68.222.65, 40.68.209.23, 13.95.147.65|
|Índia Ocidental|104.211.164.80, 104.211.162.205, 104.211.164.136|
|Oeste dos EUA|52.160.92.112, 40.118.244.241, 40.118.241.243|
|Oeste dos EUA 2|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Sul do Reino Unido|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Oeste do Reino Unido|51.141.54.185, 51.141.45.238, 51.141.47.136|

#### <a name="connectors"></a>Conectores

As chamadas feitas de um [conector](../connectors/apis-list.md) vêm do endereço IP especificado na lista a seguir:

|Região do aplicativo Lógico|IP de Saída|
|-----|----|
|Leste da Austrália|40.126.251.213|
|Sudeste da Austrália|40.127.80.34|
|Sul do Brasil|191.232.38.129|
|Canadá Central|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Leste do Canadá|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
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
|Sul do Reino Unido|51.140.80.51|
|Oeste do Reino Unido|51.141.47.105|


## <a name="next-steps"></a>Próximas etapas  

- Para começar com aplicativos lógicos, siga o tutorial [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).  
- [Veja exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
- [Você pode automatizar processos de negócios com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Aprenda a integrar seus sistemas com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/P462)

