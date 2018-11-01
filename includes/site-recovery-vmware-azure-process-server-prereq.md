---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165574"
---
Este artigo supõe que

1. A **VPN Site a Site** ou um **via expressa** já tiver sido estabelecida a conexão entre sua rede local e de rede Virtual do Azure.
2. Sua conta de usuário tem permissões para criar uma nova máquina virtual na assinatura do Azure que as máquinas virtuais foram failover em.
3. Sua assinatura tem um mínimo de 4 núcleos disponíveis para criar uma nova máquina virtual do servidor em processo.
4. Você tem o **senha do servidor de configuração** disponíveis.

> [!TIP]
> Certifique-se de que você seja capaz de conectar-se a porta 443 do servidor de configuração (em execução local) de rede Virtual do Azure que as máquinas virtuais foram failover em.
