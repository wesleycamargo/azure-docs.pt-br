---
title: Migrar do ponto de extremidade do Azure para os testes de disponibilidade do Application Insights
description: "Migrando os seus testes clássicos de monitoramento de ponto de extremidade do Azure para os testes de disponibilidade do Application Insights até 31 de outubro de 2016."
services: application-insights
documentationcenter: 
author: soubhagyadash
manager: douge
ms.assetid: 74cc1ddf-1b70-4ad2-bdb5-1bd895937861
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 9b3cf88473cd3f4e4e5a383dfdadfe06d4f34f36


---
# <a name="moving-from-azure-endpoint-monitoring-to-application-insights-availability-tests"></a>Mudando do monitoramento do ponto de extremidade do Azure para os testes de disponibilidade do Application Insights
Você usa o [Monitoramento de ponto de extremidade](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/) para seus aplicativos do Azure? Até *31 de outubro de 2016*, substituiremos esse recurso pelos novos e mais avançados [Testes de disponibilidade](app-insights-monitor-web-app-availability.md). Já criamos alguns dos novos testes, mas eles permanecerão desabilitados até 31 de outubro de 2016. 

É possível editar os novos testes e fazer a modificação você mesmo, se quiser. Você pode encontrá-los no grupo de recursos Default-ApplicationInsights-CentralUS no [portal do Azure](https://portal.azure.com) .

## <a name="what-are-availability-tests"></a>O que são testes de disponibilidade?
Os testes de disponibilidade são um recurso do Azure que verifica continuamente se qualquer serviço ou site está funcionando ao enviar solicitações HTTP a ele (testes únicos de ping ou testes na Web do Visual Studio) de até 16 locais no mundo todo. 

No [Portal clássico do Azure](https://manage.windowsazure.com), esses testes eram chamados de Monitoramento de Ponto de Extremidade. O escopo deles era mais limitado. Os novos testes de disponibilidade apresentam uma melhoria significativa:

* Até 10 testes na Web do Visual Studio ou testes de ping por recurso do Application Insights. 
* Até 16 locais no mundo todo para enviar solicitações de teste ao seu aplicativo Web. Maior controle dos critérios de sucesso de testes. 
* Teste de qualquer serviço ou site da Web - não apenas aplicativos Web do Azure.
* Repetições de teste: redução de alertas de falsos positivos devido a problemas de rede temporários. 
* Os webhooks podem receber notificações de HTTP POST para os alertas.

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

Leia mais sobre [testes de disponibilidade aqui](app-insights-monitor-web-app-availability.md).

Os testes de disponibilidade fazem parte do [Azure Application Insights](app-insights-overview.md), que é um serviço de análise extensível para qualquer aplicativo Web.

## <a name="so-whats-happening-to-my-endpoint-tests"></a>O que está acontecendo com meus testes de ponto de extremidade?
* Copiamos seus testes de monitoramento de ponto de extremidade para os novos testes de disponibilidade do Application Insights.
* Os novos testes de disponibilidade estão desabilitados no momento e os testes de Ponto de Extremidade antigos ainda estão funcionando.
* As regras de alertas *não* foram migradas. Os novos testes, inicialmente, foram configurados com uma regra padrão:
  * Dispara quando mais de um local relata falhas em cinco minutos.
  * Envia um email ao administrador da assinatura.

No [portal do Azure](https://portal.azure.com), você pode encontrar os testes migrados no grupo de recursos "Default-ApplicationInsights-CentralUS". Os nomes do teste são prefixados com "Migrated-". 

## <a name="what-do-i-need-to-do"></a>O que preciso fazer?
* Se de alguma maneira não migramos seus testes, os novos testes de disponibilidade são [fáceis de configurar](app-insights-monitor-web-app-availability.md).

### <a name="option-a-do-nothing-leave-it-to-us"></a>Opção A: não fazer nada. Deixe conosco.
**Em 31 de outubro de 2016**, nós iremos:

* Desabilitar os testes de ponto de extremidade antigos.
* Habilitar os testes de disponibilidade migrados.

### <a name="option-b-you-manage-andor-enable-the-new-tests"></a>Opção B: você gerencia e/ou habilita os novos testes.
* Examine e edite os novos testes de disponibilidade no novo [portal do Azure](https://portal.azure.com). 
  * Examine os critérios de disparo
  * Examine os destinatários de email
* Habilite os novos testes
* Vamos desabilitar os testes de monitoramento de ponto de extremidade herdados em 31 de outubro de 2016 

### <a name="option-c-opt-out"></a>Opção C: recusar
Se você não quer usar os testes de disponibilidade, é possível excluí-los no [portal do Azure](https://portal.azure.com). Também há um link Cancelar assinatura no fim dos emails de notificação.

Mesmo assim, excluiremos os testes de ponto de extremidade antigos em 31 de outubro de 2016. 

## <a name="how-do-i-edit-the-new-tests"></a>Como edito os novos testes?
Entre no [portal do Azure](https://portal.azure.com) e encontre os testes na Web 'Migrated-': 

![Escolha Grupos de Recursos, Default-ApplicationInsights-CentralUS e abra os testes 'migrated'.](./media/app-insights-migrate-azure-endpoint-tests/20.png)

Edite e/ou habilite o teste:

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)

## <a name="why-is-this-happening"></a>Por que isso está acontecendo?
Melhoria do serviço. O serviço de ponto de extremidade antigo era muito mais limitado. Você podia fornecer apenas duas URLs para testes simples de ping de 3 locais geográficos em uma VM ou um aplicativo Web do Azure. Os novos testes podem executar testes na Web de várias etapas de até 16 locais, e você pode especificar até 10 testes para um aplicativo. Você pode testar qualquer URL - não precisa ser um site do Azure.

Os novos testes são configurados separadamente do aplicativo Web ou da VM que você está testando. 

Estamos migrando os testes para garantir que você continue tendo controle sobre eles usando o novo portal. 

## <a name="what-is-application-insights"></a>O que é o Application Insights?
Os novos testes de disponibilidade fazem parte do [Azure Application Insights](app-insights-overview.md). Veja um [vídeo de dois minutos](http://go.microsoft.com/fwlink/?LinkID=733921).

## <a name="am-i-paying-for-the-new-tests"></a>Estou pagando pelos novos testes?
Os testes migrados são configurados em um recurso do Application Insights no plano Gratuito padrão. Isso permite a coleta de até cinco milhões de pontos de dados, que facilmente cobrem o volume de dados que seus testes usam atualmente. 

É claro que, se você gostar do Application Insights e criar mais testes de disponibilidade ou adotar mais de seus recursos de diagnóstico e de monitoramento de desempenho, vai gerar mais pontos de dados.  No entanto, o resultado seria você atingir a cota do plano Gratuito. Você não receberá uma fatura, a menos que opte pelos planos Standard ou Premium. 

[Saiba mais sobre os preços e o monitoramento de cotas do Application Insights](app-insights-pricing.md). 

## <a name="what-is-and-isnt-migrated"></a>O que é e não é migrado?
São preservados dos seus testes antigos de ponto de extremidade:

* A URL de ponto de extremidade a ser testada.
* Os locais geográficos dos quais as solicitações são enviadas.
* A frequência do teste permanece por 5 minutos.
* O tempo limite do teste permanece por 30 segundos. 

Não migrados:

* Regra de gatilho de alerta. A regra que configuramos é disparada quando um local relata falhas em cinco minutos.
* Os destinatários do alerta. Os emails de notificação serão enviados aos proprietários e aos coproprietários da assinatura. 

## <a name="how-do-i-find-the-new-tests"></a>Como encontro os novos testes?
Você pode editar qualquer dos testes novos, se quiser. Entre no [Portal do Azure](https://portal.azure.com), abra **Grupos de Recursos** e selecione **Default-ApplicationInsights-CentralUS**. Nesse grupo, você encontrará os novos testes da Web. [Saiba mais sobre os novos testes de disponibilidade](app-insights-monitor-web-app-availability.md).

Observe que os novos emails de alerta serão enviados deste endereço: Alertas do Application Insights (ai-noreply@microsoft.com)

## <a name="what-happens-if-i-do-nothing"></a>O que acontece se eu não fizer nada?
A Opção A será aplicada. Vamos habilitar os testes migrados e configurar as regras de alerta padrão, conforme mencionado acima. Você terá que adicionar as regras de alerta personalizadas e destinatários, conforme mencionado acima. Vamos desabilitar os testes de monitoramento de ponto de extremidade herdados. 

## <a name="where-can-i-provide-feedback-on-this"></a>Onde posso fazer comentários sobre isso?
Agradecemos os seus comentários. [Envie-nos um email](mailto:vsai@microsoft.com). 




<!--HONumber=Nov16_HO3-->


