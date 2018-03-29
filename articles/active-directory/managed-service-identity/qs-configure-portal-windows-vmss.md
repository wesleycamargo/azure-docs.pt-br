---
title: Configurar MSI em um conjunto de dimensionamento de máquinas virtuais do Azure usando o Portal do Azure
description: Instruções passo a passo para configurar uma MSI (Identidade de Serviço Gerenciada) em VMSS do Azure, usando o Portal do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: d9b493203a78aebdfadef15cf53d9cc023bb66f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Configurar uma MSI (Identidade de Serviço Gerenciada) do conjunto de dimensionamento de máquinas virtuais usando o Portal do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá como habilitar e remover MSI para um conjunto de dimensionamento de máquinas virtuais do Azure, usando o Portal do Azure.

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Habilitar MSI durante a criação do conjunto de dimensionamento de máquinas virtuais do Azure

No momento da redação deste artigo, habilitar MSI durante a criação de um conjunto de dimensionamento de máquinas virtuais no Portal do Azure não tem suporte. Em vez disso, consulte o artigo de Início Rápido da criação do conjunto de dimensionamento de máquinas virtuais do Azure para primeiro criar um conjunto de dimensionamento de máquinas virtuais do Azure:

- [Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

Em seguida, avance para a próxima seção para obter detalhes sobre como habilitar MSI no conjunto de dimensionamento de máquinas virtuais.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>Habilitar MSI em uma VMMS do Azure existente

Se você tiver um conjunto de dimensionamento de máquinas virtuais originalmente provisionado sem MSI:

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Clique na página **Configuração**, habilite a MSI no conjunto de dimensionamento de máquinas virtuais selecionando **Sim** em "	Identidade de serviço gerenciada" e, em seguida clique em **Salvar**. Esta operação pode demorar 60 segundos ou mais para ser concluída:

   ![Captura de tela da página de configuração](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Remover MSI de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precisa mais de uma MSI:

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Certifique-se também de que sua conta pertence a uma função que concede permissões de gravação no conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Clique na página **Configuração**, remova a MSI do conjunto de dimensionamento de máquinas virtuais selecionando **Não** em **Identidade de serviço gerenciada** e, em seguida, clique em **Salvar**. Esta operação pode demorar 60 segundos ou mais para ser concluída:

   ![Captura de tela da página de configuração](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](overview.md).
- Usando o Portal do Azure, conceda acesso a uma MSI do conjunto de dimensionamento de máquinas virtuais do Azure [a outro recurso do Azure](howto-assign-access-portal.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
