<properties
	pageTitle="Visão Geral dos Logs de Diagnóstico do Azure | Microsoft Azure"
	description="Saiba quais são os Logs de Diagnóstico do Azure e como você pode usá-los para compreender os eventos que ocorrem dentro de um recurso do Azure."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="johnkem"/>

# Visão Geral dos Logs de Diagnóstico
**Os Logs de Diagnóstico do Azure** são logs emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. O conteúdo desses logs varia por tipo de recurso (por exemplo, os logs de eventos do sistema Windows são uma categoria de Log de Diagnóstico para as VMs, blob e tabela, e os logs da fila são categorias de Logs de Diagnóstico para as contas de armazenamento) e difere do [Log de Atividades (anteriormente conhecido como Log de Auditoria ou Operacional)](monitoring-overview-activity-logs.md), que fornece informações sobre as operações que foram executadas nos recursos em sua assinatura. Nem todos os recursos suportam o novo tipo de Logs de Diagnóstico descrito aqui. A lista de Serviços com Suporte abaixo mostra quais tipos de recursos oferecem suporte aos novos Logs de Diagnóstico.

![Posicionamento lógico dos Logs de Diagnóstico](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## O que você pode fazer com os Logs de Diagnóstico
Aqui estão algumas coisas que você pode fazer com os Logs de Diagnóstico:

- Salve-os em uma **Conta de Armazenamento** para inspeção manual ou de auditoria. Você pode especificar o tempo (em dias) de retenção usando as **Configurações de Diagnóstico**.
- [Transmita-os para os **Hubs de Eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md) para o consumo por um serviço de terceiros ou a solução de análises personalizadas, como o Power BI.

## Configurações de Diagnóstico
Os Logs de Diagnóstico para os recursos de Não Computação são configurados usando as Configurações de Diagnóstico. **Configurações de Diagnóstico** para um controle de recursos:

- Para onde os Logs de Diagnóstico são enviados (Conta de Armazenamento, Hubs de Eventos e/ou OMS).
- Quais Categorias de Log são enviadas.
- Quanto tempo cada categoria de log deve ser mantida em uma Conta de Armazenamento – uma retenção de zero dias significa que os logs são mantidos para sempre. Se as políticas de retenção são definidas, mas o armazenamento dos logs em uma Conta de Armazenamento está desabilitado (por exemplo, se apenas as opções dos Hubs de Eventos ou OMS estão selecionadas), as políticas de retenção não têm nenhum efeito.

Essas configurações são facilmente definidas via folha Diagnóstico para um recurso no Portal do Azure, via Azure PowerShell e comandos da CLI ou via [API REST do Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Os Logs de Diagnóstico e as métricas para os recursos de Computação (por exemplo, VMs ou Service Fabric) usam [um mecanismo separado para a configuração e a seleção de saídas](../azure-diagnostics.md).

## Como habilitar a coleção de Logs de Diagnóstico
A coleção de Logs de Diagnóstico pode ser habilitada como parte da criação de um recurso ou depois de um recurso ser criado via folha do recurso no Portal. Você também pode habilitar os Logs de Diagnóstico a qualquer momento usando os comandos do Azure PowerShell ou da CLI, ou usando a API REST do Insights.

> [AZURE.TIP] Essas instruções não podem ser aplicadas diretamente em cada recurso. Confira os links do esquema na parte inferior desta página para entender as etapas especiais que podem ser aplicadas em certos tipos de recursos.

[Este artigo mostra como você pode usar um modelo de recursos para habilitar as Configurações de Diagnóstico ao criar um recurso](./monitoring-enable-diagnostic-logs-using-template.md)

### Habilite os Logs de Diagnóstico no portal
Você pode habilitar os Logs de Diagnóstico no Portal do Azure ao criar alguns tipos de recursos fazendo o seguinte:

1.	Vá para **Novo** e escolha o recurso em que você está interessado.
2.	Depois de definir as configurações básicas e selecionar um tamanho, na folha **Configurações**, em **Monitoramento**, selecione **Habilitado** e escolha uma conta de armazenamento na qual você gostaria de armazenar os Logs de Diagnóstico. Você será cobrado pelas taxas de dados normais para o armazenamento e as transações quando enviar diagnósticos para uma conta de armazenamento. ![Habilitar Logs de Diagnóstico durante a criação de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.	Clique em **OK** e crie o recurso.

Para habilitar os Logs de Diagnóstico no Portal do Azure depois de um recurso ser criado, faça o seguinte:

1.	Vá para a folha do recurso e abra folha **Diagnóstico**.
2.	Clique em **Ativar** e selecione uma Conta de Armazenamento e/ou Hub de Eventos. ![Habilitar Logs de Diagnóstico depois da criação de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.	Em **Logs**, selecione quais **Categorias de Log** você gostaria de coletar ou transmitir.
4.	Clique em **Salvar**.

### Habilitar Logs de Diagnóstico programaticamente
Para habilitar os Logs de Diagnóstico via Cmdlets do Azure PowerShell, use os comandos a seguir.

Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -StorageAccountId [your storage account id] -Enabled $true

A ID da Conta de Armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar os Logs de Diagnóstico via CLI do Azure, use os comandos a seguir:

Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

A ID da Conta de Armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para alterar as Configurações de Diagnóstico usando a API REST do Insights, confira [este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## Serviços e Esquema com Suporte para os Logs de Diagnóstico
O esquema para os Logs de Diagnóstico varia dependendo do recurso e da categoria do log. Abaixo estão os serviços com suporte e seu esquema.

| O Barramento de | Esquema e Documentos |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Balanceador de Carga do Software | [Análise de log para o Balanceador de Carga do Azure (Preview)](../load-balancer/load-balancer-monitor-log.md) |
| Grupos de segurança de rede | [Análise de logs para NSGs (grupos de segurança de rede)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Gateways do Aplicativo | [Log de diagnóstico do Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Cofre da Chave | [Logs do Cofre da Chave do Azure](../key-vault/key-vault-logging.md) |
| Pesquisa do Azure | [Habilitação e uso da análise de tráfego de pesquisa](../search/search-traffic-analytics.md) |
| Repositório Data Lake | [Acessando os logs de diagnóstico do Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Análises Data Lake | [Acessando os logs de diagnóstico do Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Aplicativos Lógicos | Nenhum esquema disponível. |

## Próximas etapas
- [Transmitir Logs de Diagnóstico para os **Hubs de Eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Alterar as Configurações de Diagnóstico usando a API REST do Insights](https://msdn.microsoft.com/library/azure/dn931931.aspx)

<!---HONumber=AcomDC_0817_2016-->