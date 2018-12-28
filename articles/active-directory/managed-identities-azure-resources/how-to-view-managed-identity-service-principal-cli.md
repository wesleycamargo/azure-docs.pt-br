---
title: Como exibir a entidade de serviço de uma identidade gerenciada usando CLI do Azure
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando CLI do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: c1e09ac91055cc02fa0ab5c2f4576d6ed6a6d3e7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884415"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Exibir a entidade de serviço de uma identidade gerenciada usando CLI do Azure

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. É possível usar essa identidade para autenticar qualquer serviço que dê suporte à autenticação do Azure AD, sem a necessidade de ter credenciais em seu código. 

Neste artigo, você aprende como exibir a entidade de serviço de uma identidade gerenciada usando CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md).
- Caso ainda não tenha uma conta do Azure, [inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- Habilite a [identidade designada pelo sistema em uma máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicativo](/azure/app-service/app-service-managed-service-identity#adding-a-system-assigned-identity).
- Para executar os exemplos de script da CLI, você tem três opções:
    - Usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
    - Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
    - [Instale a última versão da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se você preferir usar um console de CLI local e entrar no Azure usando `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

Este comando a seguir demonstra como exibir a entidade de serviço de uma VM ou aplicativo com identidade gerenciada habilitada. Substitua `<VM or application name>` pelos seus próprios valores. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como gerenciar entidades de serviço do Azure AD usando CLI do Azure, consulte [az ad sp](/cli/azure/ad/sp).


