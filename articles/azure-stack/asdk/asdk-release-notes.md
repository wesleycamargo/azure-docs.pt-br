---
title: Notas de versão do Kit de desenvolvimento do Microsoft Azure Stack | Microsoft Docs
description: Problemas conhecidos do Kit de desenvolvimento do Azure Stack, correções e aprimoramentos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: ee98aee0873796c2a06db73d3365e3ff9ef87ccf
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915876"
---
# <a name="asdk-release-notes"></a>Notas de versão ASDK

Este artigo fornece informações sobre problemas conhecidos no Azure Stack desenvolvimento ASDK (Kit), correções e alterações. Se você não tiver certeza de qual versão você está executando, você poderá [usar o portal para verificar](../azure-stack-updates.md#determine-the-current-version).

Mantenha-se atualizado com o que há de novo no ASDK assinando o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-1903"></a>Compilação 1903

A carga de 1903 não inclui uma versão ASDK.

## <a name="build-11902069"></a>Compilação 1.1902.0.69

### <a name="new-features"></a>Novos recursos

- A compilação de 1902 introduz uma nova interface de usuário no portal do administrador de pilha do Azure para a criação de planos, ofertas, cotas e planos de complemento. Para obter mais informações, incluindo capturas de tela, consulte [criar planos, ofertas e cotas](../azure-stack-create-plan.md).

- Para obter uma lista das outras alterações e aprimoramentos nesta versão, consulte [esta seção](../azure-stack-update-1902.md#improvements) notas de versão no Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Problemas conhecidos

- Foi identificado um problema no qual os pacotes mais 1450 bytes para um balanceador de carga interno (ILB) são descartados. O problema é devido à configuração de MTU no host que está sendo muito baixas para acomodar os pacotes VXLAN encapsulada que atravessam a função, que foi movida para o host a partir de 1901. Há pelo menos dois cenários que você pode encontrar em que vimos esse problema se manifestar:

  - Consultas SQL para SQL AlwaysOn que está por trás de um balanceador de carga interno (ILB) e são mais de 660 bytes.
  - Implantações de Kubernetes falhar se você tentar habilitar vários mestres.  

  O problema ocorre quando você tem a comunicação entre uma máquina virtual e um ILB na mesma rede virtual, mas em sub-redes diferentes. Você pode contornar esse problema, executando os seguintes comandos no prompt de comandos com privilégios elevados no host ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>Compilação 1.1901.0.95

Consulte a [informações de compilação importante nas notas de versão do Azure Stack](../azure-stack-update-1901.md#build-reference).

### <a name="changes"></a>Alterações

Esta compilação inclui os seguintes aprimoramentos para o Azure Stack:

- Agora, os componentes NAT e BGP são implantados no host físico. Isso elimina a necessidade de ter dois endereços IP públicos ou corporativos para implantar o ASDK e também simplifica a implantação.
- Sistemas que backups agora podem integrados do Azure Stack [ser validado](asdk-validate-backup.md) usando o **asdk installer.ps1** script do PowerShell.

### <a name="new-features"></a>Novos recursos

- Para obter uma lista dos novos recursos nesta versão, consulte [esta seção](../azure-stack-update-1901.md#new-features) notas de versão do Azure Stack.

### <a name="fixed-and-known-issues"></a>Fixos e problemas conhecidos

- Para obter uma lista dos problemas corrigidos nesta versão, consulte [esta seção](../azure-stack-update-1901.md#fixed-issues) notas de versão do Azure Stack. Para obter uma lista dos problemas conhecidos, consulte [esta seção](../azure-stack-update-1901.md#known-issues-post-installation).
- Observe que [hotfixes disponíveis do Azure Stack](../azure-stack-update-1901.md#azure-stack-hotfixes) não são aplicáveis para o Azure Stack ASDK.

## <a name="build-118110101"></a>Compilação 1.1811.0.101

### <a name="changes"></a>Alterações

Esta compilação inclui os seguintes aprimoramentos para o Azure Stack:  

- Há um conjunto de novo hardware mínimo e recomendado e requisitos de software para o ASDK. Estas novas recomendadas especificações estão documentadas em [considerações de planejamento de implantação do Azure Stack](asdk-deploy-considerations.md). Como a plataforma do Azure Stack evoluiu, mais serviços agora estão disponíveis e podem ser necessários mais recursos. As especificações de aumento refletem essas recomendações revisadas.

### <a name="new-features"></a>Novos recursos

Para obter uma lista dos novos recursos nesta versão, consulte [esta seção](../azure-stack-update-1811.md#new-features) notas de versão do Azure Stack.

### <a name="fixed-and-known-issues"></a>Fixos e problemas conhecidos

Para obter uma lista dos problemas corrigidos nesta versão, consulte [esta seção](../azure-stack-update-1811.md#fixed-issues) notas de versão do Azure Stack. Para obter uma lista dos problemas conhecidos, consulte [esta seção](../azure-stack-update-1811.md#known-issues-post-installation).
