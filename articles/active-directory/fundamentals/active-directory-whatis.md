---
title: O que é o Microsoft Azure AD (Azure Active Directory)? | Microsoft Docs
description: Use o Active Directory do Azure para estender suas identidades locais existentes para a nuvem ou desenvolva aplicativos integrados do AD do Azure.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 5087f759d682382bc22b5f95f462fe0f08619cc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449987"
---
# <a name="what-is-azure-active-directory"></a>O que é o Active Directory do Azure?
O Microsoft Azure AD (Azure Active Directory) é o serviço de gerenciamento de identidades e diretório baseado em nuvem multilocatário da Microsoft que combina os principais serviços de diretório, gerenciamento do acesso de aplicativos e proteção de identidade em uma única solução. O Azure AD também oferece uma plataforma avançada, baseada em padrões, que permite que os desenvolvedores ofereçam controle de acesso aos aplicativos com base em regras e políticas centralizadas.

![Pilha do Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Para administradores de TI.** O Microsoft Azure AD oferece uma solução mais segura à sua organização por meio do uso de um gerenciamento de identidades e acesso de SSO (logon único) mais forte a milhares de [aplicativos SaaS baseados em nuvem](../saas-apps/tutorial-list.md) e aplicativos locais. Por meio desses aplicativos, você também terá segurança de aplicativo baseada em nuvem, acesso contínuo, colaboração avançada e automação do ciclo de vida de identidades para os funcionários, ajudando a aumentar tanto a segurança quanto a conformidade.

    Além disso, com apenas [quatro cliques](./../connect/active-directory-aadconnect-get-started-express.md), é possível integrar o Microsoft Azure AD a um Windows Server Active Directory, permitindo que a organização utilize seus investimentos em identidades locais existentes para gerenciar o acesso a aplicativos SaaS baseado em nuvem.

- **Para desenvolvedores de aplicativos.** O Microsoft Azure AD permite que você concentre-se na criação dos aplicativos, permitindo a integração com uma solução de gerenciamento de identidades utilizada por milhões de organizações em todo o mundo.

- **Para clientes do Office 365, Azure ou Dynamics CRM Online.** Você já está usando o Microsoft Azure AD. Cada locatário do Office 365, Azure e Dynamics CRM Online é, na verdade, um locatário do Microsoft Azure AD, permitindo que você comece imediatamente a gerenciar o acesso de seus funcionários aos aplicativos integrados em nuvem.

## <a name="how-reliable-is-azure-ad"></a>O AD do Azure é confiável?
O projeto multilocatário, distribuído geograficamente e de alta disponibilidade do AD do Azure significa que você pode confiar nele para suas necessidades comerciais mais críticas. Com execução em 28 data centers em todo o mundo com failover automático, você terá o conforto de saber que o AD do Azure é altamente confiável e que, mesmo se um data center ficar inativo, cópias de seu diretório de dados estarão ativas em pelo menos dois data centers mais dispersos regionalmente e disponíveis para acesso instantâneo.

Para obter mais informações sobre contratos de nível de serviço, consulte[Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Escolher uma edição
Todos os serviços comerciais do Microsoft Online dependem do Azure Active Directory (Azure AD) para entrada e outras necessidades de identidade. Se você assinar qualquer um dos serviços comerciais do Microsoft Online (por exemplo, Office 365 ou Microsoft Azure), obterá o Azure AD com acesso a todos os recursos gratuitos. Com a edição Gratuita do Active Directory do Azure, você pode gerenciar usuários e grupos, sincronizar com diretórios locais, obter logon único no Azure, Office 365 e em milhares de aplicativos SaaS populares como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e muito mais. 

Para aprimorar seu Active Directory do Azure, é possível adicionar recursos pagos usando as edições do Azure Active Directory Basic, Premium P1 e Premium P2. As edições pagas do Active Directory do Azure são criadas em seu diretório gratuito existente, fornecendo recursos corporativos que abrangem autoatendimento, monitoramento avançado, relatórios de segurança, MFA (Autenticação Multifator) e acesso seguro para a sua força de trabalho móvel.

> [!NOTE]
> Para obter as opções de preço dessas edições, confira [Preço do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/). O Azure Active Directory Premium P1, Premium P2 e Azure Active Directory Basic não têm suporte atualmente na China. Para obter mais informações sobre preços do Microsoft Azure AD, contate o Fórum do Azure Active Directory.
>

* **Azure Active Directory Basic** - Projetado para trabalhos com necessidades imediatas de nuvem, esta edição fornece acesso ao aplicativo centrado em nuvem e soluções de gerenciamento de identidades de autoatendimento. Com a Basic edition do Active Directory do Azure, você obtém recursos que aumentam sua produtividade e reduzem os custos, como o gerenciamento de acesso baseado em grupo, redefinição de senha por autoatendimento para aplicativos em nuvem e o Proxy de Aplicativo do Active Directory do Azure (para publicar aplicativos web locais usando o Active Directory do Azure), tudo com o apoio de um SLA de nível empresarial de 99,9% de tempo de atividade.
* **Azure Active Directory Premium P1** – Projetado para capacitar as organizações com necessidades mais exigentes de gerenciamento de identidades e acesso, a Active Directory Premium edition do Azure adiciona funcionalidades de gerenciamento de identidades de nível empresarial rica em recursos e permite que os usuários híbridos acessem diretamente as funcionalidades locais e na nuvem. Esta edição inclui tudo o que é necessário para profissionais de informações e administradores de identidades em ambientes híbridos no acesso a aplicativos, identidade de autoatendimento e IAM (gerenciamento de acesso), Identity Protection e segurança na nuvem. Ela dá suporte a recursos avançados de administração e delegação, como grupos dinâmicos e gerenciamento de grupos por autoatendimento. Inclui o Microsoft Identity Manager (um pacote de gerenciamento de identidades e acesso local) e fornece recursos de write-back na nuvem, possibilitando soluções como a redefinição de senha por autoatendimento para seus usuários locais.
* **Azure Active Directory Premium P2** – Projetado com proteção avançada para todos os usuários e administradores, essa nova oferta inclui todos os recursos no Azure AD Premium P1, bem como Identity Protection e Privileged Identity Management. O Azure Active Directory Identity Protection utiliza bilhões de sinais para fornecer acesso condicional com base em risco aos seus aplicativos e dados importantes da empresa. Podemos também ajudar a gerenciar e proteger contas privilegiadas com o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar os administradores e o acesso a recursos e fornecer acesso just-in-time quando necessário.  

> [!NOTE]
> Uma série de recursos do Active Directory do Azure está disponível por meio de edições “pré-pagas”:
>
> * O Active Directory B2C é a solução de gerenciamento de identidades e acesso para seus aplicativos voltados para o consumidor. Para obter mais informações, consulte [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * A Autenticação Multifator do Azure pode ser usado por usuário ou por provedores de autenticação. Para obter mais informações, consulte [O que é Autenticação Multifator do Microsoft Azure?](../authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Como posso começar?

**Se você for administrador de TI:**

* [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) - você pode inscrever-se para uma avaliação gratuita de 30 dias hoje e implantar sua primeira solução na nuvem em menos de cinco minutos usando este link

* Leia [Introdução ao Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) para obter dicas e truques sobre como colocar um locatário do Azure AD em funcionamento rapidamente

**Se você for um desenvolvedor:**
 
* Confira o [Guia de Desenvolvedores](../develop/azure-ad-developers-guide.md) para o Azure Active Directory

* [Inicie uma avaliação](https://azure.microsoft.com/trial/get-started-active-directory/) –  inscreva-se para uma avaliação gratuita de 30 dias hoje mesmo e comece a integrar seus aplicativos ao Microsoft Azure AD

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre os conceitos básicos do gerenciamento de identidade e acesso do Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
