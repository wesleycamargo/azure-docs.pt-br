---
title: Como atribuir um acesso MSI a um recurso do Azure usando o portal do Azure
description: "As instruções passo a passo sobre como atribuir uma MSI em um acesse de recurso a outro recurso usando o portal do Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 83a56793d08632918a75f6580360a9dd148d7316
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de Identidade de Serviço Gerenciado a um recurso usando o portal do Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Depois de configurar um recurso do Azure com uma MSI (Identidade do Serviço Gerenciado), você pode conceder acesso à MSI a outro recurso, assim como qualquer entidade de segurança. Este artigo mostra como conceder o acesso de MSI de uma máquina virtual do Azure a uma conta de Armazenamento do Azure usando o portal do Azure.

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usar o RBAC para atribuir o acesso de MSI a outro recurso

Depois de habilitar o MSI em um recurso do Azure, [como uma VM do Azure](msi-qs-configure-portal-windows-vm.md):

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure sob a qual você configurou a MSI.

2. Navegue até o recurso desejado no qual você deseja modificar o controle de acesso. Neste exemplo, nós estamos fornecendo um acesso da máquina virtual do Azure para uma conta de armazenamento e, portanto, navegamos até a conta de armazenamento.

3. Selecione a página **Controle de Acesso (IAM)** do recurso e selecione **+ Adicionar**. Então especifique a **Função**, **Atribuir acesso à Máquina Virtual** e especifique a **Assinatura** e o **Grupo de Recursos** correspondentes no qual o recurso reside. Na área de critérios de pesquisa, você verá o recurso. Selecione o recurso e selecione **Salvar**. 

   ![Captura de tela de controle de acesso (IAM)](~/articles/active-directory/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Você será levado de volta à página principal de **Controle de Acesso (IAM)**, na qual você verá uma nova entrada para a MSI do recurso. Neste exemplo, a VM "SimpleWinVM" do Grupo de Recursos de Demonstração recebeu o acesso **Colaborador** à conta de armazenamento.

   ![Captura de tela de controle de acesso (IAM)](~/articles/active-directory/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>solução de problemas

Se o MSI para o recurso não aparecer na lista de identidades disponíveis, verifique se ele foi habilitado corretamente. Em nosso caso, podemos voltar à VM do Azure e verificar o seguinte:

- Examine a página **Configuração** e verifique se o valor para **MSI habilitada** é **Sim**.
- Examine a página **Extensões** e verifique se a extensão da MSI foi implantada com êxito.

Se um deles estiver incorreto, será preciso reimplantar a MSI em seu recurso ou solucionar a falha de implantação.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).
- Para habilitar o MSI em uma VM do Azure, consulte [Configurar um MSI (identidade do serviço gerenciada) usando o portal do Azure](msi-qs-configure-portal-windows-vm.md).


