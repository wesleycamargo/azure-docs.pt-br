---
title: Instalar o Visual Studio e conecte-se ao Azure Stack | Microsoft Docs
description: Conheça as etapas necessárias para instalar o Visual Studio e conecte-se ao Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 2afbea68c017805e9bd7db43b03face0705608b7
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42358740"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Instalar o Visual Studio e conecte-se ao Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar o Visual Studio para escrever e implantar o Azure Resource Manager [modelos](azure-stack-arm-templates.md) para o Azure Stack. As etapas neste artigo explicam a instalação do Visual Studio na [do Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou em um computador externo se planejar para o Azure Stack por meio de [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Instalar Visual Studio

1. Baixe e execute o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Abra o **Microsoft Web Platform Installer**.

3. Pesquise **Visual Studio Community 2015 com SDK do Microsoft Azure - 2.9.6**. Clique em **adicione**, e **instalar**.

4. Desinstalar o **Microsoft Azure PowerShell** que é instalado como parte do SDK do Azure.

    ![Etapas de instalação de captura de tela do WebPI](./media/azure-stack-install-visual-studio/image1.png) 

5. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)

6. Depois que a instalação for concluída, reinicie o sistema operacional.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Conectar-se ao Azure Stack com o Azure AD

1. Inicie o Visual Studio.

2. Dos **modo de exibição** menu, selecione **Cloud Explorer**.

3. No novo painel, selecione **adicionar conta** e entre com suas credenciais do Azure Active Directory (Azure AD).  

    ![Captura de tela do Gerenciador de nuvem uma vez conectado e conectados ao Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

Depois de conectado, você pode [implantar modelos](azure-stack-deploy-template-visual-studio.md) ou procurar tipos de recursos disponíveis e grupos de recursos para criar seus próprios modelos.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Conectar-se ao Azure Stack com o AD FS

1. Inicie o Visual Studio.

2. Partir **ferramentas**, selecione **opções**.

3. Expandir **ambiente** na **painel de navegação** e selecione **contas**.

4. Selecione **adicionar**e insira o ponto de extremidade do Gerenciador de recursos do usuário do Azure.  
  Para o kit de desenvolvimento do Azure Stack, a URL é: `https://management.local.azurestack/external`.  
  Para sistemas integrados do Azure Stack a URL é: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Selecione **Adicionar**.  

    Visual Studio chama o Gerenciador de recursos do Azure e descobre os pontos de extremidade incluindo o ponto de extremidade de autenticação para Azure Directory Federated Services (AD FS).

    ![Captura de tela do Gerenciador de nuvem uma vez conectado e conectados ao Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Selecione **Cloud Explorer** da **exibição** menu.
7. Selecione **adicionar conta** e entre com suas credenciais do AD FS.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer consultará as assinaturas disponíveis. Você pode selecionar um uma assinatura disponível para gerenciar.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. Navegação de seus recursos existentes, os grupos de recursos ou implantar modelos.

## <a name="next-steps"></a>Próximas etapas

 - Leia mais sobre [coexistência](https://msdn.microsoft.com/library/ms246609.aspx) com outras versões do Visual Studio.
 - [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)