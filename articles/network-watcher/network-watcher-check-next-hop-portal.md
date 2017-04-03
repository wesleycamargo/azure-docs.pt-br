---
title: "Localizar próximo salto com o Próximo Salto do Observador de Rede do Azure - Portal do Azure | Microsoft Docs"
description: "Este artigo descreve como você pode encontrar o que é o tipo do próximo salto e o endereço ip com o próximo salto usando o Portal do Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 5fb85551d3c5fb8d1c63a965d89bae788000afe8
ms.lasthandoff: 03/31/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Descubra qual o tipo do próximo salto é usando o recurso de próximo salto no Observador de Rede do Azure usando o portal

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [API REST do Azure](network-watcher-check-next-hop-rest.md)

O Próximo salto é um recurso do Observador de Rede fornece o capacidade de obter o tipo do próximo salto e o endereço IP com base em uma máquina virtual especificada. Esse recurso é útil para determinar se o tráfego deixar uma máquina virtual atravessa um gateway, internet ou redes virtuais para chegar ao seu destino.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo usa o Próximo salto para localizar o tipo do próximo salto e o endereço IP de um recurso. Para saber mais sobre o Próximo Salto, visite [Visão geral do próximo salto](network-watcher-next-hop-overview.md).

Neste cenário, você:

* Recuperará o próximo salto de uma máquina virtual.

## <a name="get-next-hop"></a>Obter o próximo salto

### <a name="step-1"></a>Etapa 1

Navegue até seu recurso Observador de Rede no Portal do Azure.

### <a name="step-2"></a>Etapa 2

Clique em **Próximo salto** no painel de navegação, selecione a máquina virtual e a interface de rede, preencha o IP de origem e de destino e clique no botão **Próximo salto**.

> [!NOTE]
> O próximo salto exige a alocação do recurso de VM para ser executado.

![visão geral de obtenção do próximo salto][1]

### <a name="step-3"></a>Etapa 3

Após a conclusão da tarefa, os resultados serão fornecidos. O endereço IP e o tipo de dispositivo do próximo salto são exibidos. A tabela a seguir mostra os valores retornados disponíveis no portal.

**Tipo do próximo salto**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Nenhum

Se uma rota personalizada tiver sido usada para rotear esse tráfego, a UDR (rota definida pelo usuário) também será exibida com os resultados.

![resultados da obtenção do próximo salto][2]

## <a name="next-steps"></a>Próximas etapas

Aprenda a analisar as configurações de Grupo de Segurança de Rede por meio de programação visitando [NSG auditoria com o Observador de Rede](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png















