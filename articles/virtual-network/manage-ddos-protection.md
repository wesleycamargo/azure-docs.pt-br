---
title: Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure | Microsoft Docs
description: Saiba como usar a telemetria da Proteção contra DDoS do Azure Standard no Azure Monitor para mitigar um ataque.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: 81f26dc72e7a1194cd7c0a5c4997e64b0f61d444
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure

Saiba como habilitar e desabilitar a proteção contra DDoS (ataque de negação de serviço distribuído) e usar a telemetria para atenuar ataques contra DDoS com a Proteção contra DDoS Standard do Azure. A Proteção contra DDoS Standard protege os recursos do Azure como máquinas virtuais, balanceadores de carga e gateways de aplicativo que têm um [endereço IP público](virtual-network-public-ip-address.md) do Azure atribuído a eles. Para saber mais sobre a Proteção contra DDoS Standard e suas funcionalidades, consulte [Visão geral da Proteção contra DDoS Standard](ddos-protection-overview.md).

Antes de concluir qualquer etapa neste tutorial, faça logon no portal do Azure em https://portal.azure.com. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção contra DDoS

Um plano de proteção contra DDoS define um conjunto de redes virtuais que têm um padrão de proteção DDoS habilitada, entre assinaturas. Você pode configurar um plano de proteção contra DDoS para sua organização e vincular redes virtuais de várias assinaturas para o mesmo plano. O plano de proteção contra DDoS também está associado a uma assinatura, que você seleciona durante a criação do plano. A assinatura do plano está associada a gerar a fatura mensal recorrente para o plano, bem como encargos excedentes, caso o número de endereços IP públicos protegidos exceder 100. Para obter mais informações sobre os preços de DDoS, consulte [Detalhes do preço](https://azure.microsoft.com/pricing/details/ddos-protection/).

A criação de mais de um plano não é necessária para a maioria das organizações. O plano não pode ser movido entre as assinaturas. Se você quiser alterar a assinatura de um plano, você precisa [excluir o plano existente](#work-with-ddos-protection-plans) e criar um novo.
 
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Pesquise por *DDoS*. Selecione **Plano de proteção contra DDoS** quando aparecer nos resultados da pesquisa.
3. Selecione **Criar**.
4. Insira ou selecione seus próprios valores, ou insira ou selecione os seguintes valores de exemplo e, em seguida, selecione **Criar**:

    |Configuração  |Valor  |
    |---------|---------|
    |NOME     | myDdosProtectionPlan         |
    |Assinatura     | Selecione sua assinatura.        |
    |Grupo de recursos     | Selecione **Criar novo** e insira *myResourceGroup*        |
    |Local padrão     | Leste dos EUA        |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Habilitar DDoS para uma nova rede virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Insira ou selecione seus próprios valores, ou insira ou selecione os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **Criar**:
 
    |Configuração  |Valor  |
    |---------|---------|
    |NOME     | myVirtualNetwork         |
    |Assinatura     | Selecione sua assinatura.        |
    |Grupo de recursos     | Clique em **Usar existente** e selecione **myResourceGroup**        |
    |Local padrão     | Leste dos EUA        |
    |Proteção contra DDoS| Selecione **Padrão** e, em seguida, em **Proteção contra DDoS**, selecione **myDdosProtectionPlan**. O plano selecionado pode estar na mesma assinatura ou em assinatura diferente que a rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.|

Você não pode mover uma rede virtual para outro grupo de recursos ou assinatura quando o DDoS padrão está habilitado para a rede virtual. Se você precisar mover uma rede virtual com um padrão de DDoS habilitado, desabilite o padrão de DDoS primeiro, mova a rede virtual e, em seguida, habilite padrão de DDoS. Após a movimentação, os limites da política ajustados automaticamente para todos os endereços IP públicos protegidos na rede virtual são redefinidos.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Habilitar DDoS para uma rede virtual existente 

1. Crie um plano de proteção contra DDoS completando as etapas em [Criar um plano de proteção contra DDoS](#create-a-ddos-protection-plan), se você não tiver um plano de proteção contra DDoS existente.
2. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
3. Insira o nome da rede virtual para a qual você deseja habilitar o padrão de proteção contra DDoS na **caixa de documentos, serviços e recursos de pesquisa** na parte superior do portal. Quando o nome da rede virtual for exibido nos resultados da pesquisa, selecione-o.
4. Selecione **Proteção contra DDoS**, em **Configurações**.
5. Selecione **Padrão**. Em **Plano de proteção contra DDoS**, selecione um plano de proteção contra DDoS existente ou o plano que você criou na etapa 1 e, em seguida, selecione **Salvar**. O plano selecionado pode estar na mesma assinatura ou em assinatura diferente que a rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Desabilitar DDoS para uma rede virtual

1. Insira o nome da rede virtual a qual você deseja desabilitar o padrão de proteção contra DDoS na **caixa de documentos, serviços e recursos de pesquisa** na parte superior do portal. Quando o nome da rede virtual for exibido nos resultados da pesquisa, selecione-o.
2. Selecione **Proteção contra DDoS**, em **Configurações**.
3. Selecione **Básico** em **Plano de proteção contra DDoS** e, em seguida, selecione **Salvar**.

## <a name="work-with-ddos-protection-plans"></a>Trabalhar com Planos de proteção contra DDoS

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *DDoS* na caixa **Filtro**. Selecione **Plano de proteção contra DDoS** quando aparecer nos resultados.
3. Selecione o plano de proteção que você deseja exibir na lista.
4. Todas as redes virtuais associadas ao plano são listadas.
5. Se você quiser excluir um plano, primeiro você deve desassociar todas as redes virtuais dele. Para desassociar um plano de uma rede virtual, consulte [Desabilitar DDoS para uma rede virtual](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurar alertas para métricas de proteção contra DDoS

Selecione uma das métricas de proteção contra DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque usando a configuração de alerta do Azure Monitor. Quando as condições forem atendidas, o endereço especificado receberá um email de alerta:

1. Selecione **Todos os serviços** na parte superior esquerda do portal. 
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Selecione **Métricas** em **Serviços Compartilhados**.
4. Insira ou selecione seus próprios valores, ou insira os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **Ok**:

    |Configuração  |Valor |
    |---------|---------|
    |NOME     | myDdosAlert        |
    |Assinatura     | Selecione a assinatura que contém o endereço IP público para o qual você deseja receber alertas.        |
    |Grupo de recursos     |  Selecione o grupo de recursos que contém o endereço IP público para o qual você deseja receber alertas.       |
    |Recurso     |    Selecione o endereço IP público que contém o endereço IP público para o qual você deseja receber alertas. DDoS monitora os endereços IP públicos atribuídos aos recursos em uma rede virtual. Se você não tiver todos os recursos com endereços IP públicos na rede virtual, você deve primeiro criar um recurso com um endereço IP público. Você pode monitorar o endereço IP público de todos os recursos implantados por meio de Gerenciador de Recursos (não clássico) listado em [Rede Virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), exceto para os Ambientes de Serviço de Aplicativo do Azure e o Gateway de VPN do Azure. Para continuar este tutorial, você pode criar rapidamente uma máquina virtual do [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).    |
    |Métrica     | Sob ataque DDoS ou não |
    |Limite     |1 - **1** significa que você está sob ataque. **0** significa que você não está sob ataque.     |
    |Período     | Selecione o valor que você escolher. |
    |Notificar por email     |  Marque a caixa de seleção       |
    |Administrador adicional | Insira seu endereço de email se você não é um proprietário de email, colaborador ou leitor para a assinatura.|

    Em poucos minutos de detecção de ataque, você receberá um email de métricas de Azure Monitor que é semelhante a figura a seguir:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque de DDoS para validar seu alerta, consulte [Validar detecção de DDoS](#validate-ddos-detection).

Você também pode aprender mais sobre [configuração de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicativos lógicos](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criação de alertas.

## <a name="configure-logging-for-ddos-protection-metrics"></a>Configurar o registro em log para métricas de proteção contra DDoS

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Em **Configurações**, selecione **Configurações de diagnóstico**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja registrar métricas.
6. Selecione **Ativar diagnóstico para coletar os dados a seguir** e, em seguida, selecione quantas opções a seguir precisar:

    - **Arquivar em uma conta de armazenamento**: os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [Arquivar os logs de diagnóstico](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos**: permite que um destinatário de log colete os logs usando um Hub de Eventos do Azure. Os Hubs de Eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [Transmitir os logs de diagnóstico para um hub de eventos](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para o Log Analytics**: grava os logs no serviço Azure Log Analytics do OMS. Para saber mais sobre essa opção, consulte [Coletar logs para uso no Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para simular um ataque de DDoS para validar o registro, consulte [Validar detecção de DDoS](#validate-ddos-detection).

## <a name="use-ddos-protection-telemetry"></a>Usar a telemetria da Proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente durante o tempo pelo qual um endereço IP público está sob mitigação. Você não vê a telemetria antes nem depois de um ataque ser mitigado.

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Selecione **Métricas** em **Serviços Compartilhados**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público para o qual você deseja a telemetria.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja a telemetria.
6. Uma série de **Métricas Disponíveis** aparecem no lado esquerdo da tela. Quando selecionadas, essas métricas são mostradas no **Gráfico de Métricas do Azure Monitor** na tela de visão geral.

Os nomes de métrica apresentam diferentes tipos de pacotes e bytes versus pacotes, com um constructo básico de nomes de marcação em cada métrica, da seguinte maneira:

- **Nome de marcação removido** (por exemplo, **Pacotes de Entrada Removidos por DDoS**): o número de pacotes removidos pelo sistema de proteção contra DDoS.
- **Nome de marcação encaminhado** (por exemplo, **Pacotes de Entrada Encaminhados por DDoS**): o número de pacotes encaminhados pelo sistema de DDoS para o VIP de destino – tráfego que não foi filtrado.
- **Nenhum nome de marca** (por exemplo: **Pacotes de Entrada de DDoS**): o número total de pacotes que entraram no sistema de depuração – que representa a soma dos pacotes ignorados e encaminhados.

Para simular um ataque de DDoS para validar a telemetria, consulte [Validar detecção de DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Exibir políticas de mitigação de DDoS

A Proteção contra DDoS padrão aplica TCP SYN, TCP e UDP (três políticas de mitigação ajustadas automaticamente) para cada endereço IP público do recurso protegido, na rede virtual que tem o DDoS habilitado. Você pode exibir os limites da política, selecionando as métricas **Pacotes de entrada TCP para ativar a mitigação de DDoS** e **Pacotes UDP de entrada para ativar a mitigação de DDoS**, conforme mostrado na figura a seguir:

![Exibir políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limites da política são configurados automaticamente por meio da nossa criação de perfil de tráfego de rede baseada em aprendizado de máquina do Azure. Somente quando o limite da política for excedido, ocorre a mitigação de DDoS para o endereço IP sob ataque.

## <a name="validate-ddos-detection"></a>Validar detecção de DDoS

A Microsoft firmou parceria com [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface em que você pode gerar tráfego contra endereços IP públicos com a Proteção contra DDoS habilitada para fins de simulação. A simulação do BreakPoint Cloud permitirá que você:

- Valide como a Proteção contra DDoS do Microsoft Azure protege seus recursos do Azure contra ataques de DDoS
- Otimize o processo de resposta a incidentes durante ataques de DDoS
- Documente a conformidade de DDoS
- Treine suas equipes de segurança de rede