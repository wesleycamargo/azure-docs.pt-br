<properties
	pageTitle="Como usar nomes de domínio personalizados para simplificar a experiência de logon para os usuários| Microsoft Azure"
	description="Explica como adicionar seu próprio nome de domínio ao Active Directory do Azure e outras informações relacionadas."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# Como usar nomes de domínio personalizados para simplificar a experiência de logon para os usuários

Você pode usar seus próprios nomes de domínio personalizados para melhorar e simplificar a experiência de entrada do usuário e outras experiências no Active Directory do Azure. Por exemplo, se sua organização tem o nome de domínio "contoso.com", os usuários podem entrar com nomes de usuário com os quais estão familiarizados, como "pedro@contoso.com".

Cada diretório no Active Directory do Azure vem com um nome de domínio interno no formato 'contoso.onmicrosoft.com', que permite começar a usar o Azure ou outros serviços da Microsoft. [Saiba mais sobre domínios internos](#built-in-domain-names-for-azure-active-directory).

## Usar o nome de domínio personalizado com o Azure AD

Se sua organização tem um nome de domínio personalizado que é familiar aos usuários, é uma prática recomendada usar esse nome de domínio personalizado com o Active Directory do Azure. Para usar um nome de domínio personalizado com o Active Directory do Azure, você deve fazer o seguinte:

-   [Adicionar e verificar seu nome de domínio personalizado](active-directory-add-domain-add-verify-general.md)

-   [Atribuir usuários ao domínio personalizado](active-directory-add-domain-add-users.md)

## Usar o nome de domínio personalizado com o Office 365 e outros serviços

Assim como outros recursos no Azure AD, nomes de domínio personalizados que você adicionou e verificou podem ser usados com o Office 365, o Intune e outros aplicativos que usam o Azure AD. Por exemplo, o uso de um nome de domínio personalizado com o Exchange Online permite que os usuários enviem e recebam emails em endereços de email familiares como joe@contoso.com. Para habilitar esses outros aplicativos a usar domínios personalizados, você precisará adicionar mais entradas DNS no registrador de DNS, conforme documentado pelo aplicativo.

-   [Como usar domínios personalizados com o Office 365](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=pt-BR&rs=pt-BR&ad=US)

-   [Como usar domínios personalizados com o Intune](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## Gerenciar nomes de domínio no Azure AD

Nomes de domínio geralmente não exigem gerenciamento contínuo nem administração no Active Directory do Azure.

-   [Veja a lista de nomes de domínio no Active Directory do Azure](active-directory-add-domain-add-users.md)

-   [O que fazer se você alterar o registrador DNS para o nome de domínio personalizado](active-directory-add-domain-change-registrar.md)

## Excluir um nome de domínio personalizado

Você poderá excluir um nome de domínio do Azure AD se sua organização não usar esse nome de domínio ou se for preciso usar o nome de domínio com outro Azure AD.

-   [Excluir um nome de domínio personalizado](#_Deleting_a_custom)

## Nomes de domínio internos do Active Directory do Azure

Saiba a diferença entre os domínios internos no Active Directory do Azure (Azure AD) e os domínios personalizados que você adiciona.

-   Interno: o domínio que vem com seu diretório do Azure AD, no formato contoso.onmicrosoft.com.

-   Personalizado: um nome de domínio que sua organização já possui, como contoso.com

## Usar o PowerShell ou o Graph API para gerenciar nomes de domínio

A maioria das tarefas de gerenciamento para nomes de domínio no Active Directory do Azure também pode ser concluída usando o Microsoft PowerShell ou de forma programática, usando o Graph API.

-   [Como usar o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Como usar o Graph API para gerenciar nomes de domínio no Azure AD (visualização)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## Próximas etapas

Se você precisar de recursos adicionais para entender o uso do nome de domínio no Active Directory do Azure, tente:

- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
- [Adicionar e verificar um nome de domínio personalizado no Active Directory do Azure](active-directory-add-domain-add-verify-general.md)
- [Atribuir usuários a um domínio personalizado](active-directory-add-domain-add-users.md)
- [Alterar o registrador DNS para o nome de domínio personalizado](active-directory-add-domain-change-registrar.md)
- [Excluir um domínio personalizado no Active Directory do Azure](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->