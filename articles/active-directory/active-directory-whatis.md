---
title: "O que é o Active Directory do Azure?"
description: Use o Active Directory do Azure para estender suas identidades locais existentes para a nuvem ou desenvolva aplicativos integrados do AD do Azure.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 21cd9fa752f6f3bf2e3aab572ad3336fbd2bee92
ms.openlocfilehash: 067bef695d8d6a340f7974478e6580b10a1e8dc8


---
# <a name="what-is-azure-active-directory"></a>O que é o Active Directory do Azure?
O Active Directory do Azure (AD do Azure) é o serviço de gerenciamento de identidade e diretório multilocatário com base em nuvem da Microsoft.

Para administradores de TI, o AD do Azure oferece uma solução acessível e fácil de usar para fornecer a funcionários e parceiros de negócios acesso de logon único (SSO) a [milhares de aplicativos SaaS na nuvem](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx) , como Office365, Salesforce.com, DropBox e Concur.

Para desenvolvedores de aplicativos, o AD do Azure permite que você se concentre na criação de seu aplicativo, tornando rápida e simples a integração de uma solução de gerenciamento de identidade da mais alta qualidade e utilizada por milhões de empresas em todo o mundo.

O AD do Azure também inclui um conjunto completo de recursos de gerenciamento de identidade, incluindo multi-factor authentication, registro de dispositivos, gerenciamento de senhas de autoatendimento, gerenciamento de grupos de autoatendimento, gerenciamento de contas com privilégios, controle de acesso com base em função, monitoramento de uso de aplicativos, auditoria avançada e alertas e monitoramento de segurança. Esses recursos podem ajudar a proteger aplicativos com base na nuvem, simplificar os processos de TI, cortar custos e ajudar a garantir que as metas de conformidade corporativa sejam atingidas.

Além disso, com apenas [quatro cliques](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx), o AD do Azure pode ser integrado a um Active Directory do Windows Server existente, fornecendo às organizações a capacidade de aproveitar os investimentos locais de identidade existentes para gerenciar o acesso à nuvem com base em aplicativos SaaS.

Se você for um cliente do Office365, Azure ou Dynamics CRM Online, talvez não tenha percebido que já está usando o AD do Azure. Todo locatário do Office365, Azure e Dynamics CRM já é, na verdade, um locatário do AD do Azure. Quando desejar, você poderá começar a usar esse locatário para gerenciar o acesso a milhares de outros aplicativos na nuvem aos quais o AD do Azure se integra!

