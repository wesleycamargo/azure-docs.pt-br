---
title: Folha de configurações do Azure Application Insights Profiler | Microsoft Docs
description: Ver o status do criador de perfil e iniciar sessões de criação de perfil
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: d96b4a99b2ea66cdeff43f06c1789dd133c2c31a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722999"
---
# <a name="configure-application-insights-profiler"></a>Configurar o Profiler do Application Insights

## <a name="profiler-settings-page"></a>Página de configurações do Profiler

A página de configurações do criador de perfil pode ser aberta na página Desempenho do Application Insights, pressionando o botão **Criador de perfil**.

![entrada no painel configurar profiler][configure-profiler-entry]

A página Configurar Perfil do Application Insights contém quatro recursos: 
1. **Perfil agora** - clicar neste botão fará com que as sessões de criação de perfil sejam iniciadas para todos os aplicativos vinculados a essa instância do Application Insights
1. **Aplicativos vinculados** - Lista de aplicativos que enviam o criador de perfil para este recurso do Application Insights
1. **Sessões em andamento** - Quando você pressiona **Perfil agora**, o status da sessão será exibido aqui)
1. **Recentes de sessões de criação de perfil** -mostra informações sobre sessões passadas de criação de perfil.

![Profiler sob demanda][profiler-on-demand]

## <a name="app-service-environments-ase"></a>Ambientes de serviço de aplicativo (ASE)
Dependendo de como seu ASE está configurado, a chamada para verificar o status do agente pode ser bloqueada. Essa página dirá que o agente não está em execução quando, na verdade, está. Você pode verificar o webjob em seu aplicativo para ter certeza. Mas se todas as configurações do aplicativo estiverem definidas corretamente e a extensão de site do App Insights estiver instalada no seu aplicativo, o criador de perfil será executado e você deverá ver as sessões de criação de perfil recentes na lista se houver tráfego adequado para seu aplicativo.

## <a id="profileondemand"></a>Disparar o Profiler manualmente

O Profiler pode ser acionado manualmente clicando em um botão. Imagine que você está executando um teste de desempenho na Web. Você precisará de rastreamentos para ajudá-lo a entender o desempenho de seu aplicativo Web sob carga. Ter controle de quando os rastreamentos são capturados é crucial, uma vez que você sabe quando o teste de carga será executado, mas o intervalo de amostragem aleatório poderá perdê-lo.
As etapas a seguir ilustram como esse cenário funciona:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Etapa 1 (opcional): Gerar tráfego para o aplicativo Web iniciando um teste de desempenho na Web

Se seu aplicativo da Web já tiver tráfego de entrada ou se você quiser gerar tráfego manualmente, pule esta seção e continue na Etapa 2.

Navegue até o portal do Application Insights, **Configurar > Teste de Desempenho**. Clique no botão Novo para iniciar um novo teste de desempenho.

![Criar novo teste de desempenho][create-performance-test]

No painel **Novo teste de desempenho**, configure a URL de destino do teste. Aceite todas as configurações padrão e comece a execução do teste de carga.

![Configurar o teste de carga][configure-performance-test]

Você verá que o novo teste é enfileirado primeiro, seguido por um status 'em andamento'.

![o teste de carga é enviado e colocado na fila][load-test-queued]

![o teste de carga está com a execução em andamento][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Etapa 2: Iniciar o criador de perfil sob demanda

Quando o teste de carga estiver em execução, poderemos iniciar o criador de perfil para capturar rastreamentos no aplicativo Web enquanto ele recebe a carga.
Navegue até o painel Configurar Profiler:


### <a name="step-3-view-traces"></a>Etapa 3: Exibir rastreamentos

Depois que o criador de perfil terminar a execução, siga as instruções na notificação para ir para a página Desempenho e exibir os rastreamentos.

## <a name="troubleshooting-on-demand-profiler"></a>Solução de problemas do criador de perfil sob demanda

Às vezes, você poderá ver uma mensagem de erro de tempo limite do Profiler após uma sessão sob demanda:

![Erro de tempo limite do Profiler][profiler-timeout]

Pode haver dois motivos para você ver este erro:

1. A sessão de criador de perfil sob demanda foi bem-sucedida, mas o Application Insights demorou muito para processar os dados coletados. Se os dados não terminarem de ser processados em 15 minutos, o portal exibirá uma mensagem de tempo limite. No entanto, após algum tempo, os rastreamentos do Profiler serão exibidos. Se isso acontecer, ignore a mensagem de erro por enquanto. Estamos trabalhando ativamente em uma correção.

1. Seu aplicativo Web tem uma versão mais antiga do agente do Profiler que não tem o recurso sob demanda. Se você tiver habilitado o perfil do Application Insights anteriormente, provavelmente que você precisará atualizar o agente do Profiler para começar a usar o recurso sob demanda.
  
Siga estas etapas para verificar e instalar o Profiler mais recente:

1. Vá até as Configurações de Aplicativo dos Serviços de Aplicativos e verifique se as seguintes configurações estão definidas:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Substitua pela chave de instrumentação correta do Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 Se alguma dessas configurações não estiver definida, acesse o painel de ativação do Application Insights para instalar a extensão de site mais recente.

1. Vá até o painel do Application Insights no portal dos Serviços de Aplicativos.

    ![Habilite o Application Insights no portal dos Serviços de Aplicativos][enable-app-insights]

1. Se você vir um botão "Atualizar" na página seguinte, clique nele para atualizar a extensão de site do Application Insights que instalará o agente mais recente do criador de perfil.

    ![Atualizar a extensão de site][update-site-extension]

1. Em seguida, clique em **alterar** para garantir que o Profiler seja ativado e selecione **OK** para salvar as alterações.

    ![Alterar e salvar o Application insights][change-and-save-appinsights]

1. Volte para a guia **Configurações do Aplicativo** do Serviço de Aplicativo para verificar novamente se os seguintes itens de configurações do aplicativo estão definidos:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Substitua pela chave de instrumentação correta do Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![Configurações de aplicativo para o criador de perfil][app-settings-for-profiler]

1. Você também pode verificar a versão da extensão e certificar-se de que não há nenhuma atualização disponível.

    ![verificar atualização da extensão][check-for-extension-update]

## <a name="next-steps"></a>Próximas etapas
[Ativar o Profiler e ver os rastreios](app-insights-profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png