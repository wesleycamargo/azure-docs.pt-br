---
title: "Gerenciar logs de fluxo de Grupo de Segurança de Rede com o Observador de Rede do Azure | Microsoft Docs"
description: "Esta página explica como gerenciar logs de fluxo de Grupo de Segurança de Rede no Observador de Rede do Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: bbea08798a601989d06774475cb25ee67e99add6
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Gerenciar logs do fluxo do Grupo de Segurança de Rede no Portal do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Logs de fluxo do Grupo de Segurança de Rede são um recurso do Observador de Rede permite que você exiba informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede. Esses logs de fluxo são escritos no formato json e mostram os fluxos de entrada e de saída por regra, a NIC à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino, Protocolo) e se o tráfego foi permitido ou negado.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

## <a name="register-insights-provider"></a>Registrar o provedor Insights

Para o registro em log de fluxo funcionar com sucesso, o provedor **Microsoft.Insights** deve ser registrado. Para registrar o provedor, navegue até **Assinaturas**, selecione a assinatura para a qual deseja habilitar os logs de fluxo. Na folha **Assinatura**, selecione **Provedores de Recursos**. Navegue pela lista de provedores e verifique se o provedor **microsoft.insights** está registrado. Se não estiver, clique em **Registrar**.

![exibir provedores][providers]

## <a name="enable-flow-logs"></a>Habilitar logs de fluxo

Estas etapas guiarão você pela ativação dos logs de fluxo em um Grupo de Segurança de Rede.

### <a name="step-1"></a>Etapa 1

Navegar até uma instância do Observador de Rede e selecione **Logs de fluxo**

![visão geral dos logs de fluxo][1]

### <a name="step-2"></a>Etapa 2

Selecione um Grupo de Segurança de Rede na lista clicando nele.

![visão geral dos logs de fluxo][2]

### <a name="step-3"></a>Etapa 3 

Na folha **Configurações dos logs do fluxo**, defina o status como **Ativado** e configure uma conta de armazenamento.  Ao concluir, clique em **OK** e **Salvar**

![visão geral dos logs de fluxo][3]

## <a name="download-flow-logs"></a>Baixar logs de fluxo

Os Logs de fluxo são salvos em uma conta de armazenamento. Para exibir os logs de fluxo é necessário baixá-los.

### <a name="step-1"></a>Etapa 1

Para baixar os Logs de fluxo, clique em **É possível baixar logs de fluxo nas contas de armazenamento configuradas**.  Isso levará você até uma exibição da conta de armazenamento onde você poderá navegar até o log para download.

![configurações dos logs do fluxo][4]

### <a name="step-2"></a>Etapa 2

Navegue até a conta de armazenamento correta e **Contêineres** > **insights-log-networksecuritygroupflowevent**

![configurações dos logs do fluxo][5]

### <a name="step-3"></a>Etapa 3

Faça o drill down até o local do log de fluxo, selecione o fluxo de log e clique em **Baixar**

![configurações dos logs do fluxo][6]

Para saber mais sobre a estrutura do log visite [Visão geral do log de fluxo de Grupo de Segurança de Rede](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Próximas etapas

Saiba como [Visualizar seus logs de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