![Pilha do Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>O AD do Azure é confiável?
O projeto multilocatário, distribuído geograficamente e de alta disponibilidade do AD do Azure significa que você pode confiar nele para suas necessidades comerciais mais críticas. Com execução em 28 data centers em todo o mundo com failover automático, você terá o conforto de saber que o AD do Azure é altamente confiável e que, mesmo se um data center ficar inativo, cópias de seu diretório de dados estarão ativas em pelo menos dois data centers mais dispersos regionalmente e disponíveis para acesso instantâneo.

Para obter mais detalhes, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="what-are-the-benefits-of-azure-ad"></a>Quais são os benefícios do AD do Azure?
Sua organização pode usar o AD do Azure para melhorar a produtividade dos funcionários, simplificar os processos de TI, melhorar a segurança e reduzir custos de várias maneiras:

* Adote rapidamente os serviços de nuvem, fornecendo aos funcionários e parceiros uma experiência de logon único fácil da plataforma dos recursos de serviços de provisionamento e gerenciamento de acesso a aplicativos SaaS totalmente automatizados do AD do Azure.
* Capacite os funcionários com acesso aos aplicativos na nuvem da mais alta qualidade e recursos de serviço de autoatendimento de onde quer que eles precisem para trabalharem nos dispositivos que adoram usar.
* Gerencie com facilidade e segurança o acesso de funcionários e fornecedores a suas contas de mídia social corporativa.
* Aprimore a segurança dos aplicativos com o acesso condicional e a autenticação multifator do AD do Azure.
* Implemente um gerenciamento de acesso de aplicativo de autoatendimento consistente, permitindo que os proprietários de negócios migrem rapidamente e, ao mesmo tempo, reduzam a sobrecarga e os custos de TI.
* Monitore o uso dos aplicativos e proteja sua empresa contra ameaças avançadas com monitoramento e relatórios de segurança.
* Proteja o acesso móvel (remoto) a aplicativos locais.

## <a name="how-does-azure-ad-compare-to-on-premises-active-directory-domain-services-ad-ds"></a>Como o Azure AD pode ser comparado ao AD DS (Active Directory Domain Services) local?

Tanto o Azure AD (Azure Active Directory) quanto o Active Directory (Active Directory Domain Services ou AD DS) local são sistemas que armazenam dados de diretório e gerenciam a comunicação entre usuários e recursos, incluindo processos de logon do usuário, autenticação e pesquisas de diretório.

O AD DS é uma função de servidor no Windows Server, o que significa que ele pode ser implantado em máquinas físicas ou virtuais. Ele tem uma estrutura hierárquica baseada em X.500. Ele usa o DNS para localizar objetos, usa basicamente Kerberos para autenticação e é possível interagir com ele usando LDAP. O Active Directory permite OUs (unidades organizacionais) e GPOs (Objetos de Política de Grupo), além do ingresso de máquinas no domínio e as relações de confiança são criadas entre domínios.

O Azure AD é um serviço de diretório público para vários clientes, o que significa que dentro dele é possível criar um locatário para seus servidores e aplicativos de nuvem, como o Office 365. Usuários e grupos são criados em uma estrutura simples sem OUs ou GPOs. A autenticação é realizada por meio de protocolos como SAML, WS-Federation e OAuth. É possível consultar o Azure AD, mas em vez de usar o LDAP, você deve usar uma API REST chamado API do AD Graph. Todos eles usam HTTP e HTTPS.

Você pode usar o Azure AD Connect para sincronizar suas identidades locais com o Azure AD.

### <a name="authentication-and-authorization-details"></a>Detalhes de autenticação e autorização

| AD do Azure                                   | AD DS local |
|--------------------------------------------|-------------------|
| <li>   SAML      <li>   O certificado do provedor de identidade do Web Services Federation    <li>   Interativo com credenciais com suporte  <li>   OAuth 2.0   <li>   OpenID Connect                          | <li>   SAML   <li>   O certificado do provedor de identidade do Web Services Federation  <li>   NTLM  <li>   Kerberos   <li>   MD5   <li>   Basic          |

### <a name="object-repository-details"></a>Detalhes do repositório de objeto

| AD do Azure                                          | AD DS local |
|---------------------------------------------------|-------------------|
| Acesso por meio do Azure AD Graph e Microsoft Graph | LDAP X.500    |

### <a name="programmatic-access-details"></a>Detalhes do acesso programático

| AD do Azure                        | AD DS local |
|---------------------------------|-------------------|
| APIs REST do MS/Azure AD Graph | LDAP          |

### <a name="sso-to-applications-details"></a>Detalhes do SSO para aplicativos

| AD do Azure           | AD DS local   |
|--------------------|---------------------|
| <li>   OpenID Connect  <li>   SAML          | <li>   SAML      <li>   WS-Fed   <li>   Open-ID connect  |

### <a name="access-management-details"></a>Detalhes do gerenciamento de acesso

| AD do Azure         | AD DS local     |
|------------------------------------|------------------------------------------------------------------------|
| <li>   Controle de acesso baseado em função e escopo definido pelo recurso   <li>   Permissões de aplicativo e delegadas definidas pelo cliente        <li>   Estrutura de consentimento (impõe consentimento apropriado de administrador/usuário, conforme definido/solicitado pelo recurso/cliente)  <li>   Por meio da função de aplicativo, pode ser aplicado individualmente ou por meio de grupos, oferece suporte a:   <ul> <li>   Gerenciado pelo administrador       <li>   Acesso de aplicativo de autoatendimento  <li>   Consentimento do usuário </ul>         | <li>   Por meio de ACLs, pode ser aplicado individualmente ou por meio de grupos, oferece suporte a:  <ul><li>   Gerenciado pelo administrador  </ul>                                                 |

### <a name="group-management-details"></a>Detalhes do gerenciamento de grupos

| AD do Azure                          | AD DS local                                 |
|-----------------------------------|---------------------------------------------------|
| <li>   Gerenciado pelo administrador    <li>   Gerenciamento dinâmico/regra   <li>   Gerenciamento de grupo de autoatendimento  | <li>   Gerenciado pelo administrador      <li>   Sistema externo (FIM, ou outro) obrigatório para:  <ul><li>   Gerenciamento dinâmico/regra      </ul>                 |

### <a name="supported-credentials-details"></a>Detalhes de credenciais com suporte

| AD do Azure           | AD DS local  |
|--------------------|--------------------|
| <li>   Nome de usuário + senha  <li>   Cartão inteligente     | <li>   Nome de usuário + senha  <li>   Cartão inteligente     |


## <a name="how-can-i-get-started"></a>Como posso começar?
* Se você for administrador de TI:
  * [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/)  - você pode se inscrever em uma avaliação gratuita de 30 dias hoje mesmo e implantar sua primeira solução de nuvem em menos de 5 minutos usando este link
  * Leia "Introdução ao Azure AD" para obter dicas e truques sobre como colocar um locatário do Azure AD em funcionamento rapidamente
* Se você for um desenvolvedor:
  * Confira nosso [Guia de desenvolvedores](active-directory-developers-guide.md) para o Active Directory do Azure
  * [Inicie uma avaliação](https://azure.microsoft.com/trial/get-started-active-directory/): inscreva-se para uma avaliação gratuita de 30 dias hoje mesmo e comece a integrar seus aplicativos ao Azure AD

## <a name="where-can-i-learn-more"></a>Onde posso saber mais?
Temos inúmeros ótimos recursos online para ajudá-lo a aprender tudo sobre o AD do Azure. Aqui está uma lista de artigos excelentes para você começar:

* [Habilitando seu diretório para gerenciamento híbrido com o Azure AD Connect](active-directory-aadconnect.md)
* [Segurança adicional para um mundo sempre conectado](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Introdução aos Relatórios do AD do Azure](active-directory-reporting-getting-started.md)
* [Gerenciar suas senhas de qualquer lugar](active-directory-passwords.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md)
* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [O que é o licenciamento do Active Directory do Microsoft Azure?](active-directory-licensing-what-is.md)
* [Como descobrir aplicativos na nuvem não aprovados, usados em minha organização](active-directory-cloudappdiscovery-whatis.md)



<!--HONumber=Nov16_HO4-->


