---
title: Configurar a área de trabalho Virtual do Windows balanceamento de carga de método (visualização) – Azure
description: Como configurar o método de balanceamento de carga para um ambiente de área de trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318338"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurar o método de balanceamento de carga de área de trabalho Virtual do Windows

Configurar o método de balanceamento de carga para um pool de host permite que você ajuste o ambiente de área de trabalho Virtual do Windows (visualização) para melhor se adequar às suas necessidades.

>[!NOTE]
> Isso não se aplica a um pool de host de área de trabalho persistentes como os usuários sempre têm um mapeamento de 1:1 para um host de sessão dentro do pool de host.

## <a name="configure-breadth-first-load-balancing"></a>Configurar balanceamento de carga do amplitude

Balanceamento de carga de amplitude é a configuração padrão para novos pools de host não persistente. Balanceamento de carga do amplitude distribui novas sessões de usuário em todos os hosts de sessão disponíveis no pool de host. Ao configurar o balanceamento de carga de amplitude, você pode definir um limite máximo de sessão por host de sessão no pool de host.

Primeiro, [Baixe e importe o módulo do Windows PowerShell de área de trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) usar em sua sessão do PowerShell se você ainda não fez isso.

Para configurar um pool de host para executar sem ajustar o limite máximo de sessão de balanceamento de carga de amplitude, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Para configurar um pool de host para realizar o balanceamento de carga de amplitude e usar um limite máximo de sessão nova, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurar balanceamento de carga de profundidade

Balanceamento de carga de profundidade distribui novas sessões de usuário para um host de sessão disponíveis com o maior número de conexões, mas não atingiu seu limite máximo de sessão. Ao configurar o balanceamento de carga de profundidade, você **deve** definir um limite máximo de sessão por host de sessão no pool de host.

Para configurar um pool de host para realizar o balanceamento de carga de profundidade, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
