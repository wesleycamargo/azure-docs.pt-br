---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52268694"
---
Suas máquinas virtuais (VMs) de IaaS e instâncias de função de PaaS em uma rede virtual recebem automaticamente um endereço IP privado de um intervalo especificado por você, com base na sub-rede a qual elas estão conectadas. Esse endereço é mantido pelas VMs e instâncias de função, até que elas sejam desativadas. Você pode desativar uma VM ou instância de função interrompendo-a no PowerShell, na CLI do Azure ou no portal do Azure. Nesses casos, depois que a instância de função ou a VM for iniciada novamente, ela receberá um endereço IP disponível da infraestrutura do Azure, que pode não ser o mesmo que ela tinha anteriormente. Se você desligar a VM ou instância de função do sistema operacional convidado, ela manterá o endereço IP que tinha.  

Em alguns casos, convém que uma VM ou instância de função tenha um endereço IP estático; por exemplo, se a sua VM executar DNS ou for um controlador de domínio. Você pode fazer isso definindo um endereço IP privado estático.

