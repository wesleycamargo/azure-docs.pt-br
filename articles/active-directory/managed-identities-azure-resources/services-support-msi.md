---
title: Serviços do Azure que dão suporte a identidades gerenciadas para recursos do Azure
description: Lista de serviços que dão suporte a identidades gerenciadas para recursos do Azure e autenticação do Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: a75d3265e70efacc9e581abe32faacb6c5fb1b37
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850509"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que dão suporte a identidades gerenciadas para recursos do Azure

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Usando uma identidade gerenciada, você pode autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, sem ter as credenciais no seu código. Estamos no processo de integração de identidades gerenciadas para recursos do Azure e autenticação do Azure AD no Azure. Verifique regularmente as atualizações.

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que dão suporte às identidades gerenciadas dos recursos do Azure

Os seguintes serviços do Azure dão suporte a identidades gerenciadas para recursos do Azure:

| Serviço | Status atribuída ao sistema | Status atribuído ao usuário| Configurar | Obter um token |
| ------- | ------ | ---- | --------- | ----------- |
| Máquinas Virtuais do Azure | Disponível | Visualização | [Portal do Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[CLI do Azure](qs-configure-cli-windows-vm.md)<br>[Modelos do Gerenciador de Recursos do Azure](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Conjuntos de Dimensionamento de Máquinas Virtuais | Disponível | Visualização | [Portal do Azure](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[CLI do Azure](qs-configure-cli-windows-vmss.md)<br>[Modelos do Gerenciador de Recursos do Azure](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Serviço de aplicativo do Azure | Windows: Disponível <br> Linux: Visualização | Visualização | [Portal do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[CLI do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[PowerShell do Azure](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Funções do Azure | Disponível | Visualização | [Portal do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[CLI do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[PowerShell do Azure](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Aplicativos Lógicos do Azure | Disponível | Não disponível | [Portal do Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#deployment-template) |  |
| Azure Data Factory V2 | Disponível | Não disponível | [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Gerenciamento de API do Azure | Disponível | Não disponível | [Modelo do Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity) |
| Instâncias de Contêiner do Azure | Linux: Visualização<br>Windows: Não disponível | Linux: Visualização<br>Windows: Não disponível | [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)<br>[Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)<br>[YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file) |  |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que suportam a autenticação do Azure AD

Os serviços a seguir dão suporte à autenticação do Azure AD e foram testados com serviços de cliente que usam identidades gerenciadas para recursos do Azure.

| Serviço | ID de Recurso | Status | Data | Atribuir acesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Disponível | Setembro de 2017 | [Portal do Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[CLI do Azure](howto-assign-access-CLI.md) <br>[Modelo do Azure Resource Manager](../../role-based-access-control/role-assignments-template.md) |
| Cofre da Chave do Azure | `https://vault.azure.net` | Disponível | Setembro de 2017 | |
| Azure Data Lake | `https://datalake.azure.net/` | Disponível | Setembro de 2017 | |
| SQL do Azure | `https://database.windows.net/` | Disponível | Outubro de 2017 | |
| Hubs de eventos do Azure | `https://eventhubs.azure.net` | Visualização | Dezembro de 2017 | |
| Barramento de Serviço do Azure | `https://servicebus.azure.net` | Visualização | Dezembro de 2017 | |
| Armazenamento do Azure | `https://storage.azure.com/` | Visualização | Maio de 2018 | |