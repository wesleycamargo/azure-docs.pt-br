---
title: "Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure | Microsoft Docs"
description: "Saiba como usar a telemetria da Proteção contra DDoS do Azure Standard no Azure Monitor para mitigar um ataque."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: ee418537fb52dbfb3eacca6e99d8572152a60910
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure

Saiba como habilitar e desabilitar a proteção contra DDoS (ataque de negação de serviço distribuído) e usar a telemetria para atenuar ataques contra DDoS com a Proteção contra DDoS Standard do Azure. A Proteção contra DDoS Standard protege os recursos do Azure como máquinas virtuais, balanceadores de carga e gateways de aplicativo que têm um [endereço IP público](virtual-network-public-ip-address.md) do Azure atribuído a eles. Para saber mais sobre a Proteção contra DDoS Standard e suas funcionalidades, consulte [Visão geral da Proteção contra DDoS Standard](ddos-protection-overview.md). 

>[!IMPORTANT]
>A Proteção contra DDoS do Azure Standard (Proteção contra DDoS) está atualmente em versão prévia. Um número limitado de recursos do Azure dá suporte à Proteção contra DDoS e ela está disponível apenas em um número seleto de regiões. Para obter uma lista de regiões disponíveis, consulte [Visão geral da Proteção contra DDoS Standard](ddos-protection-overview.md). Você precisa [registrar-se para o serviço](http://aka.ms/ddosprotection) durante a versão prévia limitada para habilitar a Proteção contra DDoS para a sua assinatura. Após o registro, você é contatado pela equipe do DDoS do Azure, que o orientará pelo processo de habilitação. 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Habilitar a Proteção contra DDoS Standard – nova rede virtual

1. Faça logon no Portal do Azure em http://portal.azure.com. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
2. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
3. Selecione **Rede** e, em seguida, selecione **Rede Virtual**.
4. Crie uma rede virtual com as configurações escolhidas. Para obter mais informações sobre como criar redes virtuais, consulte [Criar uma rede virtual](virtual-networks-create-vnet-arm-pportal.md). Em **Proteção contra DDoS**, clique em **Habilitado** e, em seguida, clique em **Criar**. Se você não vê **Proteção contra DDoS**, uma causa provável é que sua assinatura não está registrada para o recurso. Você deve concluir o [registro](http://aka.ms/ddosprotection)e receber uma notificação de que sua assinatura foi habilitada para o recurso antes que a **Proteção contra DDoS** seja exibida.

    ![Criar rede virtual](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > Ao selecionar uma região, escolha uma região com suporte na lista em [Visão geral da Proteção contra DDoS Standard do Azure](ddos-protection-overview.md). Se você não selecionar uma região compatível, a criação da rede virtual falhará.

    Um aviso informa que a habilitação da Proteção contra DDoS incorre em encargos. Nenhum encargo para a Proteção contra DDoS é gerado durante a versão prévia. Serão incorridos encargos na disponibilidade geral. Você receberá um aviso de 30 dias, antes do início dos encargos e da disponibilidade geral.

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>Habilitar a Proteção contra DDoS Standard – rede virtual existente 

1. Clique em **Redes virtuais** no menu do Portal do Azure e, em seguida, selecione a rede virtual.
2. Clique em **Proteção contra DDoS**, clique em **Habilitada** na tela *Proteção contra DDoS* e, em seguida, clique em **Salvar**. Se você não vê **Proteção contra DDoS**, uma causa provável é que sua assinatura não está registrada para o recurso. Você deve concluir o [registro](http://aka.ms/ddosprotection)e receber uma notificação de que sua assinatura foi habilitada para o recurso antes que a **Proteção contra DDoS** seja exibida. 

    > [!WARNING]
    > A rede virtual deve existir em uma região com suporte. Para obter uma lista de regiões com suporte, consulte [Visão geral da Proteção contra DDoS Standard do Azure](ddos-protection-overview.md).

    Um aviso informa que a habilitação da Proteção contra DDoS incorre em encargos. Nenhum encargo para a Proteção contra DDoS é gerado durante a versão prévia. Serão incorridos encargos na disponibilidade geral. Você receberá um aviso de 30 dias, antes do início dos encargos e da disponibilidade geral.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Desabilitar a Proteção contra DDoS em uma rede virtual

1. Clique em **Redes virtuais** no menu do Portal do Azure e, em seguida, selecione a rede virtual.
2. Clique em **Proteção contra DDoS**, clique em **Desabilitada** na tela *Proteção contra DDoS* e, em seguida, clique em **Salvar**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurar alertas em métricas de Proteção contra DDoS

Selecione uma das métricas da Proteção contra DDoS disponíveis para alertar quando há uma mitigação ativa durante um ataque usando a configuração de alerta do Azure Monitor. Quando as condições forem atendidas, o endereço especificado receberá um email de alerta.

1. Clique em **Monitor** e, em seguida, clique em **Métricas**.
2. Na tela *Métricas*, selecione o grupo de recursos, o tipo de recurso do **Endereço IP Público** e o endereço IP público do Azure.
3. Para configurar um alerta de email para uma métrica, clique em **Adicionar um alerta de métrica**. Um alerta de email pode ser criado em qualquer métrica, mas a métrica mais óbvia é **Sob ataque de DDoS ou não**. Esse é um valor booliano 1 ou 0. Um **1** significa que você está sob ataque. Um **0** significa que você não está sob ataque.
4. Para receber um email quando estiver sob ataque, defina a métrica para **Sob ataque de DDoS ou não** e a **Condição para Maior que zero (0) pelos últimos 5 minutos**. Alertas semelhantes podem ser configurados para outras métricas.

    ![Configurar métricas](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Em poucos minutos de detecção de ataque, você será notificado pelo uso de métricas no Azure Monitor.

    ![Alerta de ataque](./media/ddos-protection-manage-portal/ddos-alert.png) 

Você também pode aprender mais sobre [configuração de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) e [aplicativos lógicos](../logic-apps/logic-apps-overview.md) para criação de alertas.

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>Configurar o log nas métricas da Proteção contra DDoS Standard

1. Clique em **Monitor** e, em seguida, clique em **Configurações de Diagnóstico**.
2. Na tela *Métricas*, selecione o grupo de recursos, o tipo de recurso do **Endereço IP Público** e o endereço IP público do Azure.
3. Clique em **Ativar diagnóstico para coletar os dados a seguir**.

Há três opções disponíveis para registro em log:

- **Arquivar em uma conta de armazenamento**: grava os logs em uma conta de armazenamento.
- **Transmitir para um hub de eventos**: permite que um destinatário de log colete os logs usando um hub de eventos. Isso habilita a integração com o Splunk ou outros sistemas SIEM.
- **Enviar para o Log Analytics**: grava os logs no serviço Azure Log Analytics do OMS.

## <a name="use-ddos-protection-telemetry"></a>Usar a telemetria da Proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente durante o tempo pelo qual um endereço IP público está sob mitigação. Você não verá telemetria antes nem depois de um ataque ser mitigado.

1. Clique em **Monitor** e, em seguida, clique em **Métricas**. 
2. Na tela *Métricas*, selecione o grupo de recursos, o tipo de recurso do **Endereço IP Público** e o endereço IP público do Azure. Uma série de **Métricas Disponíveis** é exibida no lado esquerdo da tela. Quando selecionadas, essas métricas são mostradas no **Gráfico de Métricas do Azure Monitor** na tela de visão geral. 

Os nomes de métrica apresentam diferentes tipos de pacotes e bytes versus pacotes, com um constructo básico de nomes de marcação em cada métrica, da seguinte maneira:

- **Nome de marcação removido (por exemplo, Pacotes de Entrada Removidos por DDoS)**: o número de pacotes removidos pelo sistema de proteção contra DDoS.
- **Nome de marcação encaminhado (por exemplo, Pacotes de Entrada Encaminhados por DDoS)**: o número de pacotes encaminhados pelo sistema de DDoS para o VIP de destino – tráfego que não foi filtrado.
- **Nenhum nome de marca (por exemplo: Pacotes de Entrada de DDoS):** o número total de pacotes que entraram no sistema de depuração – que representa a soma dos pacotes ignorados e encaminhados.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analisar logs do Armazenamento do Azure com o Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)