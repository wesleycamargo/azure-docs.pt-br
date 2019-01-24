---
title: Visão Geral dos Logs de Diagnóstico
description: Saiba quais são os logs de diagnóstico do Azure e como você pode usá-los para compreender os eventos que ocorrem dentro de um recurso do Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 44950a467e99fb7968d838345d80c8e28aee62b9
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467461"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Coletar e consumir dados de log dos recursos do Azure

## <a name="what-are-azure-monitor-diagnostic-logs"></a>O que são logs de diagnóstico do Monitor do Azure

**Os logs de diagnóstico do Monitor do Azure** são registros emitidos por um serviço do Azure que fornecem dados ricos e frequentes sobre a operação desse serviço. O Monitor do Azure disponibiliza dois tipos de logs de diagnóstico:
* **Registros de locatário** - esses registros são provenientes de serviços no nível de inquilino que existem fora de uma assinatura do Azure, como os logs do Active Directory do Azure.
* **Registros de recursos** - esses registros são provenientes dos serviços do Azure que implantam recursos em uma assinatura do Azure, como Grupos de segurança de rede ou Contas de armazenamento.

    ![Logs de diagnóstico do recurso versus outros tipos de logs ](./media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

O conteúdo desses registros varia de acordo com o tipo de recurso e serviço do Azure. Por exemplo, os contadores de regras do Network Security Group e as auditorias do Key Vault são dois tipos de logs de diagnóstico.

Esses logs diferem de [Log de atividades](activity-logs-overview.md). O Log de Atividades fornece informações sobre as operações executadas em recursos em sua assinatura usando o Gerenciador de Recursos, por exemplo, criar uma máquina virtual ou excluir um aplicativo lógico. O Log de Atividades é um log no nível da assinatura. Os logs de diagnóstico no nível do recurso fornecem informações sobre as operações executadas dentro do próprio recurso, por exemplo, obtenção de um segredo de um Key Vault.

Esses logs também diferem dos logs de diagnóstico no nível do SO guest. Os logs de diagnóstico do sistema operacional convidado são aqueles coletados por um agente em execução dentro de uma máquina virtual ou outro tipo de recurso com suporte. Os logs de diagnóstico no nível do recurso não exigem um agente e capturam dados específicos ao recurso da própria plataforma do Azure, enquanto os logs de diagnóstico no nível do sistema operacional convidado capturam dados do sistema operacional e de aplicativos em execução em uma máquina virtual.

Nem todos os serviços suportam os logs de diagnóstico descritos aqui. [Este artigo contém uma lista de seção quais serviços oferecem suporte a logs de diagnóstico](./../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>O que você pode fazer com os Logs de Diagnóstico
Aqui estão algumas coisas que você pode fazer com os Logs de Diagnóstico:

![Posicionamento lógico dos Logs de Diagnóstico](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Salve-os em uma [**Conta de Armazenamento**](../../azure-monitor/platform/archive-diagnostic-logs.md) para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as **Configurações de Diagnóstico do Recurso**.
* [Transmita-os para os **Hubs de Eventos**](diagnostic-logs-stream-event-hubs.md) para o consumo por um serviço de terceiros ou uma solução de análises personalizadas, como o PowerBI.
* Analise-os com o [Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md), onde os dados são gravados imediatamente para registar o Log Analytics com a necessidade de gravar primeiro os dados para o armazenamento.  

Você pode usar uma conta de armazenamento ou um namespace de Hubs de Evento que não esteja na mesma assinatura que os logs emissores. O usuário que define a configuração deve ter o devido acesso RBAC para ambas as assinaturas.

> [!NOTE]
>  Atualmente, não é possível arquivar logs de fluxo de rede para uma conta de armazenamento que esteja atrás de uma rede virtual protegida.

> [!WARNING]
> O formato dos dados de log na conta de armazenamento será alterado para Linhas JSON em 1º de novembro de 2018. [Confira este artigo para obter uma descrição do impacto e saber como atualizar suas ferramentas para manipular o novo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

## <a name="diagnostic-settings"></a>Configurações de Diagnóstico

Logs de diagnóstico de recursos são configurados usando configurações de diagnóstico de recursos. Os logs de diagnóstico do inquilino são configurados usando uma configuração de diagnóstico de locatário. **Configurações de Diagnóstico** para um controle de recursos:

* Para onde os registros e métricas de diagnóstico são enviados (Conta de Armazenamento, Hubs de Eventos e / ou Log Analytics).
* Quais categorias de log são enviadas e se os dados de métrica também são enviados.
* Quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento
    - Uma retenção de zero dias significa que os registros serão mantidos indefinidamente. O valor pode ser qualquer quantidade de dias, entre 1 e 2147483647.
    - Se as políticas de retenção estiverem definidas, mas o armazenamento de logs em uma Conta de Armazenamento estiver desabilitado (por exemplo, se apenas as opções Hubs de Eventos ou Log Analytics forem selecionadas), as políticas de retenção não terão efeito.
    - As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos. A exclusão começa à meia-noite UTC, mas observe que pode levar até 24 horas para que os logs sejam excluídos da conta de armazenamento.

Essas configurações são facilmente configuradas por meio das configurações de diagnóstico no portal, via comandos do Azure PowerShell e CLI ou por meio da [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
>
> *Por exemplo*: A métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Como habilitar a coleção de Logs de Diagnóstico

A coleta de logs de diagnóstico pode ser ativada [como parte da criação de um recurso em um modelo do Resource Manager](./../../azure-monitor/platform/diagnostic-logs-stream-template.md) ou depois que um recurso é criado a partir da página desse recurso no portal. Você também pode habilitar a coleção a qualquer momento usando os comandos do Azure PowerShell ou da CLI, ou usando a API REST do Azure Monitor.

> [!TIP]
> Essas instruções não podem ser aplicadas diretamente em cada recurso. Confira os links do esquema na parte inferior desta página para entender as etapas especiais que podem ser aplicadas em certos tipos de recursos.

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>Ativar coleta de logs de diagnóstico no portal

Você poderá habilitar a coleta de logs de diagnóstico de recursos no portal do Azure depois que um recurso tiver sido criado, acessando um recurso específico ou navegando até o Azure Monitor. Para habilitar isso por meio do Azure Monitor:

1. No [portal do Azure](https://portal.azure.com), navegue até o Azure Monitor e clique em **Configurações de Diagnóstico**

    ![Seção de monitoramento do Azure Monitor](media/diagnostic-logs-overview/diagnostic-settings-blade.png)

2. Se desejar filtrar a lista por tipo de recurso ou grupo de recursos, clique no recurso para o qual você deseja definir uma configuração de diagnóstica.

3. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em “Ativar diagnóstico”.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/diagnostic-logs-overview/diagnostic-settings-none.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já definidas nesse recurso. Clique em "Adicionar configuração de diagnóstico".

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-logs-overview/diagnostic-settings-multiple.png)

3. Dê um nome para sua configuração, marque as caixas para cada destino ao qual você gostaria de enviar dados e configure o recurso usado para cada destino. Opcionalmente, defina um número de dias para manter esses logs usando os controles deslizantes **Retenção (dias)** (aplicáveis somente ao destino da conta de armazenamento). Uma retenção de zero dias armazena os logs indefinidamente.

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-logs-overview/diagnostic-settings-configure.png)

4. Clique em **Salvar**.

Após alguns instantes, a nova configuração aparece na lista de configurações para esse recurso e os logs de diagnóstico são enviados para os destinos especificados assim que os novos dados de evento são gerados.

Configurações de diagnóstico de locatário só podem ser configuradas na folha do portal para o serviço de locatário – essas configurações não aparecem na folha de configurações de diagnóstico do Azure Monitor. Por exemplo, os logs de auditoria do Active Directory do Azure são configurados, clicando na **configurações de exportação de dados** na folha Logs de auditoria.

![Configurações de diagnóstico do AAD](./media/diagnostic-logs-overview/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Habilitar a coleção de logs de diagnóstico de recurso via PowerShell

Para habilitar a coleção de logs de diagnóstico de recurso via Azure PowerShell, use os comandos a seguir:

Para habilitar o armazenamento dos logs de diagnóstico em uma conta de armazenamento, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

Para habilitar o streaming dos logs de diagnóstico para um hub de eventos, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

A ID da regra do barramento de serviço é uma cadeia de caracteres com este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para habilitar o envio dos logs de diagnóstico para um workspace do Log Analytics, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

É possível obter a ID de recurso do seu workspace do Log Analytics usando o seguinte comando:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Você pode combinar esses parâmetros para permitir várias opções de saída.

No momento, é possível configurar definições de diagnóstico de locatário usando o Azure PowerShell.

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Habilitar a coleção de logs de diagnóstico de recursos por meio da CLI do Azure

Para habilitar a coleção de logs de diagnóstico de recursos por meio da CLI do Azure, use o comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

Para habilitar o armazenamento de logs de diagnóstico em uma Conta de Armazenamento:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

O argumento `--resource-group` somente será necessário se `--storage-account` não for uma ID de objeto.

Para habilitar o fluxo de logs de diagnóstico para um Hub de Eventos:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

A ID da regra é uma cadeia de caracteres com este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para habilitar o envio de logs de diagnóstico para um workspace do Log Analytics:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

O argumento `--resource-group` somente será necessário se `--workspace` não for uma ID de objeto

Com qualquer comando, é possível adicionar categorias adicionais ao log de diagnóstico, adicionando dicionários à matriz JSON transmitida como o parâmetro `--logs`. Você pode combinar `--storage-account`, `--event-hub` e `--workspace` para habilitar várias opções de saída.

Atualmente, você não pode definir as configurações de diagnóstico do inquilino usando a CLI.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Habilitar a coleção de logs de diagnóstico do recurso via API REST

Para alterar as configurações de diagnóstico usando a API REST do Azure Monitor, confira [este documento](https://docs.microsoft.com/rest/api/monitor/).

No momento, você não pode configurar configurações de diagnóstico de locatário usando a API REST do Azure Monitor.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gerenciar configurações de diagnóstico de recurso no Portal

Verifique se todos os seus recursos estão definidos com as configurações de diagnóstico. Navegue até **Monitor** no portal e abra **Configurações de diagnóstico**.

![Folha Logs de Diagnóstico no portal](./media/diagnostic-logs-overview/diagnostic-settings-nav.png)

Você talvez tenha que clicar em "Todos os serviços" para localizar a seção Monitoramento.

Ali, você pode exibir e filtrar todos os recursos que dão suporte a configurações de diagnóstico para ver se eles têm o diagnóstico habilitado. Você também pode fazer uma busca detalhada para ver se várias configurações estão definidas em um recurso e verificar para qual conta de armazenamento, namespace de Hubs de Eventos e/ou workspace do Log Analytics os dados estão fluindo.

![Resultados da folha Logs de Diagnóstico no portal](./media/diagnostic-logs-overview/diagnostic-settings-blade.png)

A adição de uma configuração de diagnóstico mostra a exibição Configurações de Diagnóstico, onde você pode habilitar, desabilitar ou modificar as configurações de diagnóstico para o recurso selecionado.

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Serviços com suporte, categorias e esquemas para logs de diagnóstico

[Consulte este artigo](../../azure-monitor/platform/tutorial-dashboards.md) para obter uma lista de serviços com suporte e as categorias de log e os esquemas usados por esses serviços.

## <a name="next-steps"></a>Próximas etapas

* [Transmitir logs de diagnóstico de recurso os **Hubs de Eventos**](diagnostic-logs-stream-event-hubs.md)
* [Alterar as configurações de diagnóstico do recurso usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)

