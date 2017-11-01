---
title: Novidades no Azure Active Directory | Microsoft Docs
description: "Use estas notas de versão para ficar a par das **versões mais recentes versões**, **problemas conhecidos**, **correções de bug**, **funcionalidades preteridas** e **planos de alterações** no Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 0361d5b48af876af2ccc522351484d369554bfca
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory

Estamos constantemente aperfeiçoando o Azure Active Directory para você. Para que você esteja a par das últimas novidades do desenvolvimento, este tópico fornece informações sobre:

-   As versões mais recentes 
-   Problemas conhecidos 
-   Correções de bug 
-   Funcionalidades preteridas 
-   Planos de alterações 

Revisite esta página regularmente, pois estamos realizando atualizações mensalmente.


## <a name="october-2017"></a>Outubro de 2017

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto**: gerenciamento de ciclo de vida de identidade  


**As APIs de relatórios do Azure AD (versão beta) serão preteridas no nó `https://graph.windows.net/<tenant-name>/reports/`**

O Portal do Azure oferece:

- Um novo console de administração do Azure Active Directory 
- Novas APIs para gerar relatórios de atividades e de segurança
 
Devido a essas novas funcionalidades, as APIs de relatórios sob o ponto de extremidade **/reports** serão desativadas em 10 de dezembro de 2017. 

---

**Tipo:** corrigido   
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** SSO  


O Azure Active Directory dá suporte à detecção automática campo de entrada para os aplicativos que renderizam um campo de nome de usuário e senha HTML.  Essas etapas são documentadas em [Como capturar automaticamente os campos de entrada para um aplicativo](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Você pode encontrar essa funcionalidade adicionando um aplicativo *Inexistente na galeria* na página **Aplicativos empresariais** do [Portal do Azure](http://aad.portal.azure.com). Além disso, você pode configurar o modo de **Logon único** neste novo aplicativo para **Logon único baseado em senha** digitando uma URL da Web e salvando a página.
 
Devido a um problema de serviço, essa funcionalidade ficou temporariamente desabilitada por um período. O problema foi resolvido e a detecção automática do campo de entrada está disponível novamente.

---

**Tipo:** novo recurso  
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** Identity Security & Protection  


No mundo em que vivemos, a MFA (Autenticação Multifator) é uma parte essencial da proteção da sua organização. A equipe de identidade da Microsoft está evoluindo a autenticação multifator para tornar as credenciais mais adaptáveis e aperfeiçoar a experiência. Hoje temos o prazer de anunciar duas etapas importantes nessa jornada: 

- A integração do desafio multifator resulta diretamente no relatório de logon único do Azure AD, incluindo o acesso programático aos resultados da MFA

- Integração mais profunda da configuração de MFA com a experiência de configuração do Azure AD central no portal do Azure

Com essa visualização pública, os relatórios e gerenciamento de MFA são parte integrante da experiência de configuração do Azure AD central, permitindo gerenciar a funcionalidade do Portal de gerenciamento de MFA dentro a experiência do Azure AD.


---
**Tipo:** novo recurso  
**Categoria de serviço:** termos de uso  
**Funcionalidade do produto:** governança  


Os **termos de uso do Azure AD** fornecem um método simples para apresentar as informações aos usuários finais. Isso garante que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos legais ou de conformidade.

Use os termos de uso do Azure AD nos seguintes cenários:

- Termos gerais de uso para todos os usuários em sua organização. 

- Termos específicos de uso baseados nos atributos de um usuário (por exemplo, médicos vs enfermeiras ou funcionários locais vs internacionais, realizado por grupos dinâmicos). 

- Termos específicos de uso para acessar aplicativos de alto impacto nos negócios, como o Salesforce.

Para obter mais informações, consulte [Termos de Uso do Azure Active Directory](active-directory-tou.md).


---
**Tipo:** novo recurso  
**Categoria de serviço:** PIM  
**Funcionalidade do produto:** Privileged Identity Management  


Com o Azure Active Directory Privileged Identity Management (PIM), você pode gerenciar, controlar e monitorar aos Recursos do Azure (Visualização) em sua organização. Isso inclui assinaturas, grupos de recursos e até mesmo máquinas virtuais. Todos os recursos do Portal do Azure que aproveitam a funcionalidade de RBAC (Controle de Acesso Baseado em Volume) do Azure podem tirar proveito de todas as ótimas capacidades de segurança e gerenciamento de ciclo de vida que o Azure AD PIM tem a oferecer, bem como alguns excelentes novos recursos que estamos planejando lançar em breve para as funções do Azure AD.

Para saber mais, consulte [PIM para recursos do Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Tipo:** novo recurso  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** governança  


Novidades do Azure AD

As revisões de acesso (versão prévia) permitem que as organizações gerenciem com eficiência as associações de grupos e o acesso a aplicativos corporativos: 

- Você pode renovar o acesso do usuário convidado usando as revisões de acesso do acesso deles a aplicativos e a associações de grupos. As informações fornecidas pelas revisões de acesso permitem aos revisores decidir com eficiência se o acesso dos convidados deve continuar.

- Você pode renovar o certificado de acesso de funcionários a aplicativos e associações de grupos com as revisões de acesso.

Você pode coletar os controles de revisão de acesso em programas relevantes para que sua organização rastreie as análises de conformidade ou aplicativos suscetíveis a riscos.

Para obter mais informações, consulte [Revisões de acesso do Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Tipo:** novo recurso  
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** SSO  


**Capacidade de ocultar os aplicativos de terceiros dos Meus aplicativos e do inicializador do Office 365**

Agora é possível gerenciar melhor os aplicativos que aparecem em seus portais de usuário por meio de uma nova propriedade **ocultar aplicativo**. Isso ajuda nos casos em que os blocos de aplicativos estão aparecendo para serviços de back-end ou há blocos duplicados que acabam por encher os inicializadores de aplicativos do usuário. O botão de alternância encontra-se na seção de propriedades do aplicativo de terceiros e é chamado **Visível para usuário?**. Também é possível ocultar um aplicativo programaticamente por meio do PowerShell. 

Para obter mais informações, consulte [Ocultar um aplicativo de terceiros da experiência do usuário no Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**O que está disponível?**

 Como parte da transição para o novo console de admin, criamos 2 novas APIs para recuperar os Logs de atividades do Azure AD disponíveis. O novo conjunto de APIs fornecem funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de entrada. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de eventos de risco do Identity Protection do Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

**Tipo:** recurso alterado  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto**: gerenciamento de ciclo de vida de identidade  


Um pacote cumulativo de atualizações de hotfix (build 4.4.1642.0) está disponível desde 25 de setembro de 2017 para o MIM (Microsoft Identity Manager) 2016 2016 Service Pack 1 (SP1). Esse pacote cumulativo:

- Resolve problemas e adiciona melhorias
- Ele é uma atualização cumulativa que substitui todas as atualizações do MIM 2016 SP1 até o build 4.4.1459.0 para o Microsoft Identity Manager 2016. 
- Você precisa ter o **Microsoft Identity Manager 2016 build 4.4.1302.0.** 

Para obter mais informações, consulte [Pacote cumulativo de atualizações de hotfix (build 4.4.1642.0) disponível para o Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---