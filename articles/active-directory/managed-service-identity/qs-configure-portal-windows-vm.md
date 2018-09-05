---
title: Como configurar a Identidade de Serviço Gerenciada em uma VM do Azure usando o portal do Azure
description: Instruções passo a passo para configurar uma Identidade de Serviço Gerenciada em uma VM do Azure usando o portal do Azure.
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
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 552fce2ffd8b6bd786010da82e702ee98c3f8647
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888549"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Configure uma Identidade de Serviço Gerenciada VM de usando o portal do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende a habilitar e a desabilitar a identidade atribuída pelo sistema e pelo usuário para uma VM (máquina virtual) do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, sua conta precisará da seguinte atribuição de função:
    - [Colaborador de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para habilitar e remover a identidade atribuída do sistema de uma VM do Azure.

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Nesta seção, você aprende a habilitar e desabilitar a identidade atribuída pelo sistema para uma VM usando o portal do Azure.

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>Habilitar a identidade atribuída pelo sistema durante a criação de uma VM

Atualmente, o portal do Azure não dá suporte à habilitação da identidade atribuída pelo sistema durante a criação de uma VM. Em vez disso, consulte um dos seguintes artigos de início rápido de criação de VMs para primeiro criar uma VM e, em seguida, vá para a próxima seção para obter detalhes sobre como habilitar a identidade atribuída pelo sistema na VM:

- [Criar uma máquina virtual do Windows com o Portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual Linux com o Portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>Habilitar a identidade atribuída pelo sistema em uma VM existente

Para habilitar a identidade atribuída pelo sistema em uma VM que foi originalmente provisionada sem ela:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM.

2. Navegue até a Máquina Virtual desejada e selecione **Identidade**.

3. Em **Sistema atribuído**, **Status**, selecione **Ativado** e, em seguida, clique em **Salvar**:

   ![Captura de tela da página de configuração](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>Remover uma identidade atribuída pelo sistema de uma VM

Se você tiver uma Máquina Virtual que não precisa mais da identidade atribuída pelo sistema:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM. 

2. Navegue até a Máquina Virtual desejada e selecione **Identidade**.

3. Em **Sistema atribuído**, **Status**, selecione **Desativado** e, em seguida, clique em **Salvar**:

   ![Captura de tela da página de configuração](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

 Nesta seção, você aprende a adicionar e remover uma identidade atribuída pelo usuário de uma VM usando o portal do Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Atribuir uma identidade atribuída pelo usuário durante a criação de uma VM

Atualmente, o portal do Azure não dá suporte à atribuição de uma identidade atribuída pelo usuário durante a criação de uma VM. Em vez disso, consulte um dos seguintes artigos de início rápido de criação de VMs para primeiro criar uma VM e, em seguida, vá para a próxima seção para obter detalhes sobre como atribuir uma identidade atribuída pelo usuário à VM:

- [Criar uma máquina virtual do Windows com o Portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual Linux com o Portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>Atribuir uma identidade atribuída pelo usuário a uma VM existente

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM.
2. Navegue até a VM desejada e clique em **Identidade**, **Usuário atribuído** e, em seguida, **\+Adicionar**.

   ![Adicionar identidade atribuída pelo usuário à VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Clique na identidade atribuída pelo usuário que você deseja adicionar à VM e, em seguida, clique em **Adicionar**.

    ![Adicionar identidade atribuída pelo usuário à VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>Remover identidade atribuída pelo usuário de uma VM

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM.
2. Navegue até a VM desejada e clique em **Identidade**, **Usuário atribuído**, clique no nome da identidade atribuída pelo usuário que deseja excluir e, em seguida, clique em **Remover** (clique em **Sim** no painel de confirmação).

   ![Remover identidade atribuída pelo usuário de uma VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral da Identidade de Serviço Gerenciada, confira s [visão geral](overview.md).

## <a name="next-steps"></a>Próximas etapas

- Usando o portal do Azure, dê a uma Identidade de Serviço Gerenciada de VM do Azure [acesso a outro recurso do Azure](howto-assign-access-portal.md).

