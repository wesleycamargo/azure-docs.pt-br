---
title: Instalar o Visual Studio e conecte-se a pilha do Azure | Microsoft Docs
description: Conheça as etapas necessárias para instalar o Visual Studio e conecte-se a pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.openlocfilehash: cbd5e5dbcdd2565e8066b0721f45863569bfd90a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295023"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Instalar o Visual Studio e conecte-se a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar o Visual Studio para gravar e implantar o Azure Resource Manager [modelos](azure-stack-arm-templates.md) a pilha do Azure. As etapas neste artigo percorrer a instalação do Visual Studio no [Azure pilha](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou em um computador externo, se você planeja a pilha do Azure por meio de [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Instalar Visual Studio

1. Baixe e execute o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Abra o **Microsoft Web Platform Installer**.

3. Procurar **Visual Studio Community 2015 com o SDK do Microsoft Azure - 2.9.6**. Clique em **adicionar**, e **instalar**.

4. Desinstalar o **Microsoft Azure PowerShell** que é instalado como parte do SDK do Azure.

    ![Etapas de instalação de captura de tela de WebPI](./media/azure-stack-install-visual-studio/image1.png) 

5. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)

6. Reinicie o sistema operacional após a conclusão da instalação.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Conecte-se a pilha do Azure com o Azure AD

1. Inicie o Visual Studio.

2. Do **exibição** menu, selecione **Cloud Explorer**.

3. No painel de novo, selecione **adicionar conta** e entre com suas credenciais do Azure Active Directory (AD do Azure).  

    ![Captura de tela de Cloud Explorer uma vez registrados e conectados à pilha do Azure](./media/azure-stack-install-visual-studio/image2.png)

Depois de conectado, você pode [implantar modelos](azure-stack-deploy-template-visual-studio.md) ou procurar tipos de recursos disponíveis e os grupos de recursos para criar seus próprios modelos.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Conecte-se a pilha do Azure com o AD FS

1. Inicie o Visual Studio.

2. De **ferramentas**, selecione **opções**.

3. Expanda **ambiente** no **painel de navegação** e selecione **contas**.

4. Selecione **adicionar**e insira o ponto de extremidade do Gerenciador de recursos do usuário do Azure.  
  Para o kit de desenvolvimento de pilha do Azure, a URL é: `https://management.local.azurestack/external`.  
  Para a pilha do Azure sistemas integrados a URL é: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Selecione **Adicionar**.  

    O Visual Studio chama o Gerenciador de recursos do Azure e descobre os pontos de extremidade incluindo o ponto de extremidade de autenticação para o Azure Directory Federated Services (AD FS).

    ![Captura de tela de Cloud Explorer uma vez registrados e conectados à pilha do Azure](./media/azure-stack-install-visual-studio/image6.png)

6. Selecione **Cloud Explorer** do **exibição** menu.
7. Selecione **adicionar conta** e entre com suas credenciais do AD FS.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer consulta as assinaturas disponíveis. Você pode selecionar uma uma assinatura disponível para gerenciar.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. Navegação seus recursos existentes, os grupos de recursos ou implantar modelos.

## <a name="next-steps"></a>Próximas etapas

 - Leia mais sobre [coexistência](https://msdn.microsoft.com/library/ms246609.aspx) com outras versões do Visual Studio.
 - [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)