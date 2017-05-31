---
title: "Análise de logs da CDN do Azure | Microsoft Docs"
description: "O cliente pode habilitar a análise de log para a CDN do Azure."
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: rli
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e3be8aced50a2ce330523d8633f524803af8c8a7
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---

# <a name="diagnostics-logs-for-azure-cdn"></a>Logs de Diagnóstico para a CDN do Azure

Depois de habilitar a CDN para seu aplicativo, provavelmente você desejará monitorar o uso da CDN, verificar a integridade da entrega e solucionar possíveis problemas. A CDN do Azure fornece esses recursos com a [análise principal](cdn-analyze-usage-patterns.md).

Como um usuário atual da CDN do Azure com o perfil Verizon Standard ou Premium, você já pode exibir a análise principal no portal suplementar acessível por meio da opção “Gerenciar” do Portal do Azure. Com esse novo recurso de [Logs de Diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), agora você pode exibir a análise principal e salvá-la em um ou mais destinos, incluindo a Conta de Armazenamento do Azure, hub de eventos do Azure e/ou [espaço de trabalho do Log Analytics (OMS)](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started). Esse recurso está disponível para todos os pontos de extremidade da CDN que pertencem a perfis da CDN Verizon (Standard e Premium) e Akamai (Standard).

Os logs de diagnóstico permitem que você exporte métricas de uso básicas do seu ponto de extremidade da CDN para uma variedade de fontes para que possa consumi-las de forma personalizada. Por exemplo, você pode fazer o seguinte:

- Exportar dados para o Armazenamento de Blobs, exportar para CSV e gerar gráficos no Excel.
- Exportar dados para hubs de eventos e correlacionar com os dados de outros serviços do Azure.
- Exportar dados para o Log Analytics e exibir dados no seu próprio espaço de trabalho do OMS


![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/OMS-workspace.png)

O passo a passo abaixo revisará o esquema dos dados da análise principal, as etapas envolvidas na habilitação do recurso e entrega deles a vários destinos e consumo desses destinos.

## <a name="enable-logging-with-azure-portal"></a>Habilitar registro em log com o Portal do Azure

Os logs de diagnóstico estão **desligados** por padrão. Siga as etapas abaixo para habilitá-los:


Entre no [Portal do Azure](http://portal.azure.com). Se você ainda não tiver a CDN habilitada para o fluxo de trabalho, [Habilite a CDN do Azure](cdn-create-new-endpoint.md) antes de continuar.

1. No portal, navegue até **Perfil CDN**.
2. Selecione um perfil CDN e selecione o ponto de extremidade da CDN do qual você deseja habilitar os **Logs de Diagnóstico**.
    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/Browse-to-Diagnostics-logs.png)
3. Acesse a folha **Logs de Diagnóstico** na seção **Monitoramento** e altere o status para **Ativo**.
    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/Diagnostics-logs-options.png)
4. Selecione e configure o destino de arquivamento desejado (Conta de Armazenamento, Hub de Eventos, Log Analytics). 
    
    Neste exemplo, o Armazenamento do Azure é usado para armazenar os logs, selecione **Arquivar em uma conta de armazenamento**, selecione os dias de retenção e clique em **CoreAnalytics** em **Log**. No momento, oferecemos somente **CoreAnalytics**, mas mais tipos de log chegarão no futuro. Veja abaixo informações de atraso, agregação e esquema no CoreAnalytics. 

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/Diagnostics-logs-storage.png)
5.  Salve a nova configuração de diagnóstico.
    
    Os dados de log de Verizon estão 1 hora atrasados e demoram até 2 horas para começar a aparecer após a conclusão da propagação do ponto de extremidade.
    Os dados de log de Akamai estão 24 horas atrasados e demoram até 2 horas para começar a aparecer se tiverem sido criados há mais de 24 horas. Se eles tiverem acabado de ser criados, poderá demorar até 25 horas para os logs começarem a aparecer.

## <a name="enable-logging-with-powershell"></a>Habilitar o registro em log com o PowerShell

Abaixo estão dois exemplos de como habilitar e obter Logs de Diagnóstico por meio de cmdlets do Azure PowerShell.

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>Habilitando os Logs de Diagnóstico em uma conta de armazenamento

