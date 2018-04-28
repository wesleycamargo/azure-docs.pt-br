---
title: Configurar MSI em um conjunto de dimensionamento de máquinas virtuais do Azure usando CLI do Azure
description: Instruções passo a passo para configurar uma MSI (Identidade de Serviço Gerenciada) em um conjunto de dimensionamento de máquinas virtuais do Azure, usando CLI do Azure.
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 9cdf5225f2d87fffa2290e3edd09d4ae829aee21
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Configurar uma MSI (Identidade de Serviço Gerenciada) do conjunto de dimensionamento de máquinas virtuais usando CLI do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e remover MSI para um conjunto de dimensionamento de máquinas virtuais do Azure usando CLI do Azure.

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Para executar os exemplos de script da CLI, você tem três opções:

- Usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
- Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se você preferir usar um console local da CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitar MSI durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Para criar um conjunto de dimensionamento de máquinas virtuais habilitado para MSI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az_login). Use uma conta associada à assinatura do Azure, na qual você deseja implantar o conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
   az login
   ```

2. Crie um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para confinamento e implantação do conjunto de dimensionamento de máquinas virtuais e os recursos relacionados, usando [az group create](/cli/azure/group/#az_group_create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie um conjunto de dimensionamento de máquinas virtuais usando [az vmss create](/cli/azure/vmss/#az_vmss_create) . O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais nomeado *myVMSS* com uma MSI, conforme solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Habilitar MSI em um conjunto de dimensionamento de máquinas virtuais do Azure existente

Se você precisa habilitar MSI em um conjunto de dimensionamento de máquinas virtuais do Azure existente:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az_login). Use uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

   ```azurecli-interactive
   az login
   ```

2. Use o comando [az vmss identity assign](/cli/azure/vmss/identity/#az_vmss_identity_assign) para adicionar um MSI para uma VM existente:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Remover MSI de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precisa mais de uma MSI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az_login). Use uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

   ```azurecli-interactive
   az login
   ```

2. Use o comando [az vmss identity remove](/cli/azure/vmss/identity/#az_vmss_remove_identity) para remover o MSI:

   ```azurecli-interactive
   az vmss identity remove -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da Identidade do Serviço Gerenciado](overview.md)
- Para Início Rápido da criação do conjunto de dimensionamento de máquinas virtuais do Azure completo, consulte: 

  - [Criar um conjunto de dimensionamento de máquinas virtuais com CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
















