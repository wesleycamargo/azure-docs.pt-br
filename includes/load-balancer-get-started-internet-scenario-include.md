---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52268662"
---
As tarefas a seguir serão feitas nesse cenário:

* Criar um balanceador de carga que recebe o tráfego de rede na porta 80 e enviar o tráfego com balanceamento de carga para as máquinas virtuais "web1" e "web2"
* Criar regras NAT para acesso via área de trabalho remota/SSH às máquinas virtuais por trás do balanceador de carga
* Criar investigações de integridade

![Cenário com o balanceador de carga](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
