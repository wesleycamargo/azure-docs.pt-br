<properties
	pageTitle="Aplicativos do Azure AD: Atribuindo grupos a um aplicativo | Microsoft Azure"
	description="Como implementar a atribuição de grupos para aplicativos do Azure."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/03/2015"
	ms.author="inhenk"/>

# Aplicativos do Azure AD: Atribuindo grupos a um aplicativo
Antes de atribuir usuários e grupos a um aplicativo, você precisa exigir a atribuição de usuários. Para saber como exigir a atribuição de usuários, consulte o artigo [Exigindo atribuição de usuários](active-directory-applications-guiding-developers-requiring-user-assignment.md).

Este artigo pressupõe que você já tenha criado grupos no Active Directory que está usando para este aplicativo.

## Atribuindo grupos a um aplicativo
1. Faça logon no Portal do Azure com uma conta de administrador.
2. Clique no item **Todos os Itens** no menu principal.
3. Escolha o diretório que você está usando para o aplicativo.
4. Clique na guia **APLICATIVOS**.
5. Selecione o aplicativo na lista de aplicativos associada ao diretório.
6. Clique na guia **USUÁRIOS E GRUPOS**.
7. Filtre a lista de grupos no Active Directory usando a lista suspensa **Grupos**.
8. Selecione o grupo.
9. Clique em **ATRIBUIR**.
10. Clique em **sim** quando solicitado.

## Próximas etapas
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

<!---HONumber=AcomDC_1210_2015-->