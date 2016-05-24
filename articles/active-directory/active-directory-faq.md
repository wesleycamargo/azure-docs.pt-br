<properties
	pageTitle="Perguntas frequentes sobre o Azure Active Directory | Microsoft Azure"
	description="Perguntas frequentes sobre o Azure Active Directory"
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
	ms.date="04/20/2016"
	ms.author="markusvi"/>

# Perguntas frequentes sobre o Azure Active Directory

O Azure Active Directory (Azure AD) é um conjunto abrangente de recursos de gerenciamento de identidades e acesso baseado na nuvem que fornece IDaaS (Identidade como um Serviço). Como um provedor de IDaaS, o Azure AD o habilita a controlar "*quem tem permissão para fazer o que*" na rede de computadores. Além disso, o Azure AD fornece SSO (Logon Único) para os aplicativos. Com o SSO, você pode melhorar significativamente a experiência de logon dos usuários. Para saber mais, confira [O que é o Azure Active Directory](active-directory-whatis.md).



## Acessar o Azure e o Azure Active Directory

****P: por que não consigo localizar o Azure AD no portal do Azure (http://portal.azure.com)?**

**R:** a experiência de administração do Azure AD ainda está disponível no portal clássico do Azure (`http://manage.windowsazure.com`). Uma visualização pública das novas experiências de administração integradas ao portal do Azure está planejada para ser lançada em setembro. Para obter as últimas informações sobre lançamentos, confira o [Blog da Equipe do Active Directory](https://blogs.technet.microsoft.com/ad/).


---

**** P: por que é mostrado "Nenhuma assinatura encontrada" quando tento acessar o Azure AD no portal clássico do Azure (http://manage.windowsazure.com)?**

**R:** você precisará de permissões em uma assinatura do Azure se tiver uma assinatura paga do Office 365 ou do Azure AD. Navegue até [http://aka.ms/accessAAD](http://aka.ms/accessAAD) para obter uma etapa única de ativação. Caso contrário, você precisará ativar uma [avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/) completa ou uma assinatura paga.

---

**P: qual é a relação entre o Azure AD, o Office 365 e o Azure?**

**R:** o Microsoft Azure é uma infraestrutura e plataforma de computação em nuvem aberta, flexível e de nível empresarial para compilar, implantar e gerenciar aplicativos e serviços por meio de uma rede global de data centers gerenciados pela Microsoft. O Azure oferece, por exemplo, soluções de SaaS (Software como um Serviço) como o Office 365. O Office 365 é um grupo de assinaturas de software mais serviços que fornecem software de produtividade e serviços online relacionados para você. O Azure AD complementa o Office 365 com um conjunto abrangente de recursos de gerenciamento de identidades e acesso baseados na nuvem que fornecem IDaaS (Identidade como um Serviço). Se tiver uma assinatura do Office 365, você também já terá o Azure AD em execução no Azure.


---

**P: quais recursos do Azure AD estão disponíveis gratuitamente?**

**R:** todos os recursos comuns em uma assinatura do Azure estão disponíveis gratuitamente. Para obter uma lista completa desses recursos, confira [Recursos comuns](active-directory-editions.md/#common-features).



---

P: **como conectar meu diretório local ao Azure AD?**

R: você pode conectar seu diretório local ao Azure AD usando o **Azure AD Connect**. Para saber mais, confira [Integrar identidades locais ao Azure Active Directory](active-directory-aadconnect.md).


---

**P: como configurar o SSO entre meu diretório local e meus aplicativos de nuvem?**

**R:** você só precisa configurar o SSO entre seu diretório local e o Azure AD. Desde que os aplicativos em nuvem sejam gerenciados pelo Azure AD, você pode estender o alcance do SSO para o ambiente local implementando a federação com o AD FS ou a sincronização de senhas. Ambas as opções estão incluídas no **Azure AD Connect**. Para saber mais, confira [Integrar identidades locais ao Azure Active Directory](active-directory-aadconnect.md).
  


---

**P: o Azure AD me ajuda a gerenciar a infraestrutura local?**

**R:** sim, ele faz isso. O Azure AD Premium edition fornece o **Connect Health**. O Azure AD Connect Health ajuda no monitoramento e na obtenção de informações sobre a sua infraestrutura de identidade local e os serviços de sincronização. Para saber mais, confira [Monitorar a infraestrutura de identidade local e os serviços de sincronização na nuvem](active-directory-aadconnect-health.md).



## Gerenciamento de senhas

**P: posso usar o write-back de senha do Azure AD sem sincronização de senhas? (Ou seja, eu gostaria de usar o Azure AD SSPR com o write-back de senha, mas não quero que as senhas sejam armazenadas na nuvem.)**

**R:** não é necessário sincronizar as senhas do AD no Azure AD para habilitar o write-back. Em um ambiente federado, o SSO do Azure AD utiliza o diretório local para autenticar o usuário. Esse cenário não requer que a senha local seja acompanhada no Azure AD.

---

**P: quanto tempo leva para que uma senha seja gravada de volta no AD local?**

**R:** o write-back de senha opera em tempo real. Para saber mais, confira [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)


---

**P: posso usar o write-back de senha com senhas que são gerenciadas por um administrador?**

**R:** sim, se você tiver o write-back de senha habilitado, as operações de senha executadas por um administrador serão gravadas de volta no ambiente local. Para obter mais respostas a perguntas relacionadas a senhas, confira [Perguntas frequentes sobre gerenciamento de senhas](active-directory-passwords-faq.md).



## Acesso a aplicativos


**P: onde obter uma lista de aplicativos que estão pré-integrados ao Azure AD e seus recursos?**

**R:** o Azure AD tem mais de 2600 aplicativos pré-integrados da Microsoft, de provedores de serviços de aplicativos ou de parceiros. Para obter uma lista completa dos aplicativos pré-integrados, confira o [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**P: e se o aplicativo de que preciso não estiver no Azure AD Marketplace?**

**R:** com o Azure AD Premium, você pode adicionar e configurar qualquer aplicativo que desejar. Além disso, dependendo dos recursos do aplicativo e de suas preferências, você pode configurar o SSO e o provisionamento automatizado. Para obter mais informações, consulte:

- [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](active-directory-saas-custom-apps.md)
- [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md) 



---

**P: posso adicionar aplicativos que estou executando no local?**

**R:** o Proxy de Aplicativo do Azure AD o habilita a acessar facilmente os aplicativos Web locais, da mesma forma como os aplicativos SaaS no Azure Active Directory, sem a necessidade de uma VPN ou de alterações na infraestrutura de rede. Para saber mais, confira [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).


--- 

**P: como exigir MFA para usuários que acessam um aplicativo específico?**

**R:** com o acesso condicional do Azure AD, você pode atribuir uma política de acesso exclusiva para cada aplicativo. Na política, é possível exigir MFA sempre ou quando os usuários não estiverem conectados à rede local. Para saber mais, confira [Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Azure Active Directory](active-directory-conditional-access.md).

<!---HONumber=AcomDC_0518_2016-->