Para habilitar os Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```

## <a name="consuming-logs-from-storage"></a>Consumindo logs do armazenamento
Esta seção descreve o esquema da análise principal da CDN, como eles são organizados dentro de uma Conta de Armazenamento do Azure e fornece código de exemplo para baixar os logs em um arquivo CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Usando o Gerenciador de Armazenamento do Microsoft Azure
Antes de poder acessar os dados da análise principal da Conta de Armazenamento do Azure, primeiro você precisa de uma ferramenta para acessar o conteúdo em uma conta de armazenamento. Embora haja várias ferramentas disponíveis no mercado, a que recomendamos é o Gerenciador de Armazenamento do Microsoft Azure. Você pode baixar a ferramenta [aqui](http://storageexplorer.com/). Depois de baixar e instalar o software, configure para usar a mesma Conta de Armazenamento do Azure que foi configurada como um destino para os Logs de Diagnóstico da CDN.

1.    Abra o **Gerenciador de Armazenamento do Microsoft Azure**
2.    Localize a conta de armazenamento
3.    Vá para o nó **"Contêineres de Blob"** nessa conta de armazenamento e expanda o nó
4.    Selecione o contêiner chamado **“insights-logs-coreanalytics”** e clique duas vezes nele
5.    Os resultados são mostrados no painel da direita começando com o primeiro nível que se parece com **“resourceId=”**. Continue clicando em todo o caminho até encontrar o arquivo **PT1H.json**. Consulte a observação abaixo para obter a explicação do caminho.
6.    Cada blob **PT1H.json** representa os logs de análise de uma hora para um ponto de extremidade da CDN específico ou seu domínio personalizado.
7.    O esquema do conteúdo desse arquivo JSON é descrito na seção Esquema dos logs de análise principal


> [!NOTE]
> **Formato de caminho de blob**
> 
> Os logs de análise principal são gerados a cada hora. Todos os dados de uma hora são coletados e armazenados em um único Blob do Azure como um conteúdo JSON. O caminho para esse Blob do Azure aparece como se houvesse uma estrutura hierárquica. Isso é porque a ferramenta de Gerenciador de Armazenamento interpreta '/' como um separador de diretório e mostra a hierarquia para sua conveniência. Na verdade, o caminho inteiro representa apenas o nome do blob. Esse nome do blob segue a convenção de nomenclatura abaixo    
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Descrição dos campos:**

|value|Descrição|
|-------|---------|
|ID da assinatura    |ID da assinatura do Azure. Esse é o formato de Guid.|
|Recurso |Nome do Grupo. Nome do grupo de recursos ao qual os recursos da CDN pertencem.|
|Nome do Perfil |Nome do perfil CDN|
|Nome do Ponto de Extremidade |Nome do ponto de extremidade da CDN|
|Ano|    Representação de 4 dígitos do ano, por exemplo, 2017|
|Mês|    Representação de 2 dígitos do número do mês. 01 = janeiro 12 = dezembro|
|Dia|    Representação de 2 dígitos do dia do mês|
|PT1H.json|    Arquivo JSON real em que os dados da análise são armazenados|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportando os dados da análise principal para um arquivo CSV

Para facilitar o acesso à análise principal, fornecemos um código de exemplo para uma ferramenta, que permite baixar os arquivos JSON em um formato de arquivo simples separado por vírgula que pode ser usado para criar facilmente gráficos ou outras agregações.

Aqui está como você pode usar a ferramenta:

1.    Visite o link do GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.    Baixar o código
3.    Siga as instruções para compilar e configurar
4.    Execute a ferramenta
5.    O arquivo CSV resultante mostra os dados de análise em uma hierarquia simples.

## <a name="diagnostic-logs-types"></a>Tipos de Logs de Diagnóstico

Atualmente, oferecemos somente os logs de análise principal que contêm as métricas mostrando estatísticas de resposta HTTP e estatísticas de saída como visto nos POPs/bordas da CDN. Ao longo do tempo, adicionaremos outros tipos de logs.

### <a name="core-analytics-metrics-details"></a>Detalhes das métricas da análise principal
Abaixo está uma lista de métricas disponíveis nos logs de análise principal. Nem todas as métricas estão disponíveis de todos os provedores, embora essas diferenças sejam mínimas. A tabela a seguir também mostra se uma determinada métrica está disponível de um provedor. Observe que as métricas estão disponíveis apenas para os pontos de extremidade da CDN que têm tráfego neles.


|Métrica                     | Descrição   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Número total de ocorrências de solicitação durante esse período| Sim  |Sim   |
| RequestCountHttpStatus2xx |Contagem de todas as solicitações que resultaram em um código HTTP 2xx (por exemplo, 200, 202)              | Sim  |Sim   |
| RequestCountHttpStatus3xx | Contagem de todas as solicitações que resultaram em um código HTTP 3xx (por exemplo, 300, 302)              | Sim  |Sim   |
| RequestCountHttpStatus4xx |Contagem de todas as solicitações que resultaram em um código HTTP 4xx (por exemplo, 400, 404)               | Sim   |Sim   |
| RequestCountHttpStatus5xx | Contagem de todas as solicitações que resultaram em um código HTTP 5xx (por exemplo, 500, 504)              | Sim  |Sim   |
| RequestCountHttpStatusOthers |  Contagem de todos os outros códigos HTTP (fora de 2xx a 5xx) | Sim  |Sim   |
| RequestCountHttpStatus200 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 200              |Não   |Sim   |
| RequestCountHttpStatus206 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 206              |Não   |Sim   |
| RequestCountHttpStatus302 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 302              |Não   |Sim   |
| RequestCountHttpStatus304 |  Contagem de todas as solicitações que resultaram em um código de resposta HTTP 304             |Não   |Sim   |
| RequestCountHttpStatus404 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 404              |Não   |Sim   |
| RequestCountCacheHit |Contagem de todas as solicitações que resultaram em uma Ocorrência no Cache. Isso significa que o ativo foi servido diretamente do POP para o cliente.               | Sim  |Não   |
| RequestCountCacheMiss | Contagem de todas as solicitações que resultaram em uma Perda do Cache. Isso significa que o ativo não foi encontrado no POP mais próximo ao cliente e, portanto, foi recuperado da Origem.              |Sim   | Não  |
| RequestCountCacheNoCache | Contagem de todas as solicitações para um ativo que são impedidas de serem armazenadas em cache devido a uma configuração do usuário na borda.              |Sim   | Não  |
| RequestCountCacheUncacheable | Contagem de todas as solicitações para ativos que são impedidas de serem armazenadas em cache pelos cabeçalhos Cache-Control e Expires do ativo, que indicam que não devem ser armazenadas em cache em um POP ou pelo cliente HTTP                |Sim   |Não   |
| RequestCountCacheOthers | Contagem de todas as solicitações com o status de cache não cobertas pelos itens acima.              |Sim   | Não  |
| EgressTotal | Transferência de dados de saída em GB              |Sim   |Sim   |
| EgressHttpStatus2xx | Transferência de dados de saída* para respostas com códigos de status HTTP 2xx em GB            |Sim   |Não   |
| EgressHttpStatus3xx | Transferência de dados de saída para respostas com códigos de status HTTP 3xx em GB              |Sim   |Não   |
| EgressHttpStatus4xx | Transferência de dados de saída para respostas com códigos de status HTTP 4xx em GB               |Sim   | Não  |
| EgressHttpStatus5xx | Transferência de dados de saída para respostas com códigos de status HTTP 5xx em GB               |Sim   |  Não |
| EgressHttpStatusOthers | Transferência de dados de saída para respostas com outros códigos de status HTTP em GB                |Sim   |Não   |
| EgressCacheHit |  Transferência de dados de saída para respostas que foram entregues diretamente do cache da CDN nos POPs/Bordas da CDN    |Sim   |  Não |
| EgressCacheMiss | Transferência de dados de saída de respostas que não foram encontradas no servidor POP mais próximo e foram recuperadas do servidor de origem              |Sim   |  Não |
| EgressCacheNoCache | Transferência de dados de saída para ativos que são impedidos de serem armazenados em cache devido a uma configuração do usuário na borda.                |Sim   |Não   |
| EgressCacheUncacheable | Transferência de dados de saída para ativos que são impedidos de serem armazenados em cache pelos cabeçalhos Cache-Control e/ou Expires do ativo, que indicam que não devem ser armazenadas em cache em um POP ou pelo cliente HTTP                    |Sim   | Não  |
| EgressCacheOthers |  Transferências de dados de saída para outros cenários de cache.             |Sim   | Não  |

*Transferência de dados de saída refere-se ao tráfego entregue de servidores POP da CDN para o cliente.


### <a name="schema-of-the-core-analytics-logs"></a>Esquema dos logs de análise principal 

Todos os logs são armazenados no formato JSON e cada entrada tem campos de cadeia de caracteres que seguem o esquema abaixo:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Em que ‘time’ representa a hora de início do limite da hora pela qual as estatísticas são relatadas. Observe que quando uma métrica não tem suporte por um provedor de CDN, em vez de um valor double ou integer, haverá um valor null. Esse valor null indica a ausência de uma métrica e isso é diferente de um valor 0. Observe também que haverá um conjunto dessas métricas por domínio configurado no ponto de extremidade.

Propriedades de exemplo abaixo:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```
## <a name="additional-resources"></a>Recursos adicionais

* [Logs de Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Análise principal por meio do portal suplementar da CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)


