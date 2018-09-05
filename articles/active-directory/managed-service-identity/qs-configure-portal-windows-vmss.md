---
title: Configurar uma Identidade de Serviço Gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure usando o Portal do Azure
description: Instruções passo a passo para configurar uma Identidade de Serviço Gerenciada em VMSS do Azure usando o Portal do Azure.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: cbe2e3d9f60ced5c707ce5a701a5aac937ccc072
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887982"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Configurar uma Identidade de Serviço Gerenciada do conjunto de dimensionamento de máquinas virtuais usando o portal do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende a habilitar e a desabilitar a identidade atribuída pelo sistema e pelo usuário para um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, sua conta precisará da seguinte atribuição de função:
    - [Colaborador de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para habilitar e remover identidade gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais.

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema 

Nesta seção, você aprenderá a habilitar e a desabilitar a identidade atribuída pelo sistema para um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Habilitar a identidade atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Atualmente, o portal do Azure não dá suporte à habilitação da identidade atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais. Em vez disso, consulte o seguinte artigo de início rápido para criar um conjunto de dimensionamento de máquinas virtuais e, em seguida, vá para a próxima seção para obter detalhes sobre como habilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais:

- [Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>Habilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais existente

Para habilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais que originalmente foi provisionado sem ela:

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Em **Sistema atribuído**, **Status**, selecione **Ativado** e, em seguida, clique em **Salvar**:

   [![Captura de tela da página de configuração](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>Remover a identidade atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precisa mais de uma identidade atribuída pelo sistema:

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Certifique-se também de que sua conta pertence a uma função que concede permissões de gravação no conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Em **Sistema atribuído**, **Status**, selecione **Desativado** e, em seguida, clique em **Salvar**:

   ![Captura de tela da página de configuração](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover uma identidade atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

Atualmente, o portal do Azure não dá suporte à atribuição de uma identidade atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais. Em vez disso, consulte o seguinte artigo de início rápido para criar um conjunto de dimensionamento de máquinas virtuais e, em seguida, vá para a próxima seção para obter detalhes sobre como atribuir uma identidade atribuída pelo usuário:

- [Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais existente

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.
2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado e clique em **Identidade**, **Usuário atribuído** e, em seguida, **\+Adicionar**.

   ![Adicionar identidade atribuída pelo usuário ao VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Clique na identidade atribuída pelo usuário que você deseja adicionar ao conjunto de dimensionamento de máquinas virtuais e, em seguida, clique em **Adicionar**.
   
   ![Adicionar identidade atribuída pelo usuário ao VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Remover uma identidade atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM.
2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado e clique em **Identidade**, **Usuário atribuído**, clique no nome da identidade atribuída pelo usuário que deseja excluir e, em seguida, clique em **Remover** (clique em **Sim** no painel de confirmação).

   ![Remover identidade atribuída pelo usuário de um VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral da Identidade de Serviço Gerenciada, confira s [visão geral](overview.md).

## <a name="next-steps"></a>Próximas etapas

- Usando o portal do Azure, conceda a uma Identidade de Serviço Gerenciada do conjunto de dimensionamento de máquinas virtuais do Azure [acesso a outro recurso do Azure](howto-assign-access-portal.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
