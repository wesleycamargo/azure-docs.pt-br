---
title: O que é o Microsoft Azure AD (Azure Active Directory)? | Microsoft Docs
description: Saiba como usar o Azure Active Directory para estender suas identidades locais existentes para a nuvem ou para desenvolver aplicativos integrados do Azure AD.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 755c301651e7c49faa8b05b2b443c5cce1a03c90
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364022"
---
# <a name="what-is-azure-active-directory"></a>O que é o Active Directory do Azure?
O Azure AD (Azure Active Directory) é o serviço de gerenciamento de diretório e identidade multilocatário baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, gerenciamento de acesso do aplicativo e proteção de identidade em uma única solução, oferecendo uma plataforma baseada em padrões que ajuda os desenvolvedores proporcionar controle de acesso para seus aplicativos, com base em regras e políticas centralizadas.

![Pilha do Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Benefícios do Azure AD
O Azure AD ajuda a:

-   Criar e gerenciar uma identidade única para cada usuário em toda a sua empresa, mantendo os usuários, grupos e dispositivos em sincronia com o [Azure AD Connect](../connect/active-directory-aadconnect.md).

-   Fornecer acesso de logon único para seus aplicativos incluindo milhares de aplicativos SaaS pré-integrados e fornecer acesso remoto seguro para aplicativos SaaS de locais usando o [Proxy de Aplicativo do Azure AD](../manage-apps/application-proxy.md).

-   Permitir segurança de acesso do aplicativo por meio da aplicação das políticas de [Autenticação Multifator](../authentication/concept-mfa-howitworks.md) com base em regras para aplicativos locais e na nuvem.

-   Melhorar a produtividade do usuário com [de redefinição de senha de autoatendimento](../user-help/user-help-reset-password.md)e o grupo e o aplicativo acessar solicitações usando o [portal MyApps](../user-help/active-directory-saas-access-panel-introduction.md).

-   Aproveitar o [alta disponibilidade e confiabilidade](https://docs.microsoft.com/azure/architecture/checklist/availability) de um empresariais em todo o mundo, solução de gerenciamento de identidades e acesso baseado em nuvem.

## <a name="who-uses-azure-ad"></a>Quem usa o Azure AD
O Azure AD destina-se aos administradores de TI, desenvolvedores de aplicativos e para usuários do Office 365, Azure ou Dynamics CRM Online.

- **Administradores de TI.** O Microsoft Azure AD oferece uma solução mais segura à sua organização por meio do uso de um gerenciamento de identidades e acesso de SSO (logon único) mais forte a milhares de [aplicativos SaaS baseados em nuvem](../saas-apps/tutorial-list.md) e aplicativos locais. Por meio desses aplicativos, você também terá segurança de aplicativo baseada em nuvem, acesso contínuo, colaboração avançada e automação do ciclo de vida de identidades para os usuários, ajudando a aumentar tanto a segurança quanto a conformidade.

    Além disso, com o [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md), é possível integrar o Azure AD a um Windows Server Active Directory, permitindo que a organização use seus investimentos em identidades locais existentes para gerenciar o acesso a aplicativos SaaS baseado em nuvem.

- **Para desenvolvedores de aplicativos.** O Azure AD ajuda você a se concentrar na criação dos aplicativos, permitindo a integração com uma solução de gerenciamento de identidades utilizada por milhões de organizações em todo o mundo.

- **Para clientes do Office 365, Azure ou Dynamics CRM Online.** Você já está usando o Microsoft Azure AD. Cada locatário do Office 365, Azure e Dynamics CRM Online é, na verdade, um locatário do Azure AD, permitindo que você comece imediatamente a gerenciar o acesso de seus usuários aos aplicativos integrados em nuvem.

## <a name="how-reliable-is-azure-ad"></a>O AD do Azure é confiável?
O projeto multilocatário, distribuído geograficamente e de alta disponibilidade do Azure AD significa que você pode confiar nele para suas necessidades comerciais mais críticas. Azure AD é executado em 28 data centers em todo o mundo com failover automático. Isso significa que, mesmo se um data center ficar inativo, cópias dos dados de seu diretório estarão ativas em pelo menos dois data centers mais dispersos regionalmente e estarão disponíveis para acesso instantâneo.

Para obter mais informações sobre contratos de nível de serviço, consulte[Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Escolher uma edição
Todos os serviços comerciais do Microsoft Online dependem do Azure AD para entrada e outras necessidades de identidade. Se você assinar qualquer um dos serviços comerciais do Microsoft Online (por exemplo, Office 365 ou Microsoft Azure), obterá automaticamente o Azure AD com acesso a todos os recursos gratuitos. Usando a edição do Azure Active Directory Gratuito, você pode gerenciar usuários e grupos, sincronizar com diretórios locais, obter logon único no Azure, Office 365 e em milhares de aplicativos SaaS populares como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e muito mais. 

Para aprimorar a implementação do Azure AD, também é possível adicionar recursos pagos atualizando para as edições do Azure Active Directory Basic, Premium P1 e Premium P2. As edições pagas do Azure AD são criadas em seu diretório gratuito existente, fornecendo recursos corporativos que abrangem autoatendimento, monitoramento avançado, relatórios de segurança, MFA (Autenticação Multifator) e acesso seguro para a sua força de trabalho móvel.

> [!NOTE]
> Para obter as opções de preço dessas edições, confira [Preço do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/). O Azure Active Directory Premium P1, Premium P2 e Azure Active Directory Basic não têm suporte atualmente na China. Para obter mais informações sobre preços do Microsoft Azure AD, contate o Fórum do Azure Active Directory.

- **Azure Active Directory Basic.** Destinado para profissionais de tarefas com necessidades de nuvem em primeiro lugar, esta edição fornece acesso a aplicativos centralizado na nuvem, bem como soluções de gerenciamento de identidades de autoatendimento. Com a edição Basic, você obtém recursos que aumentam sua produtividade e reduzem os custos, como o gerenciamento de acesso baseado em grupo, redefinição de senha por autoatendimento para aplicativos em nuvem e o Proxy de Aplicativo do Azure Active Directory (para publicar aplicativos Web locais usando o Azure AD), tudo com o apoio de um SLA corporativo de 99,9% de tempo de atividade.

- **Azure Active Directory Premium P1.** Destinado para capacitar organizações com mais necessidades de gerenciamento de identidades e acesso, a edição Azure Active Directory Premium adiciona capacidades de gerenciamento de identidades de nível corporativo e com muitos recursos, permitindo que usuários híbridos acessem perfeitamente os recursos locais e na nuvem. Esta edição inclui tudo o que é necessário para profissionais de informações e administradores de identidades em ambientes híbridos no acesso a aplicativos, identidade de autoatendimento e IAM (gerenciamento de acesso), Identity Protection e segurança na nuvem. Ela dá suporte a recursos avançados de administração e delegação, como grupos dinâmicos e gerenciamento de grupos por autoatendimento. Inclui o Microsoft Identity Manager (um pacote de gerenciamento de identidades e acesso local) e fornece recursos de write-back na nuvem, possibilitando soluções como a redefinição de senha por autoatendimento para seus usuários locais.

- **Azure Active Directory Premium P2.** Projetado com proteção avançada para os usuários e administradores, essa nova oferta inclui todos os recursos no Azure AD Premium P1, bem como Identity Protection e Privileged Identity Management. O Azure Active Directory Identity Protection utiliza bilhões de sinais para fornecer acesso condicional com base em risco aos seus aplicativos e dados importantes da empresa. Podemos também ajudar a gerenciar e proteger contas privilegiadas com o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar os administradores e o acesso a recursos e fornecer acesso just-in-time quando necessário.  

> [!NOTE]
> Uma série de recursos do Azure Active Directory também está disponível por meio de edições "pagas conforme o uso":<ul><li>**Azure Active Directory B2C.** Solução de gerenciamento de identidades e acesso para seus aplicativos voltados para o consumidor. Para obter mais informações, confira [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).</li><li>**Autenticação Multifator do Azure.** Provedor usado por usuário ou por autenticação. Para obter mais informações, confira [O que é Autenticação Multifator do Azure?](../authentication/multi-factor-authentication.md).

## <a name="as-an-admin-how-do-i-get-started"></a>Como administrador, como faço para começar?
Inscreva-se para uma avaliação gratuita de 30 dias e implante sua primeira solução de nuvem. Confira [Avaliação gratuita do Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="as-a-developer-how-do-i-get-started"></a>Como desenvolvedor, como faço para começar?
Inscreva-se para uma avaliação gratuita de 30 dias e comece a integrar seus aplicativos ao Azure AD. Confira [Avaliação gratuita do Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Para obter mais informações, veja também o [Guia de desenvolvedores](../develop/azure-ad-developers-guide.md) para o Azure Active Directory.

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre os conceitos básicos do gerenciamento de identidades e acesso do Azure](identity-fundamentals.md).

- [Integre o Azure AD com o Windows Server Active Directory](../hybrid/how-to-connect-install-express.md).