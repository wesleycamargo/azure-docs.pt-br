<properties 
	pageTitle="Configurar um dispositivo Windows 10 com o Azure AD de Settings| Microsoft Azure" 
	description="Um tópico que explica como os usuários podem ingressar no AD do Azure por meio do menu Configurações." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/02/2015" 
	ms.author="femila"/>

# Configurar um dispositivo Windows 10 com o Azure AD nas Configurações
Se você já estiver usando o Windows 7 ou 8, e seu computador é atualizado para o Windows 10, você pode associar ao AD do Azure por meio do menu Configurações.

Para unir ao AD do Azure no menu Configurações
-----------------------------------------------------------------------------------------------

1. No menu Iniciar, clique no botão Configurações.
2. Em Configurações ->**Sistema**->**Sobre**->**Ingressar no Azure AD**
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Clique em **Continuar** na janela de mensagem de junção do Azure AD.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Forneça suas credenciais de entrada. Esse processo de inscrição incluirá todas as etapas necessárias para a autenticação completa. Se você fizer parte de um locatário federado, seu administrador fornecerá a você uma experiência de federação hospedada por sua organização.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Se a sua organização tiver configurado a autenticação multifator para unir ao AD do Azure, você precisará fornecer o segundo fator antes de poder continuar.
6. Clique em **Aceitar** na tela Permitir que este dispositivo seja gerenciado.
7. você deve ver a mensagem "O dispositivo agora faz parte de sua organização no AD do Azure".


## Informações adicionais
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre os cenários de uso e considerações de implantação para a Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=August15_HO6-->