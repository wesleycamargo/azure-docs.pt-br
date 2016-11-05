---
title: Habilitar o monitoramento e diagnóstico no Microsoft Azure | Microsoft Docs
description: Saiba como configurar o diagnóstico para os seus recursos no Azure.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2015
ms.author: robb

---
# Habilitar monitoramento e diagnóstico
No [Portal Azure](https://portal.azure.com), você pode configurar dados de monitoramento e diagnóstico avançados e frequentes sobre seus recursos. Você também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) ou [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar diagnósticos programaticamente.

Dados de diagnóstico, monitoramento e métrica no Azure são salvos em uma conta de armazenamento de sua escolha. Isso permite que você use as ferramentas que quiser para ler os dados, desde um gerenciador de armazenamento, Power BI a ferramentas de terceiros.

## Quando criar um recurso
A maioria dos serviços permite que você habilite diagnósticos ao criá-los no [Portal Azure](https://portal.azure.com).

1. Vá para **Novo** e escolha o recurso em que você está interessado.
2. Selecione **Configuração opcional**. ![Folha de diagnósticos](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)
3. Selecione **Diagnóstico**, e clique em **Em**. Você precisa escolher a conta de armazenamento na qual deseja salvar o diagnóstico. Você pagará taxas de dados normais por armazenamento e transações ao enviar diagnóstico para uma conta de armazenamento.
4. Clique em **OK** e crie o recurso.

## Alterar configurações para um recurso existente
Se você criou um recurso e quer alterar as configurações de diagnóstico (alterar o nível de coleta de dados, por exemplo), poderá fazer isso diretamente no Portal do Azure.

1. Vá para o recurso e clique no comando **Configurações**.
2. Selecione **Diagnóstico**.
3. A folha **Diagnóstico** tem todos os dados de coleta de diagnóstico e monitoramento possíveis para esse recurso. Para alguns recursos, você também pode escolher uma política de **Retenção** de dados, para limpá-los de sua conta de armazenamento. ![Diagnóstico de armazenamento](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. Depois de escolher as configurações, clique no comando **Salvar**. Pode demorar um pouco para o monitoramento de dados aparecer se você o estiver habilitando pela primeira vez.

### Categorias de coleta de dados para máquinas virtuais
Todas as métricas e logs de máquinas virtuais serão registrados em intervalos de um minuto, para que você tenha sempre as informações mais atualizadas sobre seu computador.

* **Métricas básicas**: métricas de integridade sobre sua máquina virtual, como processador e memória
* **Métricas de rede e Web**: métricas sobre suas conexões de rede e serviços Web
* **Métricas .NET**: métricas sobre aplicativos .NET e ASP.NET em execução em sua máquina virtual
* **Métricas SQL**: se você estiver executando o Microsoft SQL Service, suas métricas de desempenho
* **Logs de aplicativo de eventos do Windows**: eventos do Windows que são enviados para o canal de aplicativos
* **Logs do sistema de eventos do Windows**: eventos do Windows que são enviados para o canal do sistema. Isso também inclui todos os eventos do [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
* **Logs de segurança de eventos do Windows**: eventos do Windows que são enviados para o canal de segurança
* **Logs de infraestrutura de diagnósticos**: registro em log sobre a infraestrutura de coleta de diagnósticos
* **Logs de IIS**: logs sobre o servidor IIS

Observe que, neste momento, determinadas distribuições do Linux não têm suporte e o agente convidado deve ser instalado na máquina virtual.

## Próximas etapas
* [Receba notificações de alerta](insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* [Monitore as métricas de serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e responsivo.
* [Dimensionar contagem de instâncias automaticamente](insights-how-to-scale.md) para garantir que o serviço seja dimensionado conforme a demanda.
* [Monitore o desempenho do aplicativo](../application-insights/app-insights-azure-web-apps.md) se você quiser compreender exatamente como seu código está sendo executado na nuvem.
* [Exiba eventos e logs de auditoria](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Acompanhe a integridade do serviço](insights-service-health.md) para saber quando o Azure sofreu interrupções de serviço ou degradação do desempenho.

<!---HONumber=AcomDC_0914_2016-->