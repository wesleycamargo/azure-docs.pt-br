---
title: Configurar um dispositivo Windows 10 com o Azure AD de Settings| Microsoft Docs
description: "Explica como os usuários podem unir-se ao Azure AD por meio do menu Configurações."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: b844e1d9-ad43-4e4a-a398-5c4a43bf2f5c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 70610ec2c5a056d63ed854b33d84e29951940c2f
ms.contentlocale: pt-br
ms.lasthandoff: 12/29/2016


---
# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configurar um dispositivo Windows 10 com o Azure AD nas Configurações
Se já estiver usando o Windows 7 ou Windows 8 e o computador ou dispositivo tiver sido atualizado para o Windows 10, você poderá uni-lo ao Azure AD (Azure Active Directory) por meio do menu Configurações.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Para realizar a junção ao Azure AD no menu Configurações
1. No menu **Iniciar**, clique no botão **Configurações**.
2. Em **Configurações**, escolha    **Sistema**->**Sobre**->**Ingressar no Azure AD**.
   
   <center>
   ![Ingressar no Azure AD no menu Configurações](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>
3. Clique em **Continuar** na janela de mensagem da Junção do Azure AD.
   
   <center>
   ![Janela de mensagem de ingresso no Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Forneça suas credenciais de entrada. Esta experiência de entrada incluirá todas as etapas necessárias concluir a autenticação. Se você fizer parte de um locatário federado, seu administrador fornecerá uma experiência de federação hospedado pela sua organização.
   <center>
   ![Fornecer suas credenciais de entrada](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Se a sua organização tiver configurado a Azure Multi-Factor Authentication para unir-se ao Azure AD, forneça o segundo fator para continuar.
6. Clique em **Aceitar** na tela **Permitir que este dispositivo seja gerenciado**.
7. Você deverá ver a mensagem "O dispositivo agora faz parte de sua organização no Azure AD".

## <a name="additional-information"></a>Informações adicionais
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)


