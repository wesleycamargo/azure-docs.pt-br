---
title: Configurar o método de balanceamento de carga de visualização de área de trabalho Virtual do Windows - Azure
description: Como configurar o método de balanceamento de carga para um ambiente de área de trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328877"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Configurar o método de balanceamento de carga de visualização de área de trabalho Virtual do Windows

Configurar o método de balanceamento de carga para um pool de host permite que você ajuste o ambiente de visualização de área de trabalho Virtual do Windows para melhor se adequar às suas necessidades.

>[!NOTE]
> Isso não se aplica a um pool de host de área de trabalho persistentes como os usuários sempre têm um mapeamento de 1:1 para um host de sessão dentro do pool de host.

## <a name="configure-breadth-first-load-balancing"></a>Configurar balanceamento de carga do amplitude

Balanceamento de carga de amplitude é a configuração padrão para novos pools de host não persistente. Balanceamento de carga de amplitude distribui novas sessões de usuário em todos os hosts de sessão disponíveis no pool de host. Ao configurar o balanceamento de carga de amplitude, você pode definir um limite máximo de sessão por host de sessão no pool de host.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

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
