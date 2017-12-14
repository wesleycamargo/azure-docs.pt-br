---
title: "Como usar uma Identidade de Serviço Gerenciado da VM do Azure com SDKs do Azure"
description: "Exemplos de código para usar SDKs do Azure com uma MSI de VM do Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 46fd75668999537dfbc9fd82c1f166e126b6f547
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Como usar uma MSI (Identidade de Serviço Gerenciado) da VM do Azure com SDKs do Azure 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] Este artigo fornece uma lista de exemplos de SDK, que demonstram o uso do suporte do respectivo SDK do Azure para MSI.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Todo código/script de exemplo neste artigo supõe que o cliente esteja sendo executado em uma Máquina Virtual habilitada para MSI. Use o recurso "Conectar" da VM no Portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar a MSI em uma VM, consulte [Configurar uma MSI (Identidade de Serviço Gerenciado) da VM usando o portal do Azure](msi-qs-configure-portal-windows-vm.md) ou um dos artigos variantes (o PowerShell, CLI, modelo ou SDK do Azure). 

## <a name="sdk-code-samples"></a>Exemplos de código do SDK

| .             | Exemplo de código |
| --------------- | ----------- |
| .NET            | [Implantar um modelo do Azure Resource Manager de uma VM do Windows usando a Identidade do Serviço Gerenciado](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Chamar os serviços do Azure de uma VM do Linux usando a Identidade do Serviço Gerenciado](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Gerenciar recursos usando a Identidade de Serviço Gerenciado](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Use o MSI para autenticar de modo simples de dentro de uma VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Gerenciar recurso de uma VM habilitada para MSI](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Conteúdo relacionado

- Consulte [Azure SDKs](https://azure.microsoft.com/downloads/) (SDKs do Azure) para ver a lista completa de recursos de SDK do Azure, incluindo downloads da biblioteca, documentação e mais.
- Para habilitar a MSI em uma VM do Azure, consulte [Configure a VM Managed Service Identity (MSI) using the Azure portal](msi-qs-configure-portal-windows-vm.md) (Configurar uma MSI (Identidade de Serviço Gerenciado) usando o portal do Azure).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.








