---
title: Como configurar o monitoramento nos Gêmeos Digitais do Azure | Microsoft Docs
description: Como configurar o monitoramento nos Gêmeos Digitais do Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: adgera
ms.openlocfilehash: 1c8f1931a29ae9769f7d8ad57a184e3240105a1a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945801"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Como configurar o monitoramento nos Gêmeos Digitais do Azure

Os Gêmeos Digitais do Azure dão suporte a registro em log, monitoramento e análise robustos. Os desenvolvedores de soluções podem usar o Azure Log Analytics, os logs de diagnóstico, o log de atividades e outros serviços para dar suporte às necessidades complexas de monitoramento de um aplicativo de IoT. As opções de registro em log podem ser combinadas para consultar ou exibir registros em vários serviços e para fornecer cobertura de registro em log granular para muitos serviços.

Este artigo resume as opções de registro em log e monitoramento e como combiná-las de maneiras específicas para os Gêmeos Digitais do Azure.

## <a name="review-activity-logs"></a>Revisar logs de atividade

Os [logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) do Azure fornecem insights rápidos em históricos de operações e eventos de nível de assinatura para cada instância de serviço do Azure.

Os eventos de nível de assinatura incluem:

* Criação de recursos
* Remoção de recursos
* Criação de segredos do aplicativo
* Integração com outros serviços

O log de atividades dos Gêmeos Digitais do Azure é habilitado por padrão e pode ser encontrado no portal do Azure ao:

1. Selecionar sua instância de Gêmeos Digitais do Azure.
1. Escolher o **log de atividades** para exibir o painel de exibição:

    ![Log de atividades][1]

Para o registro de log de atividades avançadas:

1. Selecione a opção **Logs** para exibir a **Visão geral da Análise do Log de Atividades**:

    ![Seleção][2]

1. A **Visão geral da Análise do Log de Atividades** resume dados de log de atividade essenciais:

    ![Visão geral do Log Analytics de Atividade][3]

>[!TIP]
>Use **logs de atividade** para insights rápidos de eventos no nível da assinatura.

## <a name="enable-customer-diagnostic-logs"></a>Habilite logs de diagnóstico do cliente

As [Configurações de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) do Azure podem ser definidas para cada instância do Azure para complementar o log de atividades. Enquanto os logs de atividade pertencem aos eventos de nível de assinatura, o registro de log de diagnóstico fornece insights sobre o histórico operacional dos próprios recursos.

Exemplos de registro de log de diagnóstico incluem:

* O tempo de execução para uma função definida pelo usuário
* O código de status de resposta de uma solicitação de API bem-sucedida
* Recuperar uma chave do aplicativo de um cofre

Para habilitar logs de diagnóstico, siga estas etapas:

1. Abra os recursos no portal do Azure.
1. Clique em **Configurações de Diagnóstico**:

    ![Configurações de Diagnóstico um][4]

1. Clique em **Ativar diagnósticos** para coletar dados (se habilitado anteriormente).
1. Preencha os campos solicitados e selecione como e onde os dados serão salvos:

    ![Configurações de Diagnóstico dois][5]

    Os logs de diagnóstico geralmente são salvos usando [Armazenamento de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide) e são compartilhados com [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal). Ambas as opções podem ser selecionadas.

>[!TIP]
>Use **logs de diagnóstico** para obter insights sobre operações de recursos.

## <a name="azure-monitor-and-log-analytics"></a>Log Analytics e Azure Monitor

Aplicativos de IoT unem diferentes recursos, dispositivos, locais e dados em um só local. O registro em log refinado fornece informações detalhadas sobre cada parte, serviço ou componente específicos da arquitetura geral do aplicativo, mas uma visão geral unificada costuma ser necessária para manutenção e depuração.

O Azure Monitor inclui o poderoso serviço do Log Analytics, que permite que fontes de registro em log sejam exibidos e analisados em um único local. O Azure Monitor, portanto, é muito útil para analisar logs de dentro de aplicativos sofisticados de IoT.

Os exemplos de uso incluem:

* Consulta a várias históricos de log de diagnóstico
* Visualizar os logs para várias funções definidas pelo usuário
* Exibir os logs para dois ou mais serviços dentro de um período de tempo específico

A consulta completa de log é fornecida por meio do [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Para configurar esses recursos avançados:

1. No portal do Microsoft Azure, pesquise **Log Analytics**.
1. Você verá suas instâncias disponíveis de **Log Analytics**. Para consultar, escolha um e selecione **Logs**:

    ![Log Analytics][6]

1. Se você ainda não tiver uma instância do **Log Analytics**, é possível criar um espaço de trabalho clicando no botão **Adicionar**:

    ![Criar OMS][7]

Depois que sua instância do **Log Analytics** for provisionada, você poderá usar consultas avançadas para localizar entradas em vários logs ou pesquisar usando critérios específicos com **Gerenciamento de Log**:

   ![Gerenciamento de Log][8]

Para obter mais informações sobre as operações de consulta avançada, confira [Introdução às consultas](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

> [!NOTE]
> Pode haver um atraso de 5 minutos ao enviar eventos para o **Log Analytics** pela primeira vez.

O Azure Log Analytics também fornece os poderosos serviços de notificação de alerta e erro, que podem ser exibidos clicando **Diagnosticar e resolver problemas**:

   ![Notificações de alerta e erro][9]

>[!TIP]
>Use o **Log Analytics** para consultar históricos de log de várias funcionalidades, assinaturas e serviços de aplicativo.

## <a name="other-options"></a>Outras opções

Os Gêmeos Digitais do Azure também dão suporte a registro em log específico de aplicativo e auditoria de segurança. Para obter uma visão completa de todas as opções de registro em log do Azure disponíveis para sua instância de Gêmeos Digitais do Azure, consulte o artigo [Auditoria de log do Azure](https://docs.microsoft.com/azure/security/azure-log-audit).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Log de Atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) do Azure.

Aprofunde-se nas configurações de diagnóstico do Azure lendo uma [Visão geral dos logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

Saiba mais sobre [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal).

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png