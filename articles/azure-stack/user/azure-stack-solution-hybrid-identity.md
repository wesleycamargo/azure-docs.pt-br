---
title: Configurar identidade de nuvem híbrida com aplicativos do Azure e o Azure Stack | Microsoft Docs
description: Saiba como configurar a identidade de nuvem híbrida com aplicativos do Azure e o Azure Stack.
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
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: e1ace09ba293cf80b2dbbe8f746eb7fd5bc8d7cb
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036386"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Tutorial: Configurar a identidade de nuvem híbrida para aplicativos do Azure e o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como configurar uma identidade de nuvem híbrida para seus aplicativos do Azure e o Azure Stack.

Você tem duas opções para conceder acesso aos seus aplicativos no Azure global e o Azure Stack.

 * Quando o Azure Stack tem uma conexão contínua à Internet, você pode usar o Azure Active Directory (Azure AD).
 * Quando o Azure Stack está desconectado da Internet, você pode usar o Azure Directory Federated Services (AD FS).

Você pode usar as entidades de serviço para conceder acesso aos seus aplicativos do Azure Stack para fins de implantação ou a configuração usando o Azure Resource Manager no Azure Stack.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> - Estabelecer uma identidade híbrida no global Azure e o Azure Stack
> - Recupere um token para acessar a API do Azure Stack.

Você deve ter permissões de operador do Azure Stack para obter as etapas neste tutorial.

> [!Tip]  
> ![pillars.png híbrido](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> O Microsoft Azure Stack é uma extensão do Azure. O Azure Stack traz a agilidade e inovação da computação em nuvem ao seu ambiente local e habilitando a única nuvem híbrida que permite que você crie e implante aplicativos híbridos de qualquer lugar.  
> 
> O white paper [considerações sobre Design para aplicativos híbridos](https://aka.ms/hybrid-cloud-applications-pillars) analisa os pilares da qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para projetar, implantar e operar híbrido aplicativos. As considerações de design ajudar a otimizar o design do aplicativo híbrido, minimizando desafios em ambientes de produção.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Criar uma entidade de serviço do Azure AD no portal

Se você implantou o Azure Stack usando o Azure AD como o repositório de identidades, você pode criar entidades de serviço exatamente como faria para o Azure. O [criar entidades de serviço](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) artigo mostra como executar as etapas por meio do portal. Verifique se você tem o [permissões do AD do Azure necessárias](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) antes de começar.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Criar uma entidade de serviço do AD FS usando o PowerShell

Se você tiver implantado o Azure Stack com o AD FS, você pode usar o PowerShell para criar uma entidade de serviço, atribuir uma função de acesso e entrar no PowerShell usando essa identidade. [Criar entidade de serviço do AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) mostra como executar as etapas necessárias usando o PowerShell.

## <a name="using-the-azure-stack-api"></a>Usando o API do Azure Stack

O [API do Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) tutorial orienta você durante o processo de recuperar um token para acessar a API do Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Conectar-se ao Azure Stack usando o Powershell

O guia de início rápido [para entrar em funcionamento com o PowerShell no Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) orienta você pelas etapas necessárias para instalar o Azure PowerShell e conecte-se à sua instalação do Azure Stack.

### <a name="prerequisites"></a>Pré-requisitos

Uma instalação do Azure Stack conectados ao Azure Active Directory com uma assinatura que você pode acessar. Se você não tiver uma instalação do Azure Stack, você pode usar estas instruções para configurar uma [Kit de desenvolvimento do Azure Stack](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Conectar-se ao Azure Stack usando código

Para se conectar ao Azure Stack usando o código, use os pontos de extremidade do Azure Resource Manager API para obter a autenticação e os pontos de extremidade do graph para a instalação do Azure Stack e, em seguida, autenticar usando solicitações REST. Você pode encontrar um aplicativo de cliente de exemplo na [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>A menos que o SDK do Azure para seu idioma de preferência dá suporte a perfis da API do Azure, o SDK pode não funcionar com o Azure Stack. Para saber mais sobre perfis de API do Azure, consulte a [gerenciar perfis de versão de API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles) artigo.

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre como a identidade é tratada no Azure Stack, consulte [arquitetura de identidade para o Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
