---
title: Configurar MSI em um conjunto de dimensionamento de máquinas virtuais do Azure usando o Portal do Azure
description: Instruções passo a passo para configurar uma MSI (Identidade de Serviço Gerenciada) em VMSS do Azure, usando o Portal do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: c915c692a12781538e10d367d40e3efe473a6853
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929036"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-the-azure-portal"></a>Configurar uma Identidade do Serviço Gerenciado (MSI) da VMSS usando o Portal do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a desabilitar a identidade atribuída pelo sistema para uma VMSS usando o Portal do Azure. No momento, não há suporte para atribuir e remover identidades atribuídas ao usuário de uma VMSS do Azure por meio do Portal do Azure.

> [!NOTE]
> No momento, não há suporte para operações de identidade atribuída pelo usuário por meio do Portal do Azure. Procure novamente por atualizações.

## <a name="prerequisites"></a>pré-requisitos


- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitar a Identidade de Serviço Gerenciada durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

No momento, não há suporte de criação de VM por meio do Portal do Azure para operações de Identidade de Serviço Gerenciada. Em vez disso, consulte o artigo de Início Rápido da criação do conjunto de dimensionamento de máquinas virtuais do Azure para primeiro criar um conjunto de dimensionamento de máquinas virtuais do Azure:

- [Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

Em seguida, avance para a próxima seção para obter detalhes sobre como habilitar MSI no conjunto de dimensionamento de máquinas virtuais.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Habilitar a Identidade de Serviço Gerenciada em uma VMMS existente do Azure

Para habilitar a identidade atribuída pelo sistema em uma VM que foi originalmente provisionada sem ela:

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Habilite a identidade atribuída pelo sistema na VM selecionando "Sim" em "Identidade de serviço gerenciada" e clique em **Salvar**. Esta operação pode demorar 60 segundos ou mais para ser concluída:

   [![Captura de tela da página de configuração](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Remover a Identidade de Serviço Gerenciada de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precisa mais de uma MSI:

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Certifique-se também de que sua conta pertence a uma função que concede permissões de gravação no conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Desabilite a identidade atribuída pelo sistema na VM selecionando "Não" em "Identidade de serviço gerenciado" e clique em Salvar. Esta operação pode demorar 60 segundos ou mais para ser concluída:

   ![Captura de tela da página de configuração](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral da Identidade de Serviço Gerenciada, confira s [visão geral](overview.md).

## <a name="next-steps"></a>Próximas etapas

- Usando o Portal do Azure, conceda acesso a uma MSI do conjunto de dimensionamento de máquinas virtuais do Azure [a outro recurso do Azure](howto-assign-access-portal.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
