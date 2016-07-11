<properties
	pageTitle="Configurar um dispositivo Windows 10 com o Azure AD de Settings| Microsoft Azure"
	description="Explica como os usuários podem unir-se ao Azure AD por meio do menu Configurações."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# Configurar um dispositivo Windows 10 com o Azure AD nas Configurações
Se já estiver usando o Windows 7 ou Windows 8 e o computador ou dispositivo tiver sido atualizado para o Windows 10, você poderá uni-lo ao Azure AD (Azure Active Directory) por meio do menu Configurações.

## Para realizar a junção ao Azure AD no menu Configurações


1. No menu **Iniciar**, clique no botão **Configurações**.
2. Em **Configurações**, selecione **Sistema**->**Sobre**->**Unir ao Azure AD**.
<center> ![Unir ao Azure AD do menu Configurações](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Clique em **Continuar** na janela de mensagem da Junção do Azure AD.
<center> ![Janela de mensagem Unir ao Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Forneça suas credenciais de entrada. Esta experiência de entrada incluirá todas as etapas necessárias concluir a autenticação. Se você fizer parte de um locatário federado, seu administrador fornecerá uma experiência de federação hospedado pela sua organização.
<center> ![Fornecer credenciais de entrada](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Se a sua organização tiver configurado a Azure Multi-Factor Authentication para unir-se ao Azure AD, forneça o segundo fator para continuar.
6. Clique em **Aceitar** na tela **Permitir que este dispositivo seja gerenciado**.
7. Você deverá ver a mensagem "O dispositivo agora faz parte de sua organização no Azure AD".


## Informações adicionais
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)

<!---HONumber=AcomDC_0629_2016-->