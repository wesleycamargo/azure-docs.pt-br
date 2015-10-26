<properties
	pageTitle="Por que exigimos uma conta de administrador corporativo | Microsoft Azure"
	description="Descrição de configurações personalizadas."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>

# Por que exigimos uma conta de administrador corporativo para conectar ao AD DS ao configurar o Azure AD Connect

A tabela a seguir mostra os motivos pelos quais uma conta de administrador corporativo é necessária para configurar o Azure AD Connect.

Sob as seguintes condições | Descrição
------------- | ------------- |
Para configurações expressas e atualização de DirSync | <li>Para Configurações Expressas, criamos a conta do Active Directory local que é usada para sincronização (também conhecida como a conta de Conector do AD) e atribuímos as permissões corretas para sincronização e sincronização de senha</li> <li>Para a atualização de Dirsync, redefinimos a senha da conta de Conector do AD configurada atualmente e configuramos o novo serviço de sincronização do Azure AD Connect para usar essa conta. </li>



**Recursos adicionais**


* [Mais informações sobre permissões e contas do Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect no MSDN](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->