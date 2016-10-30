<properties 
   pageTitle="Monitorar logs de acesso e de desempenho e métricas para o Gateway de Aplicativo | Microsoft Azure"
   description="Saiba como habilitar e gerenciar logs de acesso e de desempenho para o Application Gateway"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amitsriva" />


# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>Logs e métricas de diagnóstico do Gateway de Aplicativo

O Azure oferece a capacidade de monitorar recursos com métricas e registro em log

[**Registro em log**](#enable-logging-with-powershell) – o registro em log permite que os logs de desempenho, acesso e outros sejam salvos ou consumidos de um recurso para fins de monitoramento.

[**Métricas**](#metrics) – Atualmente, o gateway de aplicativo atualmente tem uma métrica. Essa métrica mede a taxa de transferência do gateway de aplicativo em Bytes por segundo.

Você pode usar tipos diferentes de logs no Azure para gerenciar e solucionar problemas de gateways de aplicativo. Alguns desses logs podem ser acessados por meio do portal, e todos os logs podem ser extraídos de um armazenamento de blobs do Azure e exibidos em diferentes ferramentas, como o [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), o Excel e o PowerBI. Saiba mais sobre os tipos diferentes de logs na lista a seguir:

- **Logs de auditoria:** você pode usar os [Logs de Auditoria do Azure](../azure-portal/insights-debugging-with-events.md) (anteriormente conhecidos como Logs Operacionais) para exibir todas as operações que estão sendo enviadas à sua assinatura do Azure, bem como seu status. Os logs de auditoria são habilitados por padrão e podem ser exibidos no portal de visualização do Azure.
- **Logs de acesso:** você pode usar esse log para exibir o padrão de acesso do gateway de aplicativo e analisar informações importantes, incluindo o IP do chamador, a URL solicitada, a latência de resposta, o código de retorno, os bytes de entrada e de saída. O log de acesso é coletado a cada 300 segundos. Esse log contém um registro por instância do gateway de aplicativo. A instância do gateway de aplicativo pode ser identificada pela propriedade ‘instanceId’.
- **Logs de desempenho:** você pode usar esse log para exibir o desempenho das instâncias do gateway de aplicativo. Esse log captura informações sobre o desempenho por instância, incluindo a solicitação total atendida, a taxa de transferência em bytes, o total de solicitações atendidas, a contagem de solicitações com falha, a contagem de instâncias de back-end íntegras ou não. O log de desempenho é coletado a cada 60 segundos.
- **Logs do firewall:** use esse log para exibir as solicitações registradas por meio do modo de detecção ou prevenção de um gateway de aplicativo configurado com o firewall do aplicativo Web.

>[AZURE.WARNING] Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de Recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para obter uma melhor compreensão dos dois modelos, consulte o artigo [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../resource-manager-deployment-model.md) .

## <a name="enable-logging-with-powershell"></a>Habilitar o registro em log com o PowerShell

O log de auditoria é habilitado automaticamente para todos os recursos do Gerenciador de Recursos. Você deve habilitar o log de acesso e de desempenho para começar a coletar os dados disponíveis por meio desses logs. Para habilitar o registro em log, veja as etapas a seguir: 

1. Observe a ID do Recurso da conta de armazenamento onde os dados de log são armazenados. Isso deve ter o seguinte formato: /subscriptions/\<Iddaassinatura\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>. Todas as contas de armazenamento da assinatura podem ser usadas. Você pode usar o portal de visualização para encontrar essas informações.

    ![Portal de visualização - diagnóstico do Application Gateway](./media/application-gateway-diagnostics/diagnostics1.png)

2. Anote a ID do Recurso do gateway de aplicativo para o qual o log deve ser habilitado. Isso deve ter o seguinte formato: /subscriptions/\<Iddaassinatura\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/applicationGateways/\<nome do gateway de aplicativo\>. Você pode usar o portal de visualização para encontrar essas informações.

    ![Portal de visualização - diagnóstico do Application Gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Habilite o log de diagnóstico usando o cmdlet do powershell a seguir:

        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

>[AZURE.INFORMATION] Os logs de auditoria não exigem uma conta de armazenamento separada. O uso do armazenamento para logs de acesso e de desempenho gera encargos de serviço.

## <a name="enable-logging-with-azure-portal"></a>Habilitar registro em log com o Portal do Azure

### <a name="step-1"></a>Etapa 1

Navegue até seu recurso no Portal do Azure. Clique em **Logs de diagnóstico**. Se esta for a primeira vez que você configura o diagnóstico, a folha terá a imagem:

Para o gateway de aplicativo, há três logs disponíveis.

- Log de acesso
- Log de desempenho
- Log de firewall

Clique em **Ativar diagnóstico** para iniciar a coleta de dados.

![folha de configuração de diagnóstico][1]

### <a name="step-2"></a>Etapa 2

Na folha **Configurações de diagnóstico** , as configurações de como os logs de diagnóstico estão definidos. Neste exemplo, o Log Analytics é usado para armazenar os logs. Clique em **Configurar** em **Log Analytics** para configurar seu espaço de trabalho. É possível usar Hubs de Eventos e uma conta de armazenamento para salvar os logs de diagnóstico.

![folha de diagnósticos][2]

### <a name="step-3"></a>Etapa 3

Escolha um espaço de trabalho OMS ou crie um novo. Para este exemplo, usamos um existente.

![espaços de trabalho do OMS][3]

### <a name="step-4"></a>Etapa 4

Ao concluir, confirme as configurações e clique em **Salvar** para salvar as configurações.

![confirmar seleção][4]

## <a name="audit-log"></a>Log de auditoria

Por padrão, esse log (anteriormente conhecido como "log operacional") é gerado pelo Azure.  Os logs são preservados por 90 dias no repositório de Logs de Eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir logs de eventos e de auditoria](../azure-portal/insights-debugging-with-events.md) .

## <a name="access-log"></a>Log de acesso

Esse log só será gerado se você o tiver habilitado por Application Gateway como detalhado nas etapas anteriores. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Cada acesso do Gateway de Aplicativo é registrado no formato JSON, conforme mostrado no exemplo a seguir:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayAccess",
        "time": "2016-04-11T04:24:37Z",
        "category": "ApplicationGatewayAccessLog",
        "properties": {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIP":"37.186.113.170",
            "clientPort":"12345",
            "httpMethod":"HEAD",
            "requestUri":"/xyz/portal",
            "requestQuery":"",
            "userAgent":"-",
            "httpStatus":"200",
            "httpVersion":"HTTP/1.0",
            "receivedBytes":"27",
            "sentBytes":"202",
            "timeTaken":"359",
            "sslEnabled":"off"
        }
    }

## <a name="performance-log"></a>Log de desempenho

Esse log só será gerado se você o tiver habilitado por Application Gateway como detalhado nas etapas anteriores. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Os seguintes dados são registrados em log:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayPerformance",
        "time": "2016-04-09T00:00:00Z",
        "category": "ApplicationGatewayPerformanceLog",
        "properties": 
        {
            "instanceId":"ApplicationGatewayRole_IN_1",
            "healthyHostCount":"4",
            "unHealthyHostCount":"0",
            "requestCount":"185",
            "latency":"0",
            "failedRequestCount":"0",
            "throughput":"119427"
        }
    }


## <a name="firewall-log"></a>Log de firewall

Esse log só será gerado se você o tiver habilitado por Application Gateway como detalhado nas etapas anteriores. Esse log também exige a configuração de um firewall de aplicativo Web em um gateway de aplicativo. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Os seguintes dados são registrados em log:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>Exibir e analisar o log de auditoria

Você pode exibir e analisar dados do log de auditoria usando qualquer um dos seguintes métodos:

- **Ferramentas do azure:** recupere informações dos logs de auditoria por meio do Azure PowerShell, a CLI (Interface de Linha de Comando) do Azure, a API REST do Azure ou o portal de visualização do Azure.  Instruções passo a passo para cada método são detalhadas no artigo [Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md) .
- **Power BI:** se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing) , você poderá testá-lo gratuitamente. Usando o [Pacote de conteúdo dos Logs de Auditoria do Azure para Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), você pode analisar seus dados com painéis pré-configurados que podem ser usados como estão ou podem ser personalizados.

## <a name="view-and-analyze-the-access,-performance-and-firewall-log"></a>Exibir e analisar o log de acesso, de desempenho e de firewall

O [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) do Azure pode coletar o contador e log de eventos de arquivos da sua conta de armazenamento de Blobs e inclui visualizações e recursos avançados de pesquisa para analisar os logs.

Você também pode se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de desempenho e acesso. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-lo no Excel, no PowerBI ou em qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP] Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C# , você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no Github.

## <a name="metrics"></a>Métricas

Métricas são um recurso para certos recursos do Azure, nas quais você pode exibir os contadores de desempenho no portal. Para o Gateway de Aplicativo, havia uma métrica disponível no momento da publicação deste artigo. Essa métrica é a taxa de transferência e pode ser vista no portal. Navegue até um gateway de aplicativo e clique em **Métricas**.  Selecione a taxa de transferência na seção **Métricas disponíveis** para exibir os valores. Na imagem a seguir, você pode ver um exemplo com os filtros que podem ser usados para exibir os dados em intervalos de tempo diferentes.

Para ver uma lista das métricas de suporte atuais, visite [Métricas com suporte com o Azure Monitor](../azure-portal/monitoring-supported-metrics.md)

![exibição de métrica][5]

## <a name="alert-rules"></a>Regras de alerta

Regras de alerta podem ser iniciadas com base nas métricas em um recurso. Isso significa que para o gateway de aplicativo, um alerta pode chamar um webhook ou enviar um email a um administrador se a taxa de transferência do gateway de aplicativo ficar acima, abaixo ou no limite durante um período especificado.

O exemplo a seguir guiará você pela criação de uma regra de alerta que envia um email a um administrador após um limite de taxa de transferência ter sido violado.

### <a name="step-1"></a>Etapa 1

Clique em **Adicionar alerta da métrica** para começar. Essa folha também pode ser acessada a partir da folha de métricas.

![folha de regras de alerta][6]

### <a name="step-2"></a>Etapa 2

Na folha **Adicionar regra**, preencha as seções de nome, a condição e notificação e clique em **OK** quando terminar.

O seletor **Condição** permite quatro valores, **Maior que**, **Maior ou igual a**, **Menor que** ou **Menor ou igual a**.

O seletor de **Período** permite a escolha de um período de cinco minutos até seis horas.

Selecionando **Proprietários, colaboradores e leitores de email** o email pode ser dinâmico com base nos usuários que têm acesso a esse recurso. Caso contrário, uma lista separada por vírgulas de usuários poderá ser fornecida na caixa de texto **Emails adicionais de administrador** .

![adicionar folha de regras][7]

Se o limite for ultrapassado, um email semelhante à imagem a seguir chegará:

![email de violação de limite][8]

Após a criação de um alerta de métrica uma lista de alertas será exibida e fornecerá uma visão geral de todas as regras de alerta.

![adicionar regra de alerta][9]

Para saber mais sobre notificações de alerta, visite [Receber notificações de alerta](../azure-portal/insights-receive-alert-notifications.md)

Para entender mais sobre webhooks e como usá-los com alertas, visite [Configurar um webhook em um alerta de métrica do Azure](../azure-portal/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Próximas etapas

- Visualizar o contador e logs de eventos com o [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 
- [Visualizar os logs de auditoria do Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Exibir e analisar logs de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) .

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png


<!--HONumber=Oct16_HO2-->


