<properties
   pageTitle="Integrar o logon único do Active Directory do Azure a aplicativos recém-adquiridos| Microsoft Azure"
   description="O Active Directory do Azure dá suporte ao logon único para aplicativos de SaaS recém-adquiridos para habilitar o gerenciamento de acesso centralizado no portal do Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/09/2015"
      ms.author="curtand"/>

# Integrar o logon único do Active Directory do Azure a aplicativos recém-adquiridos  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Para começar a configurar o logon único para um aplicativo que está trazendo para sua organização, você usará um diretório existente no Active Directory do Azure. Você pode usar um diretório do Azure AD que você obtém por meio do Microsoft Azure, Office 365 ou Windows Intune. Se você tiver dois ou mais deles, consulte [Administrar seu diretório do Azure AD](active-directory-administer.md) para determinar qual deles usar.

## Outras considerações

### Autenticação

Para aplicativos que dão suporte aos protocolos SAML 2.0, WS-Federation ou OpenID Connect, o Active Directory do Azure usa certificados de assinatura para estabelecer relações de confiança. Para obter mais informações sobre isso, consulte [Gerenciando certificados para logon único federado](active-directory-sso-certs.md).

Para aplicativos que dão suporte apenas ao logon baseado em formulários HTML, o Active Directory do Azure usa a “compartimentação de senha” para estabelecer relações de confiança. Isso permite que os usuários em sua organização entrem automaticamente em um aplicativo de SaaS pelo Azure AD usando as informações de conta do aplicativo de SaaS. O Azure AD coleta e armazena com segurança as informações de conta de usuário e a senha relacionada. Para obter mais informações, consulte [Logon único baseado em senha](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

### Autorização

Uma conta provisionada permite que um usuário autorizado use um aplicativo após ser autenticado por meio de logon único. O provisionamento do usuário pode ser feito manualmente ou, em alguns casos, você pode adicionar e remover informações do usuário do aplicativo de SaaS com base nas alterações feitas no Active Directory do Azure. Para saber mais sobre o uso de conectores existentes do Azure AD para provisionamento automatizado, consulte [Provisionamento e desprovisionamento do usuário automatizados para aplicativos de SaaS](active-directory-saas-app-provisioning.md)

Caso contrário, você pode adicionar manualmente informações do usuário a um aplicativo ou usar outras soluções de provisionamento que estão disponíveis no marketplace.

### Access

O Azure AD fornece várias maneiras personalizáveis de implantar aplicativos para os usuários finais em sua organização. Você não está limitado a nenhuma implantação ou solução de acesso específica. Você pode usar [a solução que melhor atender às suas necessidades](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## Próximas etapas

Para aplicativos de SaaS que já estão em sua organização e que você deseje habilitar para logon único, consulte [Integrar SSO do Active Directory do Azure com aplicativos existentes ](active-directory-sso-integrate-existing-apps.md)

Para aplicativos de SaaS que você encontrar na Galeria de aplicativos, o Active Directory do Azure fornece inúmeros [tutoriais sobre como integrar aplicativos de SaaS](active-directory-saas-tutorial-list.md).

Se o aplicativo não estiver na Galeria de aplicativos, você poderá [adicioná-lo à Galeria de aplicativos do Active Directory do Azure como um aplicativo personalizado](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Há muito mais detalhes sobre todos esses temas na biblioteca do Azure.com, começando com [O que é o acesso a aplicativos e logon único com o Active Directory do Azure.](active-directory-appssoaccess-whatis.md).

<!---HONumber=Oct15_HO3-->