<properties
   pageTitle="Integrar o SSP do Active Directory do Azure a aplicativos existentes | Microsoft Azure"
   description="Gerencie os aplicativos de SaaS que você já usa habilitando a autenticação de logon único e o provisionamento do usuário no Active Directory do Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# Integrar o SSP do Active Directory do Azure a aplicativos existentes

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## Outras considerações

Configurar o SSO para aplicativos que você já usa é um processo diferente da criação de novas contas em novos aplicativos. Administradores e usuários verão uma alteração quando o aplicativo for integrado ao Azure AD.

### O que os administradores precisam saber para configurar o logon único para aplicativos já em uso?

Para configurar o SSO para um aplicativo existente, você precisa ter direitos de administrador global no Azure AD e no aplicativo de SaaS.

Uma vez que o aplicativo já estiver em uso, será necessário vincular as identidades de aplicativo já estabelecidas do usuário a suas respectivas identidades do Azure AD. É importante saber o que esse aplicativo usa como identificador exclusivo para o logon, se é um endereço de email, UPN (nome pessoal universal) ou nome de usuário. Isso será vinculado ao identificador exclusivo do usuário no Azure AD. Para obter mais informações sobre a vinculação de identidades de aplicativo com identidades do Azure AD, consulte [Personalizando declarações emitidas no token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e [Personalizando mapeamentos de atributos para provisionamento](active-directory-saas-customizing-attribute-mappings.md).

### Como isso afeta os usuários finais?

Ao integrar o SSO para um aplicativo que já está em uso, é importante perceber que a experiência do usuário será afetada. Para todos os aplicativos, os usuários começarão usando suas credenciais do Azure AD para entrar. Além disso, eles podem precisar usar um portal diferente para acessar os aplicativos. O SSO para alguns aplicativos pode ser feito no site do aplicativo, mas em outros aplicativos o usuário terá que passar por um portal de aplicativo central ([Meus Aplicativos](myapps.microsoft.com) ou [Office365](portal.office.com/myapps)) para entrar. Saiba mais sobre os diferentes tipos de SSO e suas experiências de usuário em [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Próximas etapas
- Veja como [Integrar o logon único do Active Directory do Azure a aplicativos recém-adquiridos](active-directory-sso-newly-acquired-saas-apps.md)
- Saiba mais sobre o [acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md)
- Encontre [tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)
-	Adicione um aplicativo personalizado com a [Configuração de SAML de autoatendimento do Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)

<!---HONumber=Oct15_HO3-->