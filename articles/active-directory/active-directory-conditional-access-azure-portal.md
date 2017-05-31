---
title: Acesso condicional ao Azure Active Directory | Microsoft Docs
description: "Use o controle de acesso condicional no Azure Active Directory para verificar se há condições específicas ao autenticar para acessar aplicativos."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5a1ce66e02943caedd52976c5dcb3cf75c23bd49
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="conditional-access-in-azure-active-directory"></a>Acesso condicional no Azure Active Directory

> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-conditional-access-azure-portal.md)
> * [Portal clássico do Azure](active-directory-conditional-access.md)

Em um mundo móvel e em nuvem, o Azure Active Directory permite o logon único para dispositivos, aplicativos e serviços de qualquer lugar. Com a proliferação de dispositivos (incluindo BYOD), trabalho fora de redes corporativas e aplicativos de SaaS de terceiros, os profissionais de TI têm duas metas opostas:

- Capacitar os usuários finais para serem produtivos sempre e em qualquer lugar
- Proteger os ativos corporativos a qualquer momento

Para melhorar a produtividade, o Azure Active Directory fornece aos seus usuários uma ampla gama de opções para acessar seus recursos corporativos. Com o gerenciamento de acesso do aplicativo, o Azure Active Directory permite que você garanta que apenas *as pessoas certas* possam acessar seus aplicativos. Mas e se você quiser ter mais controle sobre a forma como as pessoas estão acessando os recursos sob determinadas condições? E se você ainda tiver condições sob as quais deseja bloquear o acesso a determinados aplicativos, mesmo para as *pessoas certas*? Por exemplo, você pode permitir que as pessoas certas acessem determinados aplicativos em uma rede confiável; no entanto, você talvez não queira que eles acessem esses aplicativos de uma rede em que você não confia. Você pode tratar essas questões usando o acesso condicional.

O acesso condicional é um recurso do Azure Active Directory que permite que você aplique controles de acesso a aplicativos ao seu ambiente com base em condições específicas. Com controles, também é possível vincular requisitos adicionais para o acesso ou bloqueá-lo. A implementação de acesso condicional baseia-se em políticas. Uma abordagem baseada em política simplifica sua experiência de configuração porque ela segue sua maneira de pensar sobre os requisitos de acesso.  

Normalmente, você define os requisitos de acesso usando as instruções com base no seguinte padrão:

![Controle](./media/active-directory-conditional-access-azure-portal/10.png)

Quando você substitui as duas ocorrências "*disso*" por informações do mundo real, você tem um exemplo de uma declaração de política que provavelmente parece familiar:

*Quando prestadores de serviço estiverem tentando acessar nossos aplicativos de nuvem de redes que não sejam confiáveis, bloquear o acesso.*

A declaração de política acima destaca o poder de acesso condicional. Embora você possa permitir que os prestadores de serviço acessem de forma básica seus aplicativos de nuvem (**quem**), com o acesso condicional, você também pode definir condições sob as quais o acesso é possível (**como**).

No contexto do Acesso condicional ao Azure Active Directory,

- "**Quando isso acontecer**" chama-se **instrução de condição**
- "**, faça isso**" chama-se **controles**

![Controle](./media/active-directory-conditional-access-azure-portal/11.png)

A combinação de uma instrução de condição com seus controles representa uma política de acesso condicional.

![Controle](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Controles

Em uma política de acesso condicional, os controles definem o que é que deverá acontecer quando uma instrução de condição tiver sido atendida.  
Com controles, você pode bloquear o acesso ou permitir acesso com requisitos adicionais.
Quando você configura uma política que permita o acesso, precisa selecionar pelo menos um requisito.   

### <a name="grant-controls"></a>Controles de concessão
A implementação atual do Azure Active Directory permite que você configure os seguintes requisitos de controles de concessão:

![Controle](./media/active-directory-conditional-access-azure-portal/05.png)

- **Autenticação Multifator** - você pode exigir autenticação forte por meio da autenticação multifator. Como o provedor, você pode usar a Autenticação Multifator do Azure ou um provedor de autenticação multifator local, combinados com os serviços de Federação do Active Directory (AD FS). Usar a autenticação multifator ajuda a proteger recursos para impedir que sejam acessados por um usuário não autorizado que obteve acesso às credenciais de um usuário válido.

- **Dispositivo compatível** - você pode definir políticas de acesso condicional no nível do dispositivo. Você pode configurar uma política para habilitar somente os computadores compatíveis ou dispositivos móveis registrados em um aplicativo de gerenciamento de dispositivo móvel possam acessar os recursos de sua organização. Por exemplo, você pode usar o Intune para verificar a conformidade do dispositivo e relatá-lo ao Azure AD para imposição quando o usuário tenta acessar um aplicativo. Para obter diretrizes detalhadas sobre como usar o Intune para proteger aplicativos e dados, confira Proteger os aplicativos e dados com o Microsoft Intune. Você também pode usar o Intune para aplicar a proteção de dados a dispositivos perdidos ou roubados. Para saber mais, confira Ajudar a proteger seus dados com apagamento completo ou seletivo usando o Microsoft Intune.

- **Dispositivo ingressado no domínio** – você pode exigir que o dispositivo que você usou para se conectar ao Azure Active Directory seja um dispositivo ingressado no domínio. Essa política se aplica a desktops Windows, laptops e tablets empresariais. Para saber mais sobre como configurar o registro automático de dispositivos ingressados no domínio com o Azure AD, confira [Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows](active-directory-conditional-access-automatic-device-registration.md).

Se você tiver mais de um requisito selecionado na política de acesso condicional, também poderá configurar os requisitos para aplicá-las. Você optar por exigir todos os controles selecionados ou um deles.

![Controle](./media/active-directory-conditional-access-azure-portal/06.png)

### <a name="session-controls"></a>Controles de sessão
Controles de sessão permitem a limitação da experiência dentro de um aplicativo na nuvem. Os controles de sessão são impostos por aplicativos de nuvem e contam com informações adicionais sobre a sessão fornecidas pelo Azure AD para o aplicativo.

![Controle](./media/active-directory-conditional-access-azure-portal/session-control-pic.png)

#### <a name="use-app-enforced-restrictions"></a>Usar restrições de aplicativo impostas
Você pode usar esse controle para exigir que o Azure AD passe as informações de dispositivo para o aplicativo na nuvem. Isso ajuda o aplicativo de nuvem a saber se o usuário vem de um dispositivo em conformidade ou um dispositivo ingressado no domínio. Esse controle atualmente só tem suporte com o SharePoint como o aplicativo na nuvem. O SharePoint usa as informações do dispositivo para fornecer uma experiência completa ou limitada aos usuários, dependendo do estado do dispositivo.
Para saber mais sobre como exigir acesso limitado com o SharePoint, acesse [aqui](https://aka.ms/spolimitedaccessdocs).

## <a name="condition-statement"></a>Instrução de condição

A seção anterior apresentou opções com suporte para bloquear ou restringir o acesso aos recursos na forma de controles. Em uma política de acesso condicional, você define os critérios que devem ser atendidos para que os controles sejam aplicados em forma de uma instrução de condição.  

Você pode incluir as atribuições a seguir em sua instrução de condição:

![Controle](./media/active-directory-conditional-access-azure-portal/07.png)


- **Quem** - em muitos casos, você deseja que os controles sejam aplicados a um conjunto específico de usuários. Em uma instrução de condição, você pode definir esse conjunto, selecionando os usuários e os grupos aos quais a política se aplica. Se necessário, você pode excluir explicitamente um conjunto de usuários de sua política ao dispensá-los.  
Selecionando usuários e grupos, você deve definir o escopo de usuários ao qual sua política se aplicará.    

    ![Controle](./media/active-directory-conditional-access-azure-portal/08.png)



- **O que** - normalmente, há certos aplicativos executados em seu ambiente que exigem, de uma perspectiva de proteção, mais atenção do que outros. Isso afeta, por exemplo, os aplicativos que têm acesso a dados confidenciais.
Selecionando aplicativos na nuvem, você define o escopo de aplicativos de nuvem aos quais sua política se aplica. Se necessário, você pode excluir explicitamente um conjunto de aplicativos de sua política.

    ![Controle](./media/active-directory-conditional-access-azure-portal/09.png)


- **Como** - desde que o acesso a seus aplicativos seja executado sob condições que você pode controlar, talvez não haja necessidade de impor controles adicionais sobre como seus aplicativos em nuvem são acessados pelos usuários. No entanto, tudo pode mudar se o acesso aos seus aplicativos de nuvem for executado, por exemplo, de redes não confiáveis ou de dispositivos não compatíveis. Em uma instrução de condição, você pode definir certas condições de acesso com requisitos adicionais sobre a execução do acesso aos seus aplicativos.

    ![Condições](./media/active-directory-conditional-access-azure-portal/21.png)


## <a name="conditions"></a>Condições

Na implementação atual do Azure Active Directory, você pode definir condições para as seguintes áreas:

- **Risco de conexão** – um risco de conexão é um objeto que é usado pelo Azure Active Directory para controlar a probabilidade de uma tentativa de conexão não ter sido realizada pelo proprietário legítimo de uma conta de usuário. Nesse objeto, a probabilidade (Alta, Média ou Baixa) é armazenada na forma de um atributo chamado [nível de risco de conexão](active-directory-reporting-risk-events.md#risk-level). Esse objeto será gerado durante uma conexão de um usuário riscos de conexão tiverem sido detectados pelo Azure Active Directory. Para obter mais detalhes, veja [Entradas arriscadas](active-directory-identityprotection.md#risky-sign-ins).  
Você pode usar o nível de risco de conexão calculado como uma condição em uma política de acesso condicional. 

    ![Condições](./media/active-directory-conditional-access-azure-portal/22.png)

- **Plataformas de dispositivo** – a plataforma do dispositivo é caracterizada pelo sistema operacional em execução no seu dispositivo (Android, iOS, Windows Phone, Windows). Você pode definir as plataformas de dispositivo incluídas, bem como as plataformas de dispositivo isentas de uma política.  
Para usar as plataformas de dispositivo na política, primeiro altere a configuração para **Sim** e selecione todos ou plataformas de dispositivos individuais às quais a política se aplica. Se você selecionar plataformas de dispositivos individuais, a política afetará apenas essas plataformas. Nesse caso, as entradas para outras plataformas com suporte não serão afetadas pela política.

    ![Condições](./media/active-directory-conditional-access-azure-portal/02.png)

- **Locais** - o local é identificado pelo endereço IP do cliente que você usou para se conectar ao Azure Active Directory. Essa condição exigirá que você esteja familiarizado com IPs Confiáveis. IPs Confiáveis é um recurso de autenticação multifator que permite que você defina intervalos de endereços IP confiáveis que representam a intranet local da sua organização. Quando você configura as condições de um local, IPs Confiáveis permite distinguir entre conexões feitas desde a rede da sua organização e de todos os outros locais. Para obter mais detalhes, veja [IPs confiáveis](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  
Você pode incluir todos os locais ou todos os IPs confiáveis e pode excluir todos os IPs confiáveis.

    ![Condições](./media/active-directory-conditional-access-azure-portal/03.png)


- **Aplicativo cliente** - o aplicativo cliente pode estar em um nível genérico do aplicativo (navegador da Wb, aplicativos móveis, cliente da área de trabalho) que você usou para se conectar ao Azure Active Directory ou pode selecionar especificamente o Exchange Active Sync.  
A autenticação herdada refere-se aos clientes usando a autenticação básica, como clientes do Office mais antigos que não usam a autenticação moderna. No momento, o acesso condicional não tem suporte na autenticação herdada.

    ![Condições](./media/active-directory-conditional-access-azure-portal/04.png)


## <a name="common-scenarios"></a>Cenários comuns

### <a name="requiring-multi-factor-authentication-for-apps"></a>Exibir a autenticação multifator para aplicativos

Muitos ambientes têm aplicativos que exigem um nível mais alto de proteção do que outros.
Isso é, por exemplo, o caso para aplicativos que têm acesso a dados confidenciais.
Se você quiser adicionar outra camada de proteção para esses aplicativos, poderá configurar uma política de acesso condicional que exija a autenticação multifator quando os usuários acessarem esses aplicativos.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Exigir autenticação multifator para acesso de redes que não são confiáveis

Esse cenário é semelhante ao cenário anterior porque adiciona um requisito para a autenticação multifator.
No entanto, a principal diferença é a condição para esse requisito.  
Embora o foco do cenário anterior fosse aplicativos com acesso a dados confidenciais, o foco deste cenário é em locais confiáveis.  
Em outras palavras, você pode ter um requisito para autenticação multifator se um aplicativo for acessado por um usuário de uma rede em que você não confia.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Somente os dispositivos confiáveis podem acessar os serviços do Office 365

Se você estiver usando o Intune em seu ambiente, poderá começar imediatamente usando a interface de política de acesso condicional no console do Azure.

Muitos clientes do Intune estão usando o acesso condicional para garantir que somente os dispositivos confiáveis possam acessar os serviços do Office 365. Isso significa que os dispositivos móveis estão registrados no Intune e atendem aos requisitos da política de conformidade e que os computadores com Windows fazem parte de um domínio local. Uma melhoria-chave é que você não precisa definir a mesma política para cada um dos serviços do Office 365.  Quando você criar uma nova política, configure os aplicativos de nuvem para incluir cada um dos aplicativos do O365 que você deseja proteger com o acesso condicional.

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Para obter mais detalhes sobre o que você deve saber e o que você deve evitar fazer ao configurar políticas de acesso condicional, consulte 
