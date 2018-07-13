---
title: Limites e configuração – Aplicativos Lógicos do Azure | Microsoft Docs
description: Valores de limites e de configuração do serviço para os Aplicativos Lógicos do Azure
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 8baca0fc46489a22d587ba6e742615b1da79c19a
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970220"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informações de limites e configuração para os Aplicativos Lógicos do Azure

Este artigo descreve os limites e os detalhes de configuração para criar e executar fluxos de trabalho automatizados com os Aplicativos Lógicos do Azure. Para o Microsoft Flow, consulte [Limites e configuração no Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limites de definição

Estes são os limites de definição de um único aplicativo lógico:

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| Ações por fluxo de trabalho | 500 | Para estender esse limite, adicione fluxos de trabalho aninhados conforme necessário. |
| Profundidade de aninhamento permitido para ações | 8 | Para estender esse limite, adicione fluxos de trabalho aninhados conforme necessário. | 
| Fluxos de trabalho por região e assinatura | 1.000 | | 
| Gatilhos por fluxo de trabalho | 10 | Ao trabalhar no modo de exibição de código, não no designer | 
| Limite de casos de escopo do comutador | 25 | | 
| Variáveis por fluxo de trabalho | 250 | | 
| Caracteres por expressão | 8.192 | | 
| Tamanho máximo para `trackedProperties` | 16.000 caracteres | 
| Nome para `action` ou `trigger` | 80 caracteres | | 
| Comprimento de `description` | 256 caracteres | | 
| Máximo `parameters` | 50 | | 
| Máximo `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Limites de retenção e duração da execução

Estes são os limites de execução de um único aplicativo lógico:

| NOME | Limite | Observações | 
|------|-------|-------| 
| Duração da execução | 90 dias | Para alterar esse limite, consulte [Alterar duração da execução](#change-duration). | 
| Retenção de armazenamento | 90 dias a partir da hora de início de execução | Para alterar esse limite, consulte [Alterar retenção de armazenamento](#change-retention). | 
| Intervalo de recorrência mínimo | 1 segundo | | 
| Intervalo de recorrência máximo | 500 dias | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Alterar a duração de execução e a retenção de armazenamento

Você pode alterar esse limite para um valor entre 7 e 90 dias. Para ultrapassar o limite máximo, [entre em contato com a equipe de Aplicativos Lógicos](mailto://logicappsemail@microsoft.com) para obter ajuda com seus requisitos.

1. No Portal do Azure, no menu do aplicativo lógico, escolha **Configurações de fluxo de trabalho**. 

2. Em **Opções de tempo de execução**, na lista **Retenção histórica de execuções em dias** , escolha **Personalizado**. 

3. Digite ou arraste o controle deslizante para o número de dias que você deseja.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>Limites de loop e debatching

Estes são os limites de execução de um único aplicativo lógico:

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| Iterações Until | 5.000 | | 
| Itens ForEach | 100.000 | Você pode usar a [ação de consulta](../connectors/connectors-native-query.md) para filtrar matrizes maiores, conforme o necessário. | 
| Paralelismo de ForEach | 50 | O padrão é 20. <p>Para alterar o nível padrão em um loop ForEach, defina a propriedade `runtimeConfiguration` na ação `foreach`. <p>Para executar em sequência um loop ForEach, defina a propriedade `operationOptions` como "Sequencial" na ação `foreach`. | 
| Itens SplitOn | 100.000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de taxa de transferência

Estes são os limites de execução de um único aplicativo lógico:

| NOME | Limite | Observações | 
| ----- | ----- | ----- | 
| Execuções de ações a cada cinco minutos | 100.000 | Para aumentar o limite para 300.000, você pode executar um aplicativo lógico no modo `High Throughput`. Para configurar o modo de taxa de transferência no `runtimeConfiguration` do recurso de fluxo de trabalho, defina a propriedade `operationOptions` como `OptimizedForHighThroughput`. <p>**Nota**: O modo de alta taxa de transferência está em versão prévia. Você também pode distribuir uma carga de trabalho entre mais de um aplicativo conforme necessário. | 
| Chamadas de saída simultâneas a ações | ~2.500 | Reduza o número de solicitações simultâneas ou reduza a duração conforme necessário. | 
| Ponto de extremidade de tempo de execução: chamadas de entrada simultâneas | ~1,000 | Reduza o número de solicitações simultâneas ou reduza a duração conforme necessário. | 
| Ponto de extremidade de tempo de execução: lê chamadas por 5 minutos  | 60.000 | É possível distribuir uma carga de trabalho entre mais de um aplicativo conforme necessário. | 
| Ponto de extremidade de tempo de execução: invoca chamadas por 5 minutos| 45,000 | É possível distribuir uma carga de trabalho entre mais de um aplicativo conforme necessário. | 
|||| 

Para ultrapassar esses limites no processamento normal ou executar um teste de carga que possa ultrapassar esses limites, [entre em contato com a equipe de Aplicativos Lógicos](mailto://logicappsemail@microsoft.com) para que possam ajudá-lo com suas necessidades.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>Limites de solicitação HTTP

Estes são os limites para uma única solicitação HTTP ou chamada de conector síncrona:

#### <a name="timeout"></a>Tempo limite

Algumas operações de conector fazem chamadas assíncronas ou escutam solicitações de webhook, portanto o tempo limite para essas operações pode ter mais do que esses limites. Para obter mais informações, consulte os detalhes técnicos para o conector específico e também [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| Solicitação de saída | 120 segundos | Para operações com execução mais longa, use um [padrão de sondagem assíncrona](../logic-apps/logic-apps-create-api-app.md#async-pattern) ou um [até que o loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Resposta síncrona | 120 segundos | Para a solicitação original obter a resposta, todas as etapas na resposta devem ser finalizadas dentro do limite, a menos que você chame outro aplicativo lógico como um fluxo de trabalho aninhado. Para obter mais informações, consulte [Chamar, acionar ou aninhar aplicativos lógicos](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Tamanho da mensagem

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| Tamanho da mensagem | 100 MB | Para contornar esse limite, consulte [Tratar mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). No entanto, alguns conectores e APIs podem não oferecer suporte a agrupamento ou até o limite padrão. | 
| Tamanho da mensagem com agrupamento | 1 GB | Esse limite se aplica a ações com compatibilidade nativa com o agrupamento ou que permitem que você habilite o agrupamento na configuração de tempo de execução. Para obter mais informações, consulte [Tratar mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). | 
| Limite de avaliação da expressão | 131.072 caracteres | As expressões `@concat()`, `@base64()`, `@string()` não podem ser maiores do que esse limite. | 
|||| 

#### <a name="retry-policy"></a>Política de repetição

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| Tentativas de repetição | 90 | O padrão é 4. Para alterar o padrão, use o [Parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Atraso máximo de nova tentativa | 1 dia | Para alterar o padrão, use o [Parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Atraso mínimo de nova tentativa | 5 segundos | Para alterar o padrão, use o [Parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limites do conector personalizado

Aqui estão os limites para conectores personalizados que você pode criar de APIs da Web.

| NOME | Limite | 
| ---- | ----- | 
| Número de conectores personalizados | 1.000 por assinatura do Azure | 
| Número de solicitações por minuto para cada conexão criada por um conector personalizado | 500 solicitações por conexão |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites da conta de integração

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites de artefato por conta de integração

Estes são os limites no número de artefatos para cada conta de integração. Para obter mais informações, consulte [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Camada gratuita*

Use a camada gratuita somente para cenários exploratórios, não cenários de produção. Esta camada restringe o uso e a taxa de transferência e não tem nenhum SLA (Contrato de Nível de Serviço).

| Artefato | Limite | Observações | 
|----------|-------|-------| 
| Parceiros comerciais de EDI | 25 | | 
| Contratos comerciais de EDI | 10 | | 
| Mapas | 25 | | 
| Esquemas | 25 | 
| Assemblies | 10 | | 
| Configurações de lote | 5 | 
| Certificados | 25 | | 
|||| 

*Camada básica*

| Artefato | Limite | Observações | 
|----------|-------|-------| 
| Parceiros comerciais de EDI | 2 | | 
| Contratos comerciais de EDI | 1 | | 
| Mapas | 500 | | 
| Esquemas | 500 | 
| Assemblies | 25 | | 
| Configurações de lote | 1 | | 
| Certificados | 2 | | 
|||| 

*Camada Standard*

| Artefato | Limite | Observações | 
|----------|-------|-------| 
| Parceiros comerciais de EDI | 500 | | 
| Contratos comerciais de EDI | 500 | | 
| Mapas | 500 | | 
| Esquemas | 500 | 
| Assemblies | 50 | | 
| Configurações de lote | 5 |  
| Certificados | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacidade do artefato

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| Esquema | 8 MB | Para carregar arquivos maiores que 2 MB, use o [URI do blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mapa (arquivo XSLT) | 2 MB | | 
| Ponto de extremidade de tempo de execução: lê chamadas por 5 minutos | 60.000 | Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. | 
| Ponto de extremidade de tempo de execução: invoca chamadas por 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. | 
| Ponto de extremidade do tempo de execução: acompanhando chamadas por 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. | 
| Ponto de extremidade de tempo de execução: bloqueando chamadas simultâneas | ~1,000 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Tamanho da mensagem do protocolo B2B (AS2, X12, EDIFACT)

Estes são os limites que se aplicam a protocolos B2B:

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Aplicável ao decodificar e codificar | 
| X12 | 50 MB | Aplicável ao decodificar e codificar | 
| EDIFACT | 50 MB | Aplicável ao decodificar e codificar | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configuração: endereços IP

### <a name="azure-logic-apps-service"></a>Serviço de Aplicativo Lógico do Azure

Todos os aplicativos lógicos em uma região usam os mesmos intervalos de endereços IP. Para dar suporte às chamadas que os aplicativos lógicos fazem diretamente com [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) e outras solicitações HTTP, defina suas configurações de firewall para que elas incluam esses endereços IP de saída e de entrada, com base no local em que os aplicativos lógicos existem:

| Região de Aplicativos Lógicos | IP de Saída |
|-------------------|-------------|
| Leste da Austrália | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Sudeste da Austrália | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
| Sul do Brasil | 191.235.82.221, 191.235.91.7, 191.234.182.26 |
| Canadá Central | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Leste do Canadá | 52.232.128.155, 52.229.120.45, 52.229.126.25 |
| Índia Central | 52.172.154.168, 52.172.186.159, 52.172.185.79 |
| Centro dos EUA | 13.67.236.125, 104.208.25.27, 40.122.170.198 |
| Ásia Oriental | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| Leste dos EUA | 13.92.98.111, 40.121.91.41, 40.114.82.191 |
| Leste dos EUA 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Leste do Japão | 13.71.158.3, 13.73.4.207, 13.71.158.120 |
| Oeste do Japão | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| Centro-Norte dos EUA | 168.62.248.37, 157.55.210.61, 157.55.212.238 |
| Norte da Europa | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| Centro-Sul dos Estados Unidos | 104.210.144.48, 13.65.82.17, 13.66.52.232 |
| Sul da Índia | 52.172.50.24, 52.172.55.231, 52.172.52.0 |
| Sudeste Asiático | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| Centro-Oeste dos EUA | 52.161.27.190, 52.161.18.218, 52.161.9.108 |
| Europa Ocidental | 40.68.222.65, 40.68.209.23, 13.95.147.65 |
| Índia Ocidental | 104.211.164.80, 104.211.162.205, 104.211.164.136 |
| Oeste dos EUA | 52.160.92.112, 40.118.244.241, 40.118.241.243 |
| Oeste dos EUA 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132 |
| Sul do Reino Unido | 51.140.74.14, 51.140.73.85, 51.140.78.44 |
| Oeste do Reino Unido | 51.141.54.185, 51.141.45.238, 51.141.47.136 |
| | |

| Região de Aplicativos Lógicos | IP de entrada |
|-------------------|------------|
| Leste da Austrália | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Sudeste da Austrália | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Sul do Brasil | 191.235.86.199, 191.235.95.229, 191.235.94.220 |
| Canadá Central | 13.88.249.209, 52.233.30.218, 52.233.29.79 |
| Leste do Canadá | 52.232.129.143, 52.229.125.57, 52.232.133.109 |
| Índia Central | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| Centro dos EUA | 13.67.236.76, 40.77.111.254, 40.77.31.87 |
| Ásia Oriental | 168.63.200.173, 13.75.89.159, 23.97.68.172 |
| Leste dos EUA | 137.135.106.54, 40.117.99.79, 40.117.100.228 |
| Leste dos EUA 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136 |
| Leste do Japão | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Oeste do Japão | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| Centro-Norte dos EUA | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Norte da Europa | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| Centro-Sul dos Estados Unidos | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Sul da Índia | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Sudeste Asiático | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| Centro-Oeste dos EUA | 52.161.26.172, 52.161.8.128, 52.161.19.82 |
| Europa Ocidental | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Índia Ocidental | 104.211.164.112, 104.211.165.81, 104.211.164.25 |
| Oeste dos EUA | 52.160.90.237, 138.91.188.137, 13.91.252.184 |
| Oeste dos EUA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Sul do Reino Unido | 51.140.79.109, 51.140.78.71, 51.140.84.39 |
| Oeste do Reino Unido | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Conectores

Para dar suporte às chamadas que os [conectores](../connectors/apis-list.md) fazem, defina suas configurações de firewall para que elas incluam esses endereços IP de saída, com base nas regiões em que os aplicativos lógicos existem.

> [!IMPORTANT]
>
> Se você tiver configurações existentes, atualize-as **assim que possível antes de 1º de setembro de 2018** para que elas incluam e correspondam aos endereços IP nessa lista para as regiões em que os aplicativos lógicos existem. 

| Região de Aplicativos Lógicos | IP de Saída | 
|-------------------|-------------|  
| Leste da Austrália | 13.70.72.192 – 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Sudeste da Austrália | 13.77.50.240 – 13.77.50.255, 13.70.136.174, 40.127.80.34 | 
| Sul do Brasil | 191.233.203.192 – 191.233.203.207, 104.41.59.51, 191.232.38.129 | 
| Canadá Central | 13.71.170.208 – 13.71.170.223, 13.71.170.224 – 13.71.170.239, 52.237.24.126, 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13 | 
| Leste do Canadá | 40.69.106.240 – 40.69.106.255, 52.242.35.152, 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52 | 
| Índia Central | 104.211.81.192 – 104.211.81.207, 52.172.211.12, 104.211.98.164 | 
| Centro dos EUA | 13.89.171.80 – 13.89.171.95, 52.173.245.164, 40.122.49.51 | 
| Ásia Oriental | 13.75.36.64 – 13.75.36.79, 52.175.23.169, 23.99.116.181 | 
| Leste dos EUA | 40.71.11.80 – 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| Leste dos EUA 2 | 40.70.146.208 – 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Leste do Japão | 13.78.108.0 – 13.78.108.15, 13.71.153.19, 40.115.186.96 | 
| Oeste do Japão | 40.74.100.224 – 40.74.100.239, 104.215.61.248, 40.74.130.77 | 
| Centro-Norte dos EUA | 52.162.107.160 – 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Norte da Europa | 13.69.227.208 – 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| Centro-Sul dos Estados Unidos | 104.214.19.48 – 104.214.19.63, 13.65.86.57, 104.214.70.191 | 
| Sul da Índia | 40.78.194.240 – 40.78.194.255, 13.71.125.22, 104.211.227.225 | 
| Sudeste Asiático | 13.67.8.240 – 13.67.8.255, 52.187.68.19, 13.76.231.68 | 
| Centro-Oeste dos EUA | 13.71.195.32 – 13.71.195.47, 52.161.102.22, 52.161.27.108, 52.161.30.5, 52.161.29.35, 52.161.26.212 | 
| Europa Ocidental | 13.69.64.208 – 13.69.64.223, 52.174.88.118, 40.115.50.13 | 
| Índia Ocidental | 104.211.146.224 – 104.211.146.239, 104.211.189.218, 104.211.161.203 | 
| Oeste dos EUA | 40.112.243.160 – 40.112.243.175, 104.42.122.49, 104.40.51.248 | 
| Oeste dos EUA 2 | 13.66.140.128 – 13.66.140.143, 52.183.78.157, 13.66.225.219, 13.66.218.78, 13.66.220.135, 13.66.219.14 | 
| Sul do Reino Unido | 51.140.148.0 – 51.140.148.15, 51.140.80.51, 51.140.80.51 | 
| Oeste do Reino Unido | 51.140.211.0 – 51.140.211.15, 51.141.47.105, 51.141.47.105 | 
| | | 

## <a name="next-steps"></a>Próximas etapas  

* Saiba como [criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Saiba mais sobre [exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
