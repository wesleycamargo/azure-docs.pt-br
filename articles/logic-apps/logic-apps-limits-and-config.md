---
title: Limites e configuração – Aplicativos Lógicos do Azure | Microsoft Docs
description: Valores de limites e de configuração do serviço para os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/16/2018
ms.openlocfilehash: 4a2b06f18e709f9a8479770d58f43928893281be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117346"
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
| Retenção de armazenamento | 90 dias a partir da hora de início de execução | Para alterar esse limite para um valor entre 7 dias e 90 dias, consulte [alterar a retenção de armazenamento](#change-retention). | 
| Intervalo de recorrência mínimo | 1 segundo | | 
| Intervalo de recorrência máximo | 500 dias | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Alterar a duração de execução e a retenção de armazenamento

Para alterar o limite padrão para entre 7 dias e 90 dias, siga estas etapas. Se você precisar ultrapassar o limite máximo, [entre em contato com a equipe do Logic Apps](mailto://logicappsemail@microsoft.com) para obter ajuda com suas necessidades.

1. No Portal do Azure, no menu do aplicativo lógico, escolha **Configurações de fluxo de trabalho**. 

2. Em **Opções de tempo de execução**, na lista **Retenção histórica de execuções em dias** , escolha **Personalizado**. 

3. Digite ou arraste o controle deslizante para o número de dias que você deseja.

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Desabilitar ou excluir aplicativos lógicos

Ao desabilitar um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes continuarão até que sejam concluídas, e isso poderá demorar algum tempo.

Ao excluir um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes serão canceladas. Se você tiver milhares de execuções, o cancelamento poderá demorar um tempo significativo até ser concluído.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Simultaneidade, um loop e debatching de limites

Estes são os limites de execução de um único aplicativo lógico:

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| Simultaneidade do gatilho | * Ilimitada quando o controle de simultaneidade está desativado <p><p>* 25 é o limite padrão, quando o controle de simultaneidade está ativado, o que não pode ser desfeito depois de ativar o controle. Você pode alterar o padrão para um valor entre 1 e 50, inclusive. | Esse limite descreve o número mais alto de instâncias de aplicativo lógico que podem ser executados ao mesmo tempo, ou em paralelo. <p><p>Para alterar o limite padrão para um valor entre 1 e 50, inclusive, consulte [Alterar o limite de simultaneidade do gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) ou [Disparar instâncias sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). | 
| Execuções de espera máximo | Quando o controle de simultaneidade é ativado, o número mínimo de execuções de espera é 10 mais o número de execuções simultâneas (simultaneidade do gatilho). Você pode alterar o número máximo até 100, inclusive. | Esse limite descreve o maior número de instâncias do aplicativo lógico que pode aguardar para ser executado quando o aplicativo lógico já está em execução o número máximo de instâncias simultâneo. <p><p>Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). | 
| Itens da matriz de foreach | 100.000 | Esse limite descreve o maior número de itens de matriz que um loop "para cada" pode processar. <p><p>Para filtrar matrizes maiores, você pode usar o [ação de consulta](../connectors/connectors-native-query.md). | 
| Simultaneidade de foreach | 20 é o limite padrão quando o controle de simultaneidade é desativado. Você pode alterar o padrão para um valor entre 1 e 50, inclusive. | Esse limite é o maior número de iterações de loop "for each" que podem ser executadas ao mesmo tempo ou em paralelo. <p><p>Para alterar o limite padrão para um valor entre 1 e 50 inclusive, consulte [Alterar o limite de simultaneidade “para cada”](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) ou [Executar loops "para cada" sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). | 
| Itens SplitOn | 100.000 | Para gatilhos que retornam uma matriz, você pode especificar uma expressão que usa uma propriedade 'SplitOn' que [divide ou retira de lote os itens da matriz em várias instâncias de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) para processamento, em vez de usar um loop "Foreach". Essa expressão referencia a matriz a ser usada para criar e executar uma instância de fluxo de trabalho para cada item da matriz. |
| Iterações Until | 5.000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de taxa de transferência

Estes são os limites de execução de um único aplicativo lógico:

| NOME | Limite | Observações | 
| ---- | ----- | ----- | 
| Ação: Execuções a cada 5 minutos | 300.000 | O limite padrão é 100.000. Para alterar o limite padrão, consulte [Execute seu aplicativo lógico no modo "alto rendimento"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), que está na visualização. Ou, você pode distribuir a carga de trabalho entre mais de um aplicativo lógico conforme necessário. | 
| Ação: Chamadas de saída simultâneas | ~2.500 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. | 
| Ponto de extremidade de tempo de execução: Chamadas recebidas simultâneas | ~1,000 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. | 
| Ponto de extremidade de tempo de execução: Chamadas de leitura a cada 5 minutos  | 60.000 | Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. | 
| Ponto de extremidade de tempo de execução: Chamadas de invocação a cada 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. | 
| Taxa de transferência de conteúdo por 5 minutos | 600 MB | Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. | 
|||| 

Para ultrapassar esses limites no processamento normal ou executar um teste de carga que possa ultrapassar esses limites, [entre em contato com a equipe de Aplicativos Lógicos](mailto://logicappsemail@microsoft.com) para que possam ajudá-lo com suas necessidades.

<a name="sftp"></a>

## <a name="ftp-sftp-and-sftp-ssh-limits"></a>Limites de FTP, SFTP e SFTP-SSH

### <a name="file-size"></a>Tamanho do arquivo

| NOME | Limite | Observações |
|------|-------|-------|
| FTP | 50 MB | Para exceder esse limite somente para ações, consulte [Manipular mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). <p>**Observação**: O agrupamento não é aplicável para gatilhos. Além disso, alguns conectores e APIs podem não dar suporte para agrupamento ou mesmo limite padrão. | 
| SFTP | 50 MB | Para exceder esse limite somente para ações, use o [conector SFTP-SSH](../connectors/connectors-sftp-ssh.md) ou consulte [Manipular mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). <p>**Observação**: O agrupamento não é aplicável para gatilhos. Além disso, alguns conectores e APIs podem não dar suporte para agrupamento ou mesmo limite padrão. | 
| SFTP-SSH | 1 GB | Esse conector manipula até 1 GB, mas transfere os dados em partes de 50 MB e não em partes de 1 GB. Para exceder esse limite somente para ações, consulte [Manipular mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). <p>**Observação**: O agrupamento não é aplicável para gatilhos. | 
|||| 

<a name="request-limits"></a>

## <a name="http-limits"></a>Limites do HTTP

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
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Identidades gerenciadas

| NOME | Limite | 
| ---- | ----- | 
| Número de aplicativos lógicos com identidades gerenciadas atribuídas pelo sistema por assinatura do Azure | 10 | 
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites da conta de integração

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites de artefato por conta de integração

Estes são os limites no número de artefatos para cada conta de integração. Para obter mais informações, consulte [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Camada gratuita*

Use a camada gratuita somente para cenários exploratórios, não cenários de produção. Esta camada restringe o uso e a taxa de transferência e não tem nenhum SLA (Contrato de Nível de Serviço).

| Artefato | Limite | Observações | 
|----------|-------|-------| 
| Assemblies | 10 | | 
| Configurações de lote | 5 | 
| Certificados | 25 | | 
| Contratos comerciais de EDI | 10 | | 
| Parceiros comerciais de EDI | 25 | | 
| Mapas | 25 | | 
| Esquemas | 25 | 
|||| 

*Camada básica*

| Artefato | Limite | Observações | 
|----------|-------|-------| 
| Assemblies | 25 | | 
| Configurações de lote | 1 | | 
| Certificados | 2 | | 
| Contratos comerciais de EDI | 1 | | 
| Parceiros comerciais de EDI | 2 | | 
| Mapas | 500 | | 
| Esquemas | 500 | 
|||| 

*Camada Standard*

| Artefato | Limite | Observações | 
|----------|-------|-------| 
| Assemblies | 50 | | 
| Configurações de lote | 5 |  
| Certificados | 50 | | 
| Contratos comerciais de EDI | 500 | | 
| Parceiros comerciais de EDI | 500 | | 
| Mapas | 500 | | 
| Esquemas | 500 | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacidade do artefato

| Artefato | Limite | Observações | 
| -------- | ----- | ----- | 
| Assembly | 8 MB | Para carregar arquivos maiores que 2 MB, use um [contêiner de blob e uma conta de armazenamento do Azure](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mapa (arquivo XSLT) | 8 MB | Para carregar arquivos maiores que 2 MB, use a [API REST de Aplicativos Lógicos do Azure – Mapas](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). | 
| Esquema | 8 MB | Para carregar arquivos maiores que 2 MB, use um [contêiner de blob e uma conta de armazenamento do Azure](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
||||

| Ponto de extremidade de tempo de execução | Limite | Observações |
|------------------|-------|-------|
| Chamadas de leitura a cada 5 minutos | 60.000 | Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. | 
| Chamadas de invocação a cada 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. | 
| Chamadas de acompanhamento a cada 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. | 
| Chamadas simultâneas de bloqueio | ~1,000 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. | 
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

## <a name="firewall-configuration-ip-addresses"></a>Configuração do firewall: Endereços IP

Todos os aplicativos lógicos na mesma região usam os mesmos intervalos de endereços IP. Para dar suporte às chamadas que os aplicativos lógicos fazem diretamente com [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) e outras solicitações HTTP, configure seus firewalls com *todos* os endereços IP de [entrada](#inbound) *e* de [saída](#outbound) usados pelo serviço dos Aplicativos Lógicos, com base nas regiões em que os aplicativos lógicos existem. Esses endereços são exibidos sob os títulos **De entrada** e **De saída** nesta seção e são classificados por região.

Para dar suporte às chamadas que [conectores gerenciados pela Microsoft](../connectors/apis-list.md) fazem, configure seu firewall com *todos* os endereços IP [de saída](#outbound) usados por esses conectores, com base nas regiões em que seus aplicativos lógicos existem. Esses endereços são exibidos sob o título **De saída** nesta seção e são classificados por região. 

Para o [Azure Governamental](../azure-government/documentation-government-overview.md) e [Azure China 21Vianet](/azure/china/china-welcome), os endereços IP reservados para conectores não estão disponíveis no momento.

> [!IMPORTANT]
> 
> Se você tiver configurações existentes, atualize-as **assim que possível antes de 1º de setembro de 2018** para que elas incluam e correspondam aos endereços IP nessas listas para as regiões em que os aplicativos lógicos existem. 

Os Aplicativos Lógicos não permitem a conexão direta com a contas de Armazenamento do Azure por meio de firewalls. Para acessar essas contas de armazenamento, use uma das opções aqui: 

* Criar um [Ambiente de Serviço de Integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que pode se conectar aos recursos em uma rede virtual do Azure. 

* Se você já usa o Gerenciamento de API, você pode usar esse serviço nesse cenário. Para obter mais informações, confira [Arquitetura Enterprise Integration simples](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Endereços IP de entrada – apenas o serviço de Aplicativos Lógicos

| Região | IP |
|--------|----|
| Leste da Austrália | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Sudeste da Austrália | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Sul do Brasil | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Canadá Central | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Leste do Canadá | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Índia Central | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Centro dos EUA | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Ásia Oriental | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| Leste dos EUA | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| Leste dos EUA 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Leste do Japão | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Oeste do Japão | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Centro-Norte dos EUA | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Norte da Europa | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| Centro-Sul dos Estados Unidos | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Sul da Índia | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sudeste Asiático | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Centro-Oeste dos EUA | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa Ocidental | 13.95.155.53, 52.174.49.6, 52.174.49.6, 52.174.54.218 |
| Índia Ocidental | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Oeste dos EUA | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Oeste dos EUA 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Sul do Reino Unido | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Oeste do Reino Unido | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Endereços IP de saída – serviço de Aplicativos Lógicos e conectores gerenciados

| Região | IP de Aplicativos Lógicos | IP de conectores gerenciados | 
|--------|---------------|-----------------------|
| Leste da Austrália | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 – 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Sudeste da Austrália | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 | 
| Sul do Brasil | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Canadá Central | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 | 
| Leste do Canadá | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 | 
| Índia Central | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 | 
| Centro dos EUA | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 | 
| Ásia Oriental | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 | 
| Leste dos EUA | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 – 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| Leste dos EUA 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 – 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Leste do Japão | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 | 
| Oeste do Japão | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 | 
| Centro-Norte dos EUA | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 – 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Norte da Europa | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 – 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| Centro-Sul dos Estados Unidos | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 | 
| Sul da Índia | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 | 
| Sudeste Asiático | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 | 
| Centro-Oeste dos EUA | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 | 
| Europa Ocidental | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 | 
| Índia Ocidental | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 | 
| Oeste dos EUA | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 | 
| Oeste dos EUA 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 | 
| Sul do Reino Unido | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15 | 
| Oeste do Reino Unido | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 | 
|||| 

## <a name="next-steps"></a>Próximas etapas  

* Saiba como [criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Saiba mais sobre [exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
