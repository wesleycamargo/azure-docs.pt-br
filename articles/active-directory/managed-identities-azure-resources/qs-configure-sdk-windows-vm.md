---
title: Usar um SDK do Azure para configurar uma VM com identidades gerenciadas para recursos do Azure
description: Instruções passo a passo para configurar e usar identidades gerenciadas para recursos do Azure em uma VM do Azure, usando um SDK do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29446f6f0a86a00cc92fe46211caddd22aaa4859
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444837"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Configurar uma VM com identidades gerenciadas para recursos do Azure usando um SDK do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende como habilitar e remover identidades gerenciadas para recursos do Azure para uma VM do Azure usando um SDK do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>SDKs do Azure com identidades gerenciadas para suporte a recursos do Azure 

O Azure dá suporte a várias plataformas de programação por meio de uma série de [SDKs do Azure](https://azure.microsoft.com/downloads). Vários deles foram atualizados para dar suporte a identidades gerenciadas para recursos do Azure e fornecer exemplos correspondentes para demonstrar o uso. Esta lista é atualizada conforme suporte adicional é adicionado:

| . | Amostra |
| --- | ------ | 
| .NET   | [Gerenciar recursos de uma VM habilitada com identidades gerenciadas para recursos do Azure habilitados](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gerenciar o armazenamento de uma VM habilitada com identidades gerenciadas para recursos do Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Criar uma VM com identidade gerenciada atribuída ao sistema habilitada](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Criar uma VM com identidade gerenciada atribuída ao sistema habilitada](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Criar uma VM do Azure com uma identidade atribuída ao sistema habilitada](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Próximas etapas

- Consulte os artigos relacionados em **Configurar identidade para uma VM do Azure**, para saber como também é possível usar o portal do Azure, o PowerShell, a CLI e os modelos de recursos.
