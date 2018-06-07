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
ms.date: 05/22/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: a57afb4a90da5877879afddc35545e0bfef622a7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808155"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Tutorial: configurar a identidade de nuvem híbrida para aplicativos do Azure e a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Saiba como configurar uma identidade de nuvem híbrida para seus aplicativos do Azure e a pilha do Azure.

Você tem duas opções para conceder acesso aos seus aplicativos no Azure global e a pilha do Azure.

 * Quando a pilha do Azure tem uma conexão contínua à Internet, você pode usar o Azure Active Directory (AD do Azure).
 * Quando a pilha do Azure está desconectada da Internet, você pode usar o Azure Directory Federated Services (AD FS).

Você usa entidades de serviço para conceder acesso a seus aplicativos do Azure pilha com a finalidade de implantação ou de configuração usando o Gerenciador de recursos do Azure na pilha do Azure.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> * Estabelecer uma identidade híbrida no global do Azure e na pilha do Azure
> * Recupere um token para acessar a API de pilha do Azure.

Você deve ter permissões de operador de pilha do Azure para obter as etapas neste tutorial.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Criar uma entidade de serviço do AD do Azure no portal

Se você implantou o Azure pilha usando o Azure AD como repositório de identidades, você pode criar entidades de serviço, exatamente como faria para o Azure. O [criar entidades de serviço](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) artigo mostra como executar as etapas no portal. Verifique se você tem o [necessárias permissões do AD do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) antes de começar.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Criar uma entidade de serviço do AD FS usando o PowerShell

Se você tiver implantado a pilha do Azure com o AD FS, você pode usar o PowerShell para criar uma entidade de serviço, atribuir uma função de acesso e entrar no PowerShell usando essa identidade. [Criar entidade de serviço do AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) mostra como executar as etapas necessárias usando o PowerShell.

## <a name="using-the-azure-stack-api"></a>Usando a API da pilha do Azure

O [API do Azure pilha](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) tutorial orienta você pelo processo de recuperar um token para acessar a API de pilha do Azure.

## <a name="connect-to-azure-stack-using-powershell"></a>Conecte-se a pilha do Azure usando o Powershell

Início rápido [para colocar em funcionamento com o PowerShell no Azure pilha](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) orienta você pelas etapas necessárias para instalar o Azure PowerShell e conecte-se à sua instalação de pilha do Azure.

### <a name="prerequisites"></a>Pré-requisitos

Uma instalação de pilha do Azure conectada ao Active Directory do Azure com uma assinatura que você pode acessar. Se você não tiver uma instalação de pilha do Azure, você pode usar estas instruções para configurar um [Kit de desenvolvimento de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Conecte-se a pilha do Azure usando código

Para se conectar a pilha do Azure usando o código, use os pontos de extremidade do Gerenciador de recursos do Azure API para obter a autenticação e os pontos de extremidade do gráfico para a instalação de pilha do Azure e, em seguida, autenticar usando solicitações REST. Você pode encontrar um aplicativo cliente de exemplo na [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>A menos que o Azure SDK para o idioma de escolha dá suporte a perfis de API do Azure, o SDK pode não funcionar com a pilha do Azure. Para saber mais sobre perfis de API do Azure, consulte o [gerenciar perfis de versão de API](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles) artigo.

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre como a identidade é tratada na pilha do Azure, consulte [arquitetura de identidade para o Azure pilha](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
