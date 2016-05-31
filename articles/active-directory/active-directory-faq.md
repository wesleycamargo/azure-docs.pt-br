<properties
	pageTitle="Perguntas frequentes sobre o Azure Active Directory | Microsoft Azure"
	description="Perguntas frequentes do Azure Active Directory que fornecem respostas às perguntas, em conjunto com o acesso ao Azure e ao Azure Active Directory, o gerenciamento de senhas e o acesso a aplicativos."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/20/2016"
	ms.author="markusvi"/>

# Perguntas frequentes sobre o Azure Active Directory

O Azure Active Directory é uma solução abrangente de IDaaS (Identidade como um Serviço) que inclui todos os aspectos de identidade, gerenciamento de acesso e segurança.


Para saber mais, confira [O que é o Azure Active Directory?](active-directory-whatis.md).



## Acessar o Azure e o Azure Active Directory


****P: por que é mostrado "Nenhuma assinatura encontrada" quando tento acessar o Azure AD no portal clássico do Azure (https://manage.windowsazure.com)?**

**R:** o acesso ao portal clássico do Azure requer que cada usuário tenha permissões em uma assinatura do Azure. Se você tiver uma assinatura paga do Office 365 ou do Azure AD, navegue até [http://aka.ms/accessAAD](http://aka.ms/accessAAD) para obter uma etapa única de ativação. Caso contrário, você precisará ativar uma [avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/) completa ou uma assinatura paga.

Para obter mais informações, consulte:

- [Como as assinaturas do Azure estão associadas ao Active Directory do Azure](active-directory-how-subscriptions-associated-directory.md)

- [Gerenciar o diretório para sua assinatura do Office 365 no Azure](active-directory-manage-o365-subscription.md)

---

**P: qual é a relação entre o Azure AD, o Office 365 e o Azure?**

**R:** o Azure Active Directory fornece recursos comuns de identidade e acesso a todos os serviços online da Microsoft. Se está usando o Office 365, o Microsoft Azure, o Intune ou outros itens, você já está usando o Azure AD para habilitar a entrada e o gerenciamento de acesso para todos esses serviços.

De fato, todos os usuários que você habilitou para o Microsoft Online Services são definidos como contas de usuário em uma ou mais instâncias do Azure AD. Você pode habilitar essas contas para recursos gratuitos do Azure AD, como o acesso a aplicativos na nuvem.
 
Além disso, os serviços pagos do Azure AD (por exemplo: Azure AD Basic, Premium, EMS, etc.) complementam outros serviços online, como o Office 365 e o Microsoft Azure, com soluções abrangentes de gerenciamento e segurança de escala empresarial.


---



## Introdução ao Azure AD híbrido


**P: como conectar meu diretório local ao Azure AD?**

**R**: você pode conectar o diretório local ao Azure AD usando o **Azure AD Connect**.

Para saber mais, confira [Integrar identidades locais ao Azure Active Directory](active-directory-aadconnect.md).


---

**P: como configurar o SSO entre meu diretório local e meus aplicativos de nuvem?**

**R:** você só precisa configurar o SSO entre seu diretório local e o Azure AD. Contanto que você possa acessar seus aplicativos na nuvem por meio do Azure AD, o serviço direciona de forma automática os usuários para que se autentiquem corretamente com suas credenciais locais.

A implementação do SSO do local pode ser facilmente realizada com soluções de federação, como o ADFS, ou configurando a sincronização de hash de senha. Você pode implantar facilmente as duas opções usando o assistente de configuração do Azure AD Connect.
  

Para saber mais, confira [Integrar suas identidades locais ao Azure Active Directory](active-directory-aadconnect.md).
  

---

**P: o Azure Active Directory oferece um portal de autoatendimento para usuários em minha organização?**

**R:** sim, o Azure Active Directory oferece o [Painel de Acesso do Azure AD](http://myapps.microsoft.com) para o autoatendimento de usuários e o acesso ao aplicativo. Se você é cliente do Office 365, pode encontrar muitos dos mesmos recursos no portal do Office 365.

Para saber mais, confira [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).



---

**P: o Azure AD me ajuda a gerenciar a infraestrutura local?**

**R:** sim, ele faz isso. O Azure AD Premium Edition fornece o **Connect Health**. O Azure AD Connect Health ajuda no monitoramento e na obtenção de informações sobre a sua infraestrutura de identidade local e os serviços de sincronização.

Para saber mais, confira [Monitorar a infraestrutura de identidade local e os serviços de sincronização na nuvem](active-directory-aadconnect-health.md).

---

## Gerenciamento de senhas

**P: posso usar o write-back de senha do Azure AD sem sincronização de senhas? (Ou seja, eu gostaria de usar o Azure AD SSPR com o write-back de senha, mas não quero que as senhas sejam armazenadas na nuvem.)**

**R:** não é necessário sincronizar as senhas do AD no Azure AD para habilitar o write-back. Em um ambiente federado, o SSO do Azure AD utiliza o diretório local para autenticar o usuário. Esse cenário não requer que a senha local seja acompanhada no Azure AD.

---

**P: quanto tempo leva para que uma senha seja gravada de volta no AD local?**

**R:** o write-back de senha opera em tempo real.

Para saber mais, confira [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)


---

**P: posso usar o write-back de senha com senhas que são gerenciadas por um administrador?**

**R:** sim, se você tiver o write-back de senha habilitado, as operações de senha executadas por um administrador serão gravadas de volta no ambiente local.

Para obter mais respostas a perguntas relacionadas a senhas, confira [Perguntas frequentes sobre gerenciamento de senhas](active-directory-passwords-faq.md).

---

## Acesso a aplicativos


**P: onde obter uma lista de aplicativos que estão pré-integrados ao Azure AD e seus recursos?**

**R:** o Azure AD tem mais de 2600 aplicativos pré-integrados da Microsoft, de provedores de serviços de aplicativos ou de parceiros. Todos os aplicativos pré-integrados dão suporte ao SSO. O SSO o habilita a usar suas credenciais organizacionais para acessar os aplicativos. Alguns dos aplicativos também dão suporte ao provisionamento e ao desprovisionamento automatizados

Para obter uma lista completa dos aplicativos pré-integrados, confira o [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**P: e se o aplicativo de que preciso não estiver no Azure AD Marketplace?**

**R:** com o Azure AD Premium, você pode adicionar e configurar qualquer aplicativo que desejar. Dependendo dos recursos do aplicativo e de suas preferências, você pode configurar o SSO e o provisionamento automatizado.

Para obter mais informações, consulte:

- [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](active-directory-saas-custom-apps.md)
- [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md) 


---

**P: como os usuários entram em aplicativos que usam o Azure Active Directory?**
 
**R:** o Azure Active Directory fornece várias maneiras para que os usuários exibam e acessem os aplicativos, como:

- O painel de acesso do Azure AD

- O iniciador de aplicativos do Office 365

- Logon direto a aplicativos federados

- Links profundos a aplicativos federados, baseado em senha, ou existentes

Para saber mais, confira [Implantar o aplicativos integrados do Azure AD para usuários](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**P: quais são as diferentes maneiras pelas quais o Azure Active Directory habilita a autenticação e o logon único para aplicativos?**
 
**R:** o Azure Active Directory dá suporte a vários protocolos padronizados para autenticação e autorização, como SAML 2.0, OpenID Connect, OAuth 2.0 e WS-Federation. O Azure AD também dá suporte a cofres de senhas e recursos de entrada automatizada para aplicativos que dão suporte apenas à autenticação baseada em formulários.

Para obter mais informações, consulte:

- [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)

- [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Como funciona o logon único com o Active Directory do Azure?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**P: posso adicionar aplicativos que estou executando no local?**

**R:** o Proxy de Aplicativo do Azure AD oferece acesso fácil e seguro aos aplicativos Web locais que você escolhe. Você pode acessar os aplicativos da mesma maneira como acessa aplicativos de SaaS no Azure Active Directory. Não é necessário ter uma VPN nem alterar a infraestrutura de rede.

Para saber mais, confira [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).


--- 

**P: como exigir MFA para usuários que acessam um aplicativo específico?**

**R:** com o acesso condicional do Azure AD, você pode atribuir uma política de acesso exclusiva para cada aplicativo. Na política, é possível exigir MFA sempre ou quando os usuários não estiverem conectados à rede local.

Para saber mais, confira [Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Azure Active Directory](active-directory-conditional-access.md).


---

**P: o que é o provisionamento automatizado de usuário para aplicativos de SaaS?**

**R:** o Azure Active Directory permite automatizar a criação, a manutenção e a remoção de identidades de usuário em muitos aplicativos de nuvem (SaaS) populares.

Para saber mais, confira [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)

---

<!---HONumber=AcomDC_0525_2016-->