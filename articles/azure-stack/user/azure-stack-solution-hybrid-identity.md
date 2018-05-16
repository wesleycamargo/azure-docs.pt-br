---
title: Configurar a identidade de nuvem híbrida com aplicativos do Azure e a pilha do Azure | Microsoft Docs
description: Saiba como configurar a identidade de nuvem híbrida com aplicativos do Azure e a pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>Tutorial: Configurar a identidade de nuvem híbrida com aplicativos do Azure e a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você tem duas opções para conceder acesso a seus aplicativos no Azure globais e pilha do Azure. Quando a pilha do Azure tem uma conexão contínua à Internet, você pode usar o Azure Active Directory (AD do Azure). Quando a pilha do Azure está desconectada da Internet, você pode usar o Azure Directory Federated Services (AD FS). Você usa entidades de serviço para conceder acesso a seus aplicativos na pilha do Azure para implantar ou configuração por meio do Gerenciador de recursos do Azure na pilha do Azure. 

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> * Estabelecer uma identidade híbrida no global do Azure e na pilha do Azure
> * Recuperar um token para acessar a API de pilha do Azure.

Estas etapas exigem que você tenha permissões de operador de pilha do Azure.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Criar uma entidade de serviço do AD do Azure no portal

Se você implantou o Azure pilha usando o Azure AD como repositório de identidades, você pode criar entidades de serviço, exatamente como faria para o Azure. [Este documento](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) mostra como executar as etapas no portal. Verifique se você tiver o [necessárias permissões do AD do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) antes de começar.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Criar uma entidade de serviço do AD FS usando o PowerShell

Se você tiver implantado a pilha do Azure com o AD FS, você pode usar o PowerShell para criar uma entidade de serviço, atribuir uma função de acesso e entrar no PowerShell usando essa identidade. [Este documento](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) mostra como executar as etapas usando o PowerShell.

## <a name="using-the-azure-stack-api"></a>Usando a API da pilha do Azure

[Este tutorial](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) irá orientá-lo pelo processo de recuperar um token para acessar a API de pilha do Azure.

## <a name="connect-to-azure-stack-using-powershell"></a>Conecte-se a pilha do Azure usando o Powershell

Aqui está um exemplo de script usando os conceitos apresentados neste documento para se conectar à pilha do Azure.

### <a name="prerequisites"></a>Pré-requisitos

Uma instalação de pilha do Azure conectada ao Active Directory do Azure com uma assinatura que você pode acessar. Se você não tiver uma instalação de pilha do Azure, você pode usar estas instruções para configurar um [Kit de desenvolvimento de pilha do Azure](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy).

[Este tutorial](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) orientará você durante as etapas necessárias para instalar o Azure PowerShell e conecte-se à sua instalação de pilha do Azure.

#### <a name="connect-to-azure-stack-using-code"></a>Conecte-se a pilha do Azure usando código

Para se conectar a pilha do Azure usando o código, use os pontos de extremidade do Gerenciador de recursos do Azure API para obter a autenticação e os pontos de extremidade do gráfico para a instalação de pilha do Azure e, em seguida, autenticar usando solicitações REST. Você pode encontrar um aplicativo de exemplo [aqui](https://github.com/shriramnat/HybridARMApplication).

> [!Note]  
A menos que o Azure SDK para o idioma de escolha dá suporte a perfis de API do Azure, o SDK pode não funcionar com a pilha do Azure. Para saber mais sobre perfis de API do Azure, vá [aqui](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre como a identidade é tratada na pilha do Azure, consulte [arquitetura de identidade para o Azure pilha](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).  
 - Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
