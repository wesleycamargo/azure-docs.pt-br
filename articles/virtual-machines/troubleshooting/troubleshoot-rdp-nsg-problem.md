---
title: Não é possível se conectar-se às VMs do Azure porque a porta RDP não está habilitada no NSG | Microsoft Docs
description: Saiba como solucionar um problema no qual o RDP falha por causa da configuração do NSG no portal do Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: cb9058d4f68b2dc202edeeaa6cafb2eefa82470b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284720"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Não é possível conectar-se remotamente a uma VM porque a porta RDP não está habilitada no NSG

Este artigo explica como resolver um problema em que você não pode se conectar a uma máquina virtual do Windows Azure (VM) porque a porta de protocolo de área de trabalho remota (RDP) não está habilitada no Grupo de Segurança de Rede (NSG).


> [!NOTE] 
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../azure-resource-manager/resource-manager-deployment-model.md). É recomendável que você use o modelo de implantação do Resource Manager em vez do modelo de implantação clássico para novas implantações. 

## <a name="symptom"></a>Sintoma

Você não pode fazer uma conexão de RDP a uma VM no Azure porque a porta RDP não está aberta no grupo de segurança de rede.

## <a name="solution"></a>Solução 

Quando você cria uma nova VM, todo o tráfego da Internet é bloqueado por padrão. 

Para habilitar a porta RDP em um NSG, siga essas etapas:
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Em **Máquinas virtuais**, selecione a VM que tem o problema. 
3. Em **Configurações**, selecione **Rede**. 
4. Nas **regras de porta de entrada**, verifique se a porta do RDP está configurada corretamente. Veja a seguir um exemplo da configuração: 

    **Prioridade**: 300 </br>
    **Porta**: 3389 </br>
    **Nome**: Port_3389 </br>
    **Porta**: 3389 </br>
    **Protocolo**: TCP </br>
    **Origem**: qualquer uma </br>
    **Destinos**: qualquer um </br>
    **Ação**: permitir </br>

No que você especificar o endereço IP de origem, essa configuração permite o tráfego somente de um endereço IP específico ou intervalo de endereços IP para conectar-se à VM. Certifique-se de que o computador que está usando para iniciar a sessão RDP está dentro do intervalo.

Para saber mais sobre NSGs, confira [Grupos de Segurança de Rede](../../virtual-network/security-overview.md).

> [!NOTE]
> A porta 3389 RDP é exposta à Internet. Portanto, é recomendável que você use essa porta somente para o recomendado para testar. Para ambientes de produção, recomendamos que você use uma VPN ou conexão privada.

## <a name="next-steps"></a>Próximas etapas

Se a porta do RDP já estiver habilitada em NSG, consulte [Solucionar problemas de um erro geral de RDP na VM do Azure](./troubleshoot-rdp-general-error.md).



