---
title: Como configurar o MSI em uma VM do Azure usando o portal do Azure
description: Instruções passo a passo para configurar uma Identidade de Serviço Gerenciado (MSI) em uma VM do Azure usando o portal do Azure.
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
ms.openlocfilehash: 27ecb00bddb41ae45e790a54702c058ff3f1d24b
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035934"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configurar uma Identidade do Serviço Gerenciado (MSI) da VM usando a CLI do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a desabilitar a identidade atribuída pelo sistema para uma VM do Azure usando o Portal do Azure. No momento, não há suporte para atribuir e remover identidades atribuídas ao usuário de VMs do Azure por meio do Portal do Azure.

> [!NOTE]
> No momento, não há suporte para operações de identidade atribuída pelo usuário por meio do Portal do Azure. Procure novamente por atualizações. 

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Identidade de Serviço Gerenciada durante a criação de uma VM do Azure

No momento, não há suporte de criação de VM por meio do Portal do Azure para operações de Identidade de Serviço Gerenciada. Em vez disso, consulte um dos seguintes artigos de início rápido de criação de VM para primeiro criar uma VM:

- [Criar uma máquina virtual do Windows com o Portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual Linux com o Portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Vá para a próxima seção para obter detalhes sobre como habilitar a Identidade de Serviço Gerenciada na VM.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Habilitar a Identidade de Serviço Gerenciada em uma VM existente do Azure

Para habilitar a identidade atribuída pelo sistema em uma VM que foi originalmente provisionada sem ela:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual".

2. Navegue até a Máquina Virtual desejada e selecione a página "Configuração".

3. Habilite a identidade atribuída pelo sistema na VM selecionando "Sim" em "Identidade de serviço gerenciada" e clique em **Salvar**. Esta operação pode demorar 60 segundos ou mais para ser concluída:

    > [!NOTE]
    > Não há suporte para adicionar uma identidade atribuída pelo usuário para uma VM por meio do Portal do Azure.

   ![Captura de tela da página de configuração](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Remova a Identidade de Serviço Gerenciada de uma VM do Azure

Se você tiver uma Máquina Virtual que não precisa mais da identidade atribuída pelo sistema:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual".

2. Navegue até a Máquina Virtual desejada e selecione a página "Configuração".

3. Desabilite a identidade atribuída pelo sistema na VM selecionando "Não" em "Identidade de serviço gerenciado" e clique em Salvar. Esta operação pode demorar 60 segundos ou mais para ser concluída:

    > [!NOTE]
    > Não há suporte para adicionar uma identidade atribuída pelo usuário para uma VM por meio do Portal do Azure.

   ![Captura de tela da página de configuração](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral da Identidade de Serviço Gerenciada, confira s [visão geral](overview.md).

## <a name="next-steps"></a>Próximas etapas

- Usando o portal do Azure, conceda [acesso a outro recurso do Azure](howto-assign-access-portal.md) ao MSI da VM do Azure.

