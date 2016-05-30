<properties
	pageTitle="Gerencias senhas no Active Directory do Azure | Microsoft Azure"
	description="Como gerenciar senhas no Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="curtand"/>

# Gerenciar senhas no Active Directory do Azure

Se você for um administrador, poderá redefinir a senha do usuário no Active Directory do Azure (Azure AD) por meio do portal clássico do Azure. Clique no nome do diretório e na página Usuários, clique no nome do usuário e na parte inferior do portal, clique em **Redefinir senha**.

O restante deste tópico aborda o conjunto completo de recursos de gerenciamento de senha para o qual o Azure AD dá suporte, incluindo:

- A alteração de **senha por autoatendimento** permite que os usuários finais ou os administradores alterem suas senhas expiradas ou não expiradas sem chamar um administrador ou o suporte técnico em busca de suporte.
- A redefinição de **senha por autoatendimento** permite que os usuários finais ou os administradores redefinam suas senhas automaticamente, sem chamar um administrador ou o suporte técnico em busca de suporte. A redefinição de senha por autoatendimento exige o AD Premium ou Basic do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
- A **Redefinição de senha iniciada pelo administrador** permite que um administrador redefina a senha de um usuário final ou de outro administrador de dentro do portal clássico do Azure.
- **Relatórios de atividade de gerenciamento de senha** fornecem aos administradores percepções sobre as atividades de registro e de redefinição de senha que ocorrem em suas organizações.
- O **Write-back de senha** permite o gerenciamento de senhas locais na nuvem, de modo que todos os cenários acima podem ser executados por, ou em nome de, usuários federados ou sincronizados por senha. O write-back de senha requer o AD Premium do Azure. Para obter mais informações, consulte [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]
O AD Premium do Azure está disponível para clientes chineses usando a instância do AD do Azure que abrange todo o mundo. O Azure AD Premium não tem suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).

Use os links a seguir para ir para a documentação na qual você está mais interessado:

- [Visão geral: gerenciamento de senhas no AD do Azure](active-directory-passwords-how-it-works.md)
- [Redefinição de senha por autoatendimento no AD do Azure: como habilitar, configurar e testar a redefinição de senha por autoatendimento](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Redefinição de senha por autoatendimento no AD do Azure: como personalizar a redefinição de senha para atender às suas necessidades](active-directory-passwords-customize.md)
- [Redefinição de senha por autoatendimento no AD do Azure: práticas recomendadas de implantação e gerenciamento](active-directory-passwords-best-practices.md)
- [Relatórios de gerenciamento de senhas no AD do Azure: como exibir a atividade de gerenciamento de senha em seu locatário](active-directory-passwords-get-insights.md)
- [Write-back de senha: como configurar o AD do Azure para gerenciar senhas locais](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Solucionando problemas de gerenciamento de senha do AD do Azure](active-directory-passwords-troubleshoot.md)
- [Perguntas frequentes sobre o gerenciamento de senha do AD do Azure](active-directory-passwords-faq.md)


## O que vem a seguir?

- [Administrando o AD do Azure](active-directory-administer.md)
- [Criar ou editar usuários no AD do Azure](active-directory-create-users.md)
- [Gerenciar grupos no AD do Azure](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0518_2016-->