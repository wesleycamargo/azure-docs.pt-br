<properties
   pageTitle="Gerenciamento de identidades para aplicativos multilocatários | Microsoft Azure"
   description="Práticas recomendadas para autenticação, autorização e gerenciamento de identidades em aplicativos multilocatários."
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

# Gerenciamento de identidades para aplicativos multilocatários no Microsoft Azure

Esta série descreve as práticas recomendadas para multilocação, ao usar o Azure AD para autenticação e gerenciamento de identidades.

Quando estiver compilando um aplicativo multilocatário, um dos primeiros desafios é gerenciar identidades de usuário, porque agora cada usuário pertence a um locatário. Por exemplo:

- Os usuários entram com suas credenciais organizacionais.
- Eles devem ter acesso aos dados de sua organização, mas não aos dados que pertencem a outros locatários.
- Uma organização pode se inscrever para obter o aplicativo e, em seguida, atribuir funções do aplicativo a seus membros.

O Azure AD (Azure Active Directory) tem alguns excelentes recursos que dão suporte a todos esses cenários.

Para acompanhar esta série de artigos, também criamos uma [implementação de ponta a ponta][tailspin] completa de um aplicativo multilocatário. Os artigos refletem o que aprendemos no processo de compilação do aplicativo. Para começar a usar o aplicativo, veja [Executando o aplicativo Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).


## Sumário

- [Introdução](guidance-multitenant-identity-intro.md)
- [Sobre o aplicativo Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
- [Autenticação com o Azure AD](guidance-multitenant-identity-authenticate.md)
- [Trabalhando com identidades baseadas em declarações](guidance-multitenant-identity-claims.md)
- [Inscrição e integração de locatário](guidance-multitenant-identity-signup.md)
- [Definindo e gerenciando funções do aplicativo](guidance-multitenant-identity-app-roles.md)
- [Autorização](guidance-multitenant-identity-authorize.md)
- [Protegendo uma API Web de back-end](guidance-multitenant-identity-web-api.md)
- [Armazenando em cache os tokens de acesso do OAuth2](guidance-multitenant-identity-token-cache.md)
- [Federação com AD FS de um cliente](guidance-multitenant-identity-adfs.md)
- [Usando a declaração do cliente para obter tokens de acesso do Azure AD](guidance-multitenant-identity-client-assertion.md)
- [Usando o Cofre de Chaves para proteger segredos do aplicativo](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->