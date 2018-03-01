---
title: "Criar um espaço de trabalho no Azure Log Analytics | Microsoft Docs"
description: "Saiba como criar um espaço de trabalho do Log Analytics para habilitar as soluções de gerenciamento e a coleta de dados da sua nuvem e dos ambientes locais."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: magoedte
ms.openlocfilehash: 5d8b20d5da442aa1f37eb7e2b2cb8049031e7a24
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar um espaço de trabalho do Log Analytics no Portal do Azure
No Portal do Azure, você pode configurar um espaço de trabalho do Log Analytics, que é um ambiente exclusivo do Log Analytics com seu próprio repositório de dados, fontes de dados e soluções.  As etapas descritas neste artigo serão necessárias se você pretender coletar dados das seguintes fontes:

* Recursos do Azure em sua assinatura
* Computadores locais monitorados pelo System Center Operations Manager
* Coleções de dispositivos do System Center Configuration Manager 
* Dados de diagnóstico ou de log do armazenamento do Azure

Para outras fontes, como as VMs do Azure e os computadores com Windows ou com Linux em seu ambiente, consulte os tópicos a seguir:

*  [Coletar dados sobre as Máquinas Virtuais do Azure](log-analytics-quick-collect-azurevm.md) 
*  [Coletar dados sobre os computadores com Linux](log-analytics-quick-collect-linux-computer.md)
*  [Coletar dados sobre os computadores com Windows](log-analytics-quick-collect-windows-computer.md)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure-portal"></a>Fazer logon no portal do Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar um espaço de trabalho
1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.<br><br> ![portal do Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Clique em **Criar** e, em seguida, selecione opções para os seguintes itens:

  * Forneça um nome para o novo **Espaço de Trabalho do OMS**, como *DefaultLAWorkspace*. 
  * Selecione uma **Assinatura** a vincular escolhendo uma na lista suspensa, se a selecionada por padrão não é adequada.
  * Para o **Grupo de Recursos**, opte por usar um grupo de recursos existente já configurado ou criar um novo.  
  * Selecione um **local** disponível.  Para obter mais informações, consulte em quais [regiões o Log Analytics está disponível](https://azure.microsoft.com/regions/services/).
  * Você pode escolher um de três diferentes **tipos de preço** no Log Analytics, mas para este guia de início rápido, você vai selecionar o preço **gratuito**.  Para obter mais informações sobre os tipos específicos, consulte [Detalhes de preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Depois de fornecer as informações necessárias no painel **Espaço de Trabalho do OMS**, clique em **OK**.  

Enquanto as informações são verificadas e o espaço de trabalho é criado, você pode acompanhar seu progresso no menu **Notificações**. 

## <a name="next-steps"></a>Próximas etapas
Agora que já tem um espaço de trabalho disponível, você pode configurar a coleta de monitoramento de telemetria, executar pesquisas de logs para analisar os dados e adicionar uma solução de gerenciamento para fornecer dados adicionais e informações analíticas. 

* Para habilitar a coleta de dados de recursos do Azure com o Diagnóstico do Azure ou com o armazenamento do Azure, consulte [Coletar logs e as métricas do serviço do Azure para uso no Log Analytics](log-analytics-azure-storage.md).  
* [Adicione o System Center Operations Manager como uma fonte de dados](log-analytics-om-agents.md) para coletar dados de agentes que se reportam ao seu grupo de gerenciamento do Operations Manager e armazene-o em seu repositório de espaço de trabalho do Log Analytics. 
* Conecte-se ao [Configuration Manager](log-analytics-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de gerenciamento](/log-analytics-add-solutions.md) disponíveis e como adicionar ou remover uma solução do espaço de trabalho.

