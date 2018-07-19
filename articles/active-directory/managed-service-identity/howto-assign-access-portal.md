---
title: Como atribuir um acesso MSI a um recurso do Azure usando o portal do Azure
description: As instruções passo a passo sobre como atribuir uma MSI em um acesse de recurso a outro recurso usando o portal do Azure.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: cf6df6dd87de56ca3c8180d5377e35d9be9bbd79
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036438"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de Identidade de Serviço Gerenciado a um recurso usando o portal do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso do Azure com uma MSI (Identidade do Serviço Gerenciado), você pode conceder acesso à MSI a outro recurso, assim como qualquer entidade de segurança. Este artigo mostra como conceder acesso de MSI de uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais a uma conta de armazenamento do Azure, usando o Portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usar o RBAC para atribuir o acesso de MSI a outro recurso

Após habilitar a MSI em um recurso do Azure, como uma [VM do Azure](qs-configure-portal-windows-vm.md) ou [VMSS do Azure ](qs-configure-portal-windows-vmss.md):

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure sob a qual você configurou a MSI.

2. Navegue até o recurso desejado no qual você deseja modificar o controle de acesso. Neste exemplo, fornecemos uma máquina virtual do Azure e um conjunto de dimensionamento de máquinas virtuais do Azure para acesso a uma conta de armazenamento e, em seguida, navegaremos para a conta de armazenamento.

3. Para uma máquina virtual do Azure, selecione a página **IAM (Controle de acesso)** do recurso e selecione **+ Adicionar**. Então especifique a **Função**, **Atribuir acesso à Máquina Virtual** e especifique a **Assinatura** e o **Grupo de Recursos** correspondentes no qual o recurso reside. Na área de critérios de pesquisa, você verá o recurso. Selecione o recurso e selecione **Salvar**. 

   ![Captura de tela do IAM (Controle de acesso)](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Para um conjunto de dimensionamento de máquinas virtuais do Azure, selecione a página **IAM (Controle de acesso)** do recurso e selecione **+ Adicionar**. Em seguida, especifique o **Função**, **Atribuir acesso a**. Na área de critérios de pesquisa, pesquise o conjunto de dimensionamento de máquinas virtuais. Selecione o recurso e selecione **Salvar**.
   
   ![Captura de tela de controle de acesso (IAM)](../managed-service-identity/media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Você será levado de volta à página principal de **Controle de Acesso (IAM)**, na qual você verá uma nova entrada para a MSI do recurso.

    Máquina virtual do Azure:

   ![Captura de tela de controle de acesso (IAM)](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Conjunto de dimensionamento de máquinas virtuais do Azure:

    ![Captura de tela de controle de acesso (IAM)](../managed-service-identity/media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>solução de problemas

Se o MSI para o recurso não aparecer na lista de identidades disponíveis, verifique se ele foi habilitado corretamente. No nosso caso, podemos retornar para a máquina virtual do Azure e verificar o seguinte:

- Examine a página **Configuração** e verifique se o valor para **MSI habilitada** é **Sim**.
- Examine a página **Extensões** e certifique-se de que a extensão da MSI implantada com êxito (página **Extensões** não está disponível para um conjuntos de dimensionamento de máquinas virtuais do Azure).

Se um deles estiver incorreto, será preciso reimplantar a MSI em seu recurso ou solucionar a falha de implantação.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](overview.md).
- Para habilitar a MSI em uma máquina virtual do Azure, consulte [Configurar uma MSI (Identidade de Serviço Gerenciada) da VM do Azure usando o Portal do Azure](qs-configure-portal-windows-vm.md).
- Para habilitar a MSI em um conjunto de dimensionamento de máquinas virtuais do Azure, consulte [Configurar uma MSI (Identidade de Serviço Gerenciada) do conjunto de dimensionamento de máquinas virtuais do Azure usando o Portal do Azure](qs-configure-portal-windows-vmss.md)


