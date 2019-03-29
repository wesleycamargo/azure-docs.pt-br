---
title: Serviços do Azure que dão suporte a identidades gerenciadas para recursos do Azure
description: Lista de serviços que dão suporte a identidades gerenciadas para recursos do Azure e autenticação do Azure AD
services: active-directory
author: MarkusVi
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5592ee7f3c4b5f55785100ceff09659aa437cb6
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578570"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que dão suporte a identidades gerenciadas para recursos do Azure

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Usando uma identidade gerenciada, você pode autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, sem ter as credenciais no seu código. Estamos no processo de integração de identidades gerenciadas para recursos do Azure e autenticação do Azure AD no Azure. Verifique regularmente as atualizações.

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que dão suporte às identidades gerenciadas dos recursos do Azure

Os seguintes serviços do Azure dão suporte a identidades gerenciadas para recursos do Azure:

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Visualização | Visualização | Visualização | 
| Atribuído pelo usuário | Visualização | Visualização | Visualização | Visualização |

Veja a lista a seguir para configurar a identidade gerenciada para Máquinas Virtuais do Azure (em regiões em que estiverem disponíveis):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos do Gerenciador de Recursos do Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquina virtual do Azure

|Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Visualização | Visualização | Visualização |
| Atribuído pelo usuário | Visualização | Visualização | Visualização | Visualização |

Veja a lista a seguir para configurar a identidade gerenciada para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure (em regiões em que estiverem disponíveis):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos do Gerenciador de Recursos do Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Disponível | Disponível | Disponível |
| Atribuído pelo usuário | Visualização | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Serviço de Aplicativo do Azure (em regiões em que estiver disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [PowerShell do Azure](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Visualização | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Visualização | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para usar uma identidade gerenciada com [do Azure BluePrint](../../governance/blueprints/overview.md):

- [Portal do Azure - atribuição de planta](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST – atribuição de planta](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerenciada |Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Disponível | Disponível | Disponível |
| Atribuído pelo usuário | Visualização | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Azure Functions (em regiões em que estiver disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [PowerShell do Azure](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Aplicativos Lógicos do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Visualização | Visualização | Não disponível | Visualização |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Aplicativos Lógicos do Azure (em regiões em que estiverem disponíveis):

- [Portal do Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Azure Data Factory V2 (em regiões em que estiver disponível):

- [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Gerenciamento de API do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Gerenciamento de API do Azure (em regiões em que estiver disponível):

- [Modelo do Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Linux: Visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Linux: Visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Instâncias de Contêiner do Azure (em regiões em que estiverem disponíveis):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que suportam a autenticação do Azure AD

Os serviços a seguir dão suporte à autenticação do Azure AD e foram testados com serviços de cliente que usam identidades gerenciadas para recursos do Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Consulte a lista a seguir para configurar o acesso ao Azure Resource Manager:

- [Atribuir acesso por meio do portal do Azure](howto-assign-access-portal.md)
- [Atribuir acesso por meio do Powershell](howto-assign-access-powershell.md)
- [Atribuir acesso por meio da CLI do Azure](howto-assign-access-CLI.md)
- [Atribuir acesso por meio do modelo do Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Nuvem | ID de Recurso | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Disponível |
| Azure Government | `https://management.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://management.microsoftazure.de/` | Disponível |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Disponível |

### <a name="azure-key-vault"></a>Cofre da Chave do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Disponível |
| Azure Government | `https://vault.usgovcloudapi.net` | Disponível |
| Azure Alemanha |  `https://vault.microsoftazure.de` | Disponível |
| Azure China 21Vianet | `https://vault.azure.cn` | Disponível |

## <a name="azure-data-lake"></a>Azure Data Lake 

| Nuvem | ID de Recurso | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Disponível |
| Azure Government |  | Não disponível |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |

## <a name="azure-sql"></a>SQL do Azure 

| Nuvem | ID de Recurso | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Disponível |
| Azure Government | `https://database.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://database.cloudapi.de/` | Disponível |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Disponível |

## <a name="azure-event-hubs"></a>Hubs de eventos do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Visualização |
| Azure Government |  | Não disponível |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |

## <a name="azure-service-bus"></a>Barramento de Serviço do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Visualização |
| Azure Government |  | Não disponível |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |

## <a name="azure-storage"></a>Armazenamento do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` | Disponível |
| Azure Government | `https://storage.azure.com/` | Disponível |
| Azure Alemanha | `https://storage.azure.com/` | Disponível |
| Azure China 21Vianet | `https://storage.azure.com/` | Disponível |
