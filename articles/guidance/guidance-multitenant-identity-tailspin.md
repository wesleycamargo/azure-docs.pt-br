<properties
   pageTitle="Sobre o aplicativo Tailspin Surveys | Microsoft Azure"
   description="Visão geral do aplicativo Tailspin Surveys"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Sobre o aplicativo Tailspin Surveys

Este artigo faz [parte de uma série]. Há também um [aplicativo de exemplo] completo que acompanha esta série.

A Tailspin é uma empresa fictícia que está desenvolvendo um aplicativo SaaS chamado Surveys. Esse aplicativo permite às organizações criar e publicar pesquisas online.

- Uma organização pode se inscrever para obter o aplicativo.
- Depois que a organização estiver inscrita, os usuários podem entrar no aplicativo com suas credenciais organizacionais.
- Os usuários podem criar, editar e publicar pesquisas.

> [AZURE.NOTE] Para começar a usar o aplicativo, veja [Executando o aplicativo Surveys].

Esta captura de tela mostra a página Editar Pesquisa:

![Editar pesquisa](media/guidance-multitenant-identity/edit-survey.png)

Observe que o usuário é conectado com sua identidade organizacional, `bob@contoso.com`.

Os usuários podem exibir as pesquisas criadas por outros usuários no mesmo locatário.

![Pesquisas de locatário](media/guidance-multitenant-identity/tenant-surveys.png)

Quando um usuário cria uma pesquisa, ele ou ela pode convidar outras pessoas para ser colaboradores da pesquisa. Os colaboradores podem editar a pesquisa, mas não podem excluí-la ou publicá-la.

![Adicionar colaborador](media/guidance-multitenant-identity/add-contributor.png)

Um usuário pode adicionar colaboradores de outros locatários, o que permite o compartilhamento de recursos entre locatários. Nessa tela, Carlos(`bob@contoso.com`) está adicionando Beatriz (`alice@fabrikam.com`) como uma colaboradora de uma pesquisa criada por ele.

Quando Beatriz fizer logon, ela verá a pesquisa listada em “Pesquisas com as quais posso contribuir”.

![Colaborador da pesquisa](media/guidance-multitenant-identity/contributor.png)

Observe que Beatriz entra em seu próprio locatário, não como uma convidada do locatário da Contoso. Beatriz tem permissões de colaborador somente para essa pesquisa &mdash; ela não pode exibir outras pesquisas no locatário da Contoso.

## Arquitetura

O aplicativo Surveys consiste em um front-end da Web e um back-end de API da Web. Ambos são implementados usando o [ASP.NET Core 1.0].

O aplicativo Web usa o Azure AD (Azure Active Directory) para autenticar usuários. Ele também chama o Azure AD para obter tokens de acesso do OAuth 2 para a API Web. Tokens de acesso são armazenados no Cache Redis do Azure. O cache permite que várias instâncias compartilhem o mesmo cache de token (por exemplo, em um farm de servidores).

![Arquitetura](media/guidance-multitenant-identity/architecture.png)

<!-- Links -->
[parte de uma série]: guidance-multitenant-identity.md
[Executando o aplicativo Surveys]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[ASP.NET Core 1.0]: https://docs.asp.net/en/latest/
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->