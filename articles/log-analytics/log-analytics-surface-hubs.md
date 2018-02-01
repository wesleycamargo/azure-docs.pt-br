---
title: "Monitorar hubs de superfície com o Azure Log Analytics | Microsoft Docs"
description: "Use a solução Surface Hubs para controlar a integridade dos seus Hubs de superfície e compreender como eles estão sendo usados."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f56369e412bdd285d3c370f5153fee4f539dfcf
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorar Surface Hubs com o Log Analytics para acompanhar sua integridade

![Símbolo do Surface Hub](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Este artigo descreve como você pode usar a solução Surface Hub no Log Analytics para monitorar dispositivos do Microsoft Surface Hubs. O Log Analytics o ajuda a controlar a integridade dos seus Surface Hubs além de compreender como eles estão sendo usados.

Cada Surface Hub tem o Microsoft Monitoring Agent instalado. É por meio do agente que você pode enviar dados do seu Surface Hub para o Log Analytics. Os arquivos de log são lidos a partir de seus Surface Hubs e são, em seguida, enviados para o Log Analytics. Problemas como servidores offline, o calendário não está sincronizando, ou se a conta do dispositivo não puder fazer logon no Skype são mostrados no painel do Surface Hub no Log Analytics. Ao usar os dados no painel, você pode identificar os dispositivos que não estão em execução ou que têm outros problemas e, potencialmente, aplicar correções para os problemas detectados.

## <a name="install-and-configure-the-solution"></a>Instale e configure a solução
Use as informações a seguir para instalar e configurar a solução. Para gerenciar seus Surface Hubs no Log Analytics, você precisará do seguinte:

* Um nível de [assinatura do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) que dará suporte ao número de dispositivos que você deseja monitorar. Os preços do Log Analytics variam dependendo de quantos dispositivos estão registrados e a quantidade de dados que ele processa. Convém levar isso em consideração ao planejar a distribuição do Surface Hub.

Em seguida, você irá adicionar um espaço de trabalho existente do Log Analytics ou crie um. Instruções detalhadas para usar o método podem ser encontradas em [Introdução ao Log Analytics](log-analytics-get-started.md). Quando o espaço de trabalho do Log Analytics estiver configurado, há duas maneiras de registrar seus dispositivos Surface Hub:

* Automaticamente por meio do Intune
* Manualmente por meio das **configurações** em seu dispositivo do Surface Hub.

## <a name="set-up-monitoring"></a>Configurar monitoramento
Você pode monitorar a integridade e a atividade do seu Surface Hub usando o Log Analytics. Você pode registrar o Surface Hub usando o Intune ou localmente usando as **Configurações** no Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Conectar Surface Hubs ao Log Analytics através do Intune
Será necessário o ID do espaço de trabalho e a chave do espaço de trabalho para o espaço de trabalho do Log Analytics que gerenciará os Surface Hubs. Você pode obter das configurações de espaço de trabalho no portal do Azure.

O Intune é um produto da Microsoft que permite que você gerencie centralmente as definições de configuração do Log Analytics que são aplicadas a um ou mais dos seus dispositivos. Siga estas etapas para configurar seus dispositivos por meio do Intune:

1. Entre no Intune.
2. Navegue até **configurações** > **Fontes conectadas**.
3. Criar ou editar uma política com base no modelo de Surface Hub.
4. Navegue até a seção OMS (Insights operacionais do Azure) da política e adicione o *ID do espaço de trabalho* do Log Analytics e a *Chave do espaço de trabalho* à política.
5. Salve a política.
6. Associe a política ao grupo de dispositivos apropriado.

   ![Política do Intune](./media/log-analytics-surface-hubs/intune.png)

O Intune então sincroniza as configurações do Log Analytics com os dispositivos no grupo de destino, registrando-os em seu espaço de trabalho do Log Analytics.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Conectar Surface Hubs ao Log Analytics usando o aplicativo de configurações
Será necessário o ID do espaço de trabalho e a chave do espaço de trabalho para o espaço de trabalho do Log Analytics que gerenciará os Surface Hubs. Você pode obter das configurações para o espaço de trabalho do Log Analytics no portal do Azure.

Se você não usar o Intune para gerenciar seu ambiente, poderá registrar dispositivos manualmente por meio das **Configurações** em cada Surface Hub:

1. No seu Surface Hub, abra **Configurações**.
2. Insira as credenciais de administrador do dispositivo quando solicitado.
3. Clique em **Este dispositivo** e, em **Monitoramento**, clique em **Configurar definições do OMS**.
4. Selecione **Habilitar monitoramento**.
5. Na caixa de diálogo Configurações do OMS, digite o **ID do espaço de trabalho** do Log Analytics e digite a **Chave do espaço de trabalho**.  
   ![configurações](./media/log-analytics-surface-hubs/settings.png)
6. Clique em **OK** para concluir a configuração.

Uma confirmação será exibida informando se a configuração foi aplicada ou não com êxito ao dispositivo. Se foi, uma mensagem será exibida informando que o agente foi conectado com êxito ao Log Analytics. O dispositivo começará a enviar dados ao Log Analytics, onde você pode exibi-lo e trabalhar com ele.

## <a name="monitor-surface-hubs"></a>Monitorar Surface Hubs
Monitorar os Surface Hubs usando o Log Analytics é muito parecido com o monitoramento de todos os outros dispositivos registrados.

1. Entre no Portal do Azure.
2. Navegue até seu espaço de trabalho do Log Analytics e selecione **Visão geral**.
2. Clique no bloco Surface Hub.
3. A integridade do dispositivo é exibida.

   ![Painel do Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Você pode criar [alertas](log-analytics-alerts.md) com base em pesquisas de log existentes ou personalizadas. Ao usar os dados que o Log Analytics coleta de seu Surface Hubs, você pode procurar problemas e alertar sobre as condições que definir para seus dispositivos.

## <a name="next-steps"></a>Próximas etapas
* Use [Pesquisas de Log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados do Surface Hub.
* Crie [alertas](log-analytics-alerts.md) para notificá-lo quando ocorrerem problemas com seus Surface Hubs.
