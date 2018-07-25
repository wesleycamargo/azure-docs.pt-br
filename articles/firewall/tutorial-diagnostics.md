---
title: Tutorial - Monitorar os logs do Firewall do Azure
description: Neste tutorial, você aprenderá como habilitar e gerenciar logs do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991824"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>Tutorial: Monitor logs do Firewall do Azure

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Os exemplos nos artigos do Firewall do Azure supõem que você já habilitou a visualização pública do Firewall do Azure. Para obter mais informações, consulte [Habilitar a versão prévia do Firewall do Azure](public-preview.md).

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Armazenamento e Hubs de Eventos e analisados no Log Analytics ou por outras ferramentas, como Excel e Power BI.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o log por meio do portal do Azure
> * Habilitar o registro em log com o PowerShell
> * Exibir e analisar o log de atividades
> * Exibir e analisar os logs de regra de aplicativo e de rede

## <a name="diagnostic-logs"></a>Logs de diagnóstico

 Os seguintes logs de diagnóstico estão disponíveis para o Firewall do Azure:

* **Log de regra de aplicativo**

   O log de regra do aplicativo é salvo em uma conta de armazenamento, transmitido para hubs de eventos e/ou enviados ao Log Analytics somente se você tiver habilitado ele para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de aplicativo configurado gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Log de regra de rede**

   O log de regra de rede é salvo em uma conta de armazenamento, transmitido para hubs de eventos e/ou enviados ao Log Analytics somente se você tiver habilitado ele para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de rede configuradas gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Você tem três opções para armazenar os logs:

* **Conta de armazenamento**: as contas de armazenamento são mais adequadas para os logs quando eles são armazenados por mais tempo e examinados quando necessário.
* **Hubs de eventos**: os hubs de eventos são uma ótima opção para integração a outras ferramentas SEIM (informações de segurança e gerenciamento de evento) para receber alertas sobre os recursos.
* **Log Analytics**: o Log Analytics é mais adequado para o monitoramento geral em tempo real do aplicativo ou para a observação de tendências.

## <a name="activity-logs"></a>Logs de atividade

   As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure.

   Você pode usar os [logs de atividades do Azure](../azure-resource-manager/resource-group-audit.md) (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações que estão sendo enviadas à sua assinatura do Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Habilitar o log de diagnósticos por meio do portal do Azure

Pode levar alguns minutos até que os dados sejam exibidos em seus logs depois de concluir este procedimento para ativar o log de diagnósticos. Se você não vir algo a princípio, verifique novamente depois de alguns minutos.

1. No portal do Azure, abra seu grupo de recursos de firewall e clique no firewall.
2. Em **Monitoramento**, clique em **Logs de diagnóstico**.

   Para o Firewall do Azure, dois logs específicos de serviço estão disponíveis:

   * Log de regra de aplicativo
   * Log de regra de rede

3. Para iniciar a coleta de dados., clique em **Ativar diagnóstico**.
4. A página **Configurações de diagnóstico** fornece as configurações dos logs de diagnóstico. 
5. Neste exemplo, o Log Analytics armazena os logs, então digite **Firewall do log analytics** para o nome.
6. Clique em **Enviar para o Log Analytics** para configurar seu espaço de trabalho. Você também pode usar os hubs de eventos e uma conta de armazenamento para salvar os logs de diagnóstico.
7. Em **Log Analytics**, clique em **Configurar**.
8. Na página Espaços de trabalho do OMS, clique em **Criar novo espaço de trabalho**.
9. Na página **Espaço de trabalho do Log analytics**, digite **firewall-oms** para o novo nome do **Espaço de trabalho do OMS**.
10. Selecione sua assinatura, use o grupo de recursos de firewall existente (**Test-FW-RG**), selecione **Leste dos EUA** para o local e selecione o tipo de preço **gratuito**.
11. Clique em **OK**.
   ![Iniciar o processo de configuração][1]
12. Em **Log**, clique em **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule** para coletar logs de regras de aplicativo e de rede.
   ![Salvar configurações de diagnóstico][2]
13. Clique em **Salvar**.

## <a name="enable-logging-with-powershell"></a>Habilitar o registro em log com o PowerShell

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. O log de diagnósticos deve ser habilitado para começar a coletar os dados disponíveis por meio desses logs.

Para habilitar os logs de diagnóstico, realize as seguintes etapas:

1. Anote a ID do recurso da conta de armazenamento, na qual os dados de log são armazenados. Esse valor tem o formato: */subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>*.

   Use qualquer conta de armazenamento em sua assinatura. Use o portal do Azure para encontrar essas informações. As informações estão localizadas na página do recurso **Propriedades**.

2. Anote a ID do recurso Firewall para o qual o log está habilitado. Esse valor tem o formato: */subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/azureFirewalls/\<nome do Firewall\>*.

   Use o portal para encontrar essas informações.

3. Habilite o log de diagnóstico usando o seguinte cmdlet do PowerShell:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Os logs de diagnóstico não exigem uma conta de armazenamento separada. O uso do armazenamento para logs de acesso e de desempenho gera encargos de serviço.

## <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

Você pode exibir e analisar os dados do log de atividades usando um dos seguintes métodos:

* **Ferramentas do Azure**: recupere informações do log de atividades por meio do Azure PowerShell, da CLI do Azure, da API REST do Azure ou do portal do Azure. As instruções passo a passo para cada método são detalhadas no artigo [Activity operations with Resource Manager](../azure-resource-manager/resource-group-audit.md) (Operações de atividade com o Resource Manager).
* **Power BI**: se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), experimente uma gratuitamente. Com o [pacote de conteúdo dos Logs de Atividades do Azure para Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), você pode analisar seus dados com painéis pré-configurados que podem ser usados no estado em que se encontram ou ser personalizados.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Exibir e analisar os logs de regra de aplicativo e de rede

O Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) coleta os arquivos de log de eventos e o contador. Ele inclui visualizações e funcionalidades de pesquisa avançadas para analisar os logs.

Você também pode se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de desempenho e acesso. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, no Power BI ou em qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.


## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o firewall para coletar logs, você pode explorar o Log Analytics para exibir seus dados.

> [!div class="nextstepaction"]
> [Soluções de monitoramento de rede no Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
