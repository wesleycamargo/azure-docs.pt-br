---
title: Como atribuir um acesso de identidade gerenciada a um recurso do Azure usando o portal do Azure
description: Instruções passo a passo para atribuir uma identidade gerenciada em um acesso de recurso a outro recurso, usando o portal do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: ae01087e0430049036da74a36d6155826c02aa00
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436358"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de identidade gerenciada a um recurso usando o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Após configurar um recurso do Azure com uma identidade gerenciada, será possível conceder o acesso de identidade gerenciada a outro recurso, assim como qualquer entidade de segurança. Este artigo mostra como conceder acesso de identidade gerenciada de uma máquina virtual do Azure ou conjunto de dimensionamento de máquinas virtuais a uma conta de armazenamento do Azure, usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Use RBAC para atribuir um acesso de identidade gerenciada a outro recurso

Após habilitar a identidade gerenciada em um recurso do Azure, como uma [VM do Azure](qs-configure-portal-windows-vm.md) ou [VMSS do Azure](qs-configure-portal-windows-vmss.md):

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure na qual você configurou a identidade gerenciada.

2. Navegue até o recurso desejado no qual você deseja modificar o controle de acesso. Neste exemplo, estamos fornecendo um acesso à máquina virtual do Azure para uma conta de armazenamento, portanto, navegamos até a conta de armazenamento.

3. Selecione a página **IAM (Controle de Acesso)** do recurso e selecione **+ Adicionar atribuição de função**. Em seguida, especifique a **Função**, **Atribuir acesso**  e especifique a **Assinatura** correspondente. Na área de critérios de pesquisa, você verá o recurso. Selecione o recurso e selecione **Salvar**. 

   ![Captura de tela de controle de acesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Próximas etapas

- [Identidade gerenciada para visão geral dos recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md).
- Para habilitar a identidade gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure](qs-configure-portal-windows-vmss.md).


