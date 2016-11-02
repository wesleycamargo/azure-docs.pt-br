<properties
    pageTitle="Usar o Portal do Azure para criar alertas para os serviços do Azure | Microsoft Azure"
    description="Use o Portal do Azure para criar alertas do Azure, que podem disparar notificações ou automação quando as condições especificadas são atendidas."
    authors="rboucher"
    manager=""
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="robb"/>


# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>Usar o Portal do Azure para criar alertas para os serviços do Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md) 

## <a name="overview"></a>Visão geral

Este artigo mostra como configurar alertas do Azure usando o Portal do Azure.   

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

- **Valores da métrica** - o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.    
- **Eventos do log de atividades** - um alerta pode disparar em *cada* evento ou somente quando ocorre determinado número de eventos.


Você pode configurar um alerta para fazer o seguinte quando ele dispara: 

- enviar um email para o administrador de serviços e os coadministradores
- enviar email para outros emails que você especificar.
- chamar um webhook
- iniciar a execução de um runbook do Azure (apenas no Portal do Azure) 

Você pode configurar e obter informações sobre o uso de regras de alerta 

- [Portal do Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md) 
- [CLI (Interface de linha de comando)](insights-alerts-command-line-interface.md) 
- [API REST do Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx)
 

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o Portal do Azure

1. No [Portal](https://portal.azure.com/), localize o recurso no qual você está interessado em monitor e selecione-o.

2. Selecione **Alertas** ou **Regras de alerta** na seção MONITORAMENTO. O texto e o ícone podem variar um pouco para recursos diferentes.  

    ![Monitoramento](./media/insights-alerts-portal/AlertRulesButton.png)


3. Selecione o comando **Adicionar alerta** e preencha os campos.

    ![Adicionar alerta](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. Dê um **Nome** para o alerta de regra e escolha uma **Descrição**, que também mostre os emails de notificação.
5. Selecione a **Métrica** que você deseja monitorar, escolha uma **Condição** e um valor de **Limite** para a métrica. Escolha também o **Período** durante o qual a regra de métrica deverá ser atendida antes de o alerta disparar. Por exemplo, se você usar o período "PT5M" e o alerta procura por CPU acima de 80%, o alerta disparará quando a CPU estiver consistentemente acima de 80% por cinco minutos. Após o primeiro gatilho, ele disparará novamente quando a CPU permanecer abaixo de 80% durante cinco minutos. A medição da CPU ocorre a cada um minuto.   

6. Verifique **Proprietários de email...** se quiser que os administradores e coadministradores recebem um email quando o alerta disparar.

7. Se você quiser que outros emails recebam uma notificação quando o alerta for disparado, adicione-os ao campo **Email(s) de administrador adicionais** . Separe vários emails com ponto e vírgula - *email@contoso.com;email2@contoso.com* 

8. Coloque um URI válido no campo **Webhook** se você quiser chamá-lo quando o alerta for disparado.

9. Se você usar a Automação do Azure, selecione um Runbook a ser executado quando o alerta for disparado. 

10. Selecione **OK** ao concluir a criação do alerta.   

Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, você poderá selecioná-lo e:

- Exibir um gráfico mostrando o limite de métrica e os valores reais do dia anterior. 
- Editar ou exclui-lo. 
- **Desabilitar** ou **Habilitar** se você quiser interromper temporariamente ou continuar recebendo notificações do alerta. 



## <a name="next-steps"></a>Próximas etapas

* [Obter uma visão geral do monitoramento do Azure](monitoring-overview.md) , incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba mais sobre como [configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks da Automação do Azure](..\automation\automation-starting-a-runbook.md).
* Tenha uma [visão geral dos logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) e colete métricas detalhadas de alta frequência em seu serviço.
* Tenha uma [visão geral da coleção de métricas](insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.



<!--HONumber=Oct16_HO2-->


