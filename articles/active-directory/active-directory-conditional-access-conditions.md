---
title: Quais são as condições no acesso condicional do Active Directory do Azure? | Microsoft Docs
description: Saiba como as condições são usadas no acesso condicional do Active Directory do Azure para acionar uma política.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 59ae7e83bc2b76cb679b0baf1f5739f28ec7046c
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248273"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Quais são as condições no acesso condicional do Active Directory do Azure? 

Você pode controlar como usuários autorizados acessam seus aplicativos na nuvem usando [acesso condicional do Azure AD (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal). Em uma política de acesso condicional, você define a resposta ao motivo para acionar sua política. Um exemplo de resposta é **Então faça isso**. Um motivo de exemplo é **Quando isso acontece**.

![Motivo e resposta](./media/active-directory-conditional-access-conditions/10.png)


No contexto do acesso condicional, **Quando isso acontece,** é chamado de **condição**. **Então, isso** é chamado de **controle de acesso**. A combinação de suas condições e seus controles de acesso representa uma política de acesso condicional.

![Política de acesso condicional](./media/active-directory-conditional-access-conditions/61.png)


As condições que você não configurou em uma política de acesso condicional não são aplicadas. Algumas condições foram [obrigatórias](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices) para aplicar uma política de acesso condicional para um ambiente. 

Este artigo é uma visão geral das condições e como elas são usadas em uma política de acesso condicional. 

## <a name="users-and-groups"></a>Usuários e grupos

A condição de usuários e grupos é obrigatória em uma política de acesso condicional. Em sua política, você pode selecionar **Todos os usuários** ou escolher usuários e grupos específicos.

![Usuários e grupos](./media/active-directory-conditional-access-conditions/111.png)

Quando você seleciona **Todos os usuários**, sua política é aplicada a todos os usuários no diretório, incluindo usuários convidados.

Quando você **selecionar usuários e grupos**, poderá definir as seguintes opções:

* **Todos os usuários convidados** segmentam uma política para usuários convidados B2B. Essa condição corresponde a qualquer conta de usuário que tenha o atributo **userType** definido como **guest**. Você pode usar essa configuração quando uma política precisa ser aplicada assim que a conta é criada em um fluxo de convite no AD do Azure.

* **Funções de diretório** segmentam uma política com base na atribuição de função de um usuário. Essa condição suporta funções de diretório como **Administrador global** ou **Administrador de senha**.

* **Usuários e grupos** segmentam conjuntos específicos de usuários. Por exemplo, você pode selecionar um grupo que contenha todos os membros do departamento de RH quando um aplicativo de RH for selecionado como o aplicativo na nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

Você também pode excluir usuários ou grupos específicos de uma política. Um caso de uso comum é as contas de serviço, se a sua política aplicar a autenticação multifator (MFA). 

Especificar determinados conjuntos de usuários é útil para a implantação de uma nova política. Em uma nova política, você deve ter como destino apenas um conjunto inicial de usuários para validar o comportamento da política. 



## <a name="cloud-apps"></a>Aplicativos na nuvem 

Um aplicativo na nuvem é um site ou serviço. Os sites protegidos pelo Proxy de Aplicativo do Azure AD também são aplicativos na nuvem. Para uma descrição detalhada dos aplicativos na nuvem compatíveis, consulte [atribuições de aplicativos em nuvem](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

A condição **de aplicativos na nuvem** é obrigatória em uma política de acesso condicional. Em sua política, é possível selecionar **Todos os aplicativos em nuvem** ou escolher aplicativos específicos.

![Incluir aplicativos de nuvem](./media/active-directory-conditional-access-conditions/03.png)

- Selecione **Todos os aplicativos na nuvem** para as políticas de linha de base a serem aplicadas a toda a organização. Um caso de uso comum para essa seleção é uma política que exige autenticação multifator quando o risco de entrada for detectado para qualquer aplicativo de nuvem. Uma política aplicada a **Todos os aplicativos na nuvem** se aplica ao acesso a todos os sites e serviços. Essa configuração não se limita aos aplicativos em nuvem exibidos na lista **Selecionar aplicativos**. 

- Selecione aplicativos individuais na nuvem para segmentar serviços específicos por política. Por exemplo, você pode exigir que os usuários tenham um [dispositivo compatível](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) para acessar o SharePoint Online. Essa política também é aplicada a outros serviços quando eles acessam o conteúdo do SharePoint. Um exemplo é o Microsoft Teams. 

Você pode excluir aplicativos específicos de uma política. No entanto, esses aplicativos ainda estão sujeitos às políticas aplicadas aos serviços que acessam. 



## <a name="sign-in-risk"></a>Risco de entrada

Um risco de login é um indicador da alta, média ou baixa probabilidade de que uma tentativa de login não tenha sido feita pelo proprietário legítimo de uma conta de usuário. O Azure AD calcula o nível de risco de entrada durante a entrada do usuário. O nível de risco de login calculado pode ser uma condição em uma política de acesso condicional. 

![Níveis de risco de login](./media/active-directory-conditional-access-conditions/22.png)

Para usar essa condição, você precisa ter a [Azure Active Directory Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection-enable) habilitada.
 
Casos de uso comuns para essa condição são políticas que possuem as seguintes proteções: 

- Bloquear usuários com alto risco de login. Essa proteção impede que usuários potencialmente não legítimos acessem seus aplicativos na nuvem. 
- Exigir autenticação multifatorial para usuários com risco médio de login. Ao aplicar a autenticação multifator, você pode fornecer confiança adicional de que o login é feito pelo proprietário legítimo de uma conta.

Para obter mais informações, consulte [Entradas de risco](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-security-risky-sign-ins).  

## <a name="device-platforms"></a>Plataformas de dispositivo

A plataforma do dispositivo é caracterizada pelo sistema operacional que é executado no seu dispositivo. O Microsoft Azure Active Directory identifica a plataforma usando as informações fornecidas pelo dispositivo, como o agente do usuário. Esta informação não é verificada. Recomendamos que todas as plataformas tenham uma política aplicada a elas. A política deve bloquear o acesso, exigir conformidade com as políticas do Microsoft Intune ou exigir que o dispositivo seja associado ao domínio. O padrão é aplicar uma política a todas as plataformas de dispositivos. 


![Configurar plataformas de dispositivos](./media/active-directory-conditional-access-conditions/24.png)

Para obter uma lista das plataformas de dispositivos compatíveis, consulte [condição da plataforma de dispositivos](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#device-platform-condition).


Um caso de uso comum para essa condição é uma política que restringe o acesso aos seus aplicativos de nuvem para [dispositivos gerenciados](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Para ver mais cenários, incluindo a condição de plataforma do dispositivo, confira [Acesso condicional com base em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Estado do dispositivo

A condição de estado do dispositivo exclui dispositivos e dispositivos associados híbridos do Azure AD e dispositivos marcados como compatíveis a partir de uma política de acesso condicional. Essa condição é útil quando uma política deve se aplicar apenas a um dispositivo não gerenciado para fornecer segurança adicional à sessão. Por exemplo, apenas impor o controle de sessão do Microsoft Cloud App Security quando um dispositivo não é gerenciado. 


![Configurar o estado do dispositivo](./media/active-directory-conditional-access-conditions/112.png)

Se você quiser bloquear o acesso de dispositivos não gerenciados, implemente [acesso condicional baseado em dispositivo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).


## <a name="locations"></a>Locais

Usando locais, você pode definir condições com base em onde uma conexão foi tentada. 

![Configurar locais](./media/active-directory-conditional-access-conditions/25.png)

Casos de uso comuns para essa condição são políticas com as seguintes proteções:

- Exigir autenticação multifatorial para usuários que acessam um serviço quando estão fora da rede corporativa.  

- Bloquear o acesso para usuários que acessam um serviço de países ou regiões específicas. 

Para obter mais informações, consulte [Qual é a condição de local no acesso condicional do Active Directory do Azure?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Aplicativos cliente

Usando a condição de aplicativos do cliente, você pode aplicar uma política a diferentes tipos de aplicativos. Exemplos são sites, serviços, aplicativos móveis e aplicativos de área de trabalho. 



Um aplicativo é classificado da seguinte maneira:

- Um site ou serviço se usar protocolos SSO da web, SAML, WS-Fed ou OpenID Connect para um cliente confidencial.

- Um aplicativo de aplicativo ou desktop para dispositivos móveis se ele usar o aplicativo móvel OpenID Connect para um cliente nativo.

Para obter uma lista dos aplicativos cliente que você pode usar na política de acesso condicional, consulte [Condição dos aplicativos cliente](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition) na referência técnica do Acesso Condicional do Active Directory do Azure.

Casos de uso comuns para essa condição são políticas com as seguintes proteções: 

- Exigir um dispositivo [compatível](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) para aplicativos móveis e de área de trabalho que baixem grandes quantidades de dados para o dispositivo. Ao mesmo tempo, permita o acesso do navegador a partir de qualquer dispositivo.

- Bloqueie o acesso de aplicativos da Web, mas permita o acesso de aplicativos móveis e de desktop.

Você pode aplicar essa condição ao SSO da Web e a protocolos de autenticação modernos. Você também pode aplicá-lo a aplicativos de e-mail que usem o Microsoft Exchange ActiveSync. Exemplos são os aplicativos de correio nativos na maioria dos smartphones. Atualmente, os aplicativos clientes que usam protocolos herdados precisam ser protegidos usando os Serviços de Federação do Azure AD.

Você só pode selecionar a condição de aplicativos cliente se o Microsoft Office 365 Exchange Online for o único aplicativo em nuvem selecionado.

![Aplicativos na nuvem](./media/active-directory-conditional-access-conditions/32.png)

A seleção do **Exchange ActiveSync** como uma condição de aplicativos cliente é suportada somente se você não tiver outras condições configuradas em uma política. No entanto, você pode restringir o escopo dessa condição para se aplicar apenas a plataformas suportadas.

 
![Aplicar política apenas a plataformas suportadas](./media/active-directory-conditional-access-conditions/33.png)

A aplicação dessa condição apenas a plataformas suportadas é igual a todas as plataformas de dispositivos em uma condição de [plataforma de dispositivos](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).

![Configurar plataformas de dispositivos](./media/active-directory-conditional-access-conditions/34.png)


 Para obter mais informações, consulte estes artigos:

- [Configure o SharePoint Online e o Exchange Online para acesso condicional do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Acesso condicional baseado no aplicativo do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam). 


### <a name="legacy-authentication"></a>Autenticação herdada  

O acesso condicional agora se aplica a clientes antigos do Microsoft Office que não suportam autenticação moderna. Também se aplica a clientes que usam protocolos de e-mail como POP, IMAP e SMTP. Usando a autenticação legada, você pode configurar políticas como **bloquear o acesso de outros clientes**.


![Configurar aplicativos cliente](./media/active-directory-conditional-access-conditions/160.png)  


#### <a name="known-issues"></a>Problemas conhecidos

- A configuração de uma política para **Outros clientes** bloqueia determinados clientes, como SPConnect, para toda a organização. Esse bloqueio acontece porque os clientes mais antigos são autenticados de maneiras inesperadas. O problema não se aplica aos principais aplicativos do Office, como os antigos clientes do Office. 

- Pode levar até 24 horas para que a política entre em vigor. 


#### <a name="frequently-asked-questions"></a>Perguntas frequentes

**P:** Esta autenticação bloqueará os Serviços Web do Microsoft Exchange?

Depende do protocolo de autenticação usado pelo Exchange Web Services. Se o aplicativo Serviços Web do Exchange usar autenticação moderna, ele será abordado pelo aplicativo cliente **Aplicativos móveis e clientes de área de trabalho**. A autenticação básica é coberta pelo aplicativo cliente **Outros clientes**.


**P:** Quais controles posso usar para **Outros clientes**?

Qualquer controle pode ser configurado para **Outros clientes**. No entanto, a experiência do usuário final será bloqueada para todos os casos. **Outros clientes** não suportam controles como MFA, dispositivo compatível e ingresso no domínio. 
 
**P:** Quais condições posso usar para **Outros clientes**?

Qualquer condição pode ser configurada para **Outros clientes**.

**P:** O Exchange ActiveSync suporta todas as condições e controles?

Não. A lista a seguir resume o suporte do Exchange ActiveSync: 

- O Exchange ActiveSync oferece suporte somente à segmentação por usuário e grupo. Não oferece suporte a convidados ou funções. Se uma condição de convidado ou função for configurada, todos os usuários serão bloqueados. O Exchange ActiveSync bloqueia todos os usuários porque não pode determinar se a diretiva deve ser aplicada ao usuário ou não.

- O Exchange ActiveSync funciona somente com o Microsoft Exchange Online como o aplicativo na nuvem. 

- O Exchange ActiveSync não oferece suporte a nenhuma condição, exceto o próprio aplicativo cliente. 

- O Exchange ActiveSync pode ser configurado com qualquer controle. Todos os controles, exceto a conformidade do dispositivo, levam a um bloqueio.

**P:** As políticas se aplicam a todos os aplicativos clientes por padrão no futuro?

Não. Não há mudanças no comportamento da política padrão. As políticas continuam a ser aplicadas a aplicativos móveis e de navegador e clientes de desktop por padrão.



## <a name="next-steps"></a>Próximas etapas

- Para saber como configurar uma política de acesso condicional, consulte [Guia de início rápido: exigir MFA para aplicativos específicos com acesso condicional do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-app-based-mfa).

- Para configurar políticas de acesso condicional para o seu ambiente, consulte as [Práticas recomendadas para acesso condicional no Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices). 

