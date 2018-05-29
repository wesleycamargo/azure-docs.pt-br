---
title: Serviços do Azure que oferecem suporte à Identidade de Serviço Gerenciado
description: Lista de serviços que oferecem suporte a Identidade de Serviço Gerenciada e autenticação do Microsoft Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 03/28/2018
ms.topic: reference
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: ff56928748f536ffe1cfc30891585565e962d9fe
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930265"
---
# <a name="services-that-support-managed-service-identity"></a>Serviços que oferecem suporte à Identidade de Serviço Gerenciada 

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Usando uma identidade gerenciada, você pode autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, sem ter as credenciais no seu código. Estamos no processo de integração da autenticação do MSI e do Azure AD no Azure. Verifique regularmente as atualizações.

## <a name="azure-services-that-support-managed-service-identity"></a>Serviços do Azure que oferecem suporte à Identidade de Serviço Gerenciado

Os seguintes serviços do Azure oferecem suporte à Identidade de Serviço Gerenciado.

| Serviço | Status | Data | Configurar | Obter um token |
| ------- | ------ | ---- | --------- | ----------- |
| Máquinas Virtuais do Azure | Visualização | Setembro de 2017 | [Portal do Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[CLI do Azure](qs-configure-cli-windows-vm.md)<br>[Modelos do Gerenciador de Recursos do Azure](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Serviço de aplicativo do Azure | Visualização | Setembro de 2017 | [Portal do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Funções do Azure | Visualização | Setembro de 2017 | [Portal do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Visualização | Novembro de 2017 | [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que suportam a autenticação do Azure AD

Os seguintes serviços dão suporte à autenticação do Azure AD e foram testados com serviços de cliente que usam a Identidade de Serviço Gerenciado.

| Serviço | ID de Recurso | Status | Data | Atribuir acesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | Disponível | Setembro de 2017 | [Portal do Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[CLI do Azure](howto-assign-access-CLI.md) |
| Cofre da Chave do Azure | https://vault.azure.net | Disponível | Setembro de 2017 | |
| Azure Data Lake | https://datalake.azure.net | Disponível | Setembro de 2017 | |
| SQL do Azure | https://database.windows.net | Disponível | Outubro de 2017 | |
| Hubs de eventos do Azure | https://eventhubs.azure.net | Disponível | Dezembro de 2017 | |
| Barramento de Serviço do Azure | https://servicebus.azure.net | Disponível | Dezembro de 2017 | |