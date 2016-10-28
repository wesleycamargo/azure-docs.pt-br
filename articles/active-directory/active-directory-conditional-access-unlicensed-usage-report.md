<properties
	pageTitle="Relatório de uso não licenciado | Microsoft Azure"
	description="O relatório de uso não licenciado ajuda a identificar usuários sem licença que estejam usando recursos pagos do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="markvi"/>

# Relatório de uso não licenciado

O relatório de uso não licenciado ajuda a identificar usuários sem licença que estejam usando recursos pagos do Azure AD. Isso permite fazer melhor uso das licenças que você comprou e identificar quando você poderá precisar de mais licenças.

O relatório mostra o uso ativo dos recursos pagos nos últimos 30 dias.

## Estrutura de relatório
 
| Nome da coluna |	Descrição |
| :--                  | :--         |
| Usuário Não Licenciado |	Nome do usuário |
| Recurso | O nome do recurso. Por exemplo: acesso condicional |
| Aplicativo acessado | O nome do aplicativo que está sendo acessado com o recurso. Por exemplo: Office 365 SharePoint Online |

 
> [AZURE.NOTE] Se uma conta de usuário tiver sido excluída, a coluna 'Usuário Não Licenciado' será preenchida com uma ID, como 1003000090D8B285


## Recurso de acesso condicional

Os usuários não licenciados serão sinalizados quando acessarem um serviço com a política de acesso condicional aplicada se não tiverem uma licença do Azure AD Premium.

Isso se aplica a políticas de MFA/Local, bem como a políticas de dispositivos que usam o Intune.
 

## Consulte também

- [Usar o acesso condicional com o Office 365 e com outros aplicativos conectados ao Azure Active Directory](active-directory-conditional-access.md)
- [Introdução ao acesso condicional ao Azure AD](active-directory-conditional-access-azuread-connected-apps.md)

<!---HONumber=AcomDC_0727_2016-->