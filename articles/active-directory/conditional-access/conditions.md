---
title: Quais são as condições no acesso condicional do Active Directory do Azure? | Microsoft Docs
description: Saiba como as condições são usadas no acesso condicional do Active Directory do Azure para acionar uma política.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: f5b0472481a953b42d8dd89c1766e2c8064b3241
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449718"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Quais são as condições no acesso condicional do Active Directory do Azure? 

Você pode controlar como usuários acessam seus aplicativos na nuvem usando [acesso condicional do Azure AD (Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Em uma política de acesso condicional, você define a resposta ("Então, faça isso") para o motivo do acionamento de sua política ("Quando isso acontecer"). 

![Motivo e resposta](./media/conditions/10.png)


No contexto do acesso condicional, **Quando isso acontece,** é chamado de **condição**. **Então, isso** é chamado de **controle de acesso**. A combinação de suas condições e seus controles de acesso representa uma política de acesso condicional.

![Política de acesso condicional](./media/conditions/61.png)


As condições que você não configurou em uma política de acesso condicional não são aplicadas. Algumas condições foram [obrigatórias](best-practices.md) para aplicar uma política de acesso condicional para um ambiente. 

Este artigo é uma visão geral das condições e como elas são usadas em uma política de acesso condicional. 

## <a name="users-and-groups"></a>Usuários e grupos

A condição de usuários e grupos é obrigatória em uma política de acesso condicional. Em sua política, você pode selecionar **Todos os usuários** ou escolher usuários e grupos específicos.

![Usuários e grupos](./media/conditions/111.png)

Quando você seleciona **Todos os usuários**, sua política é aplicada a todos os usuários no diretório, incluindo usuários convidados.

Quando você **selecionar usuários e grupos**, poderá definir as seguintes opções:

* **Todos os usuários convidados** segmentam uma política para usuários convidados B2B. Essa condição corresponde a qualquer conta de usuário que tenha o atributo **userType** definido como **guest**. Use essa configuração quando uma política precisa ser aplicada assim que a conta é criada em um fluxo de convite no Azure AD.

* **Funções de diretório** segmentam uma política com base na atribuição de função de um usuário. Essa condição suporta funções de diretório como **Administrador global** ou **Administrador de senha**.

* **Usuários e grupos** segmentam conjuntos específicos de usuários. Por exemplo, você pode selecionar um grupo que contenha todos os membros do departamento de RH quando um aplicativo de RH for selecionado como o aplicativo na nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

Você também pode excluir usuários ou grupos específicos de uma política. Um caso de uso comum é as contas de serviço, se a sua política aplicar a autenticação multifator (MFA). 

Especificar determinados conjuntos de usuários é útil para a implantação de uma nova política. Em uma nova política, você deve ter como destino apenas um conjunto inicial de usuários para validar o comportamento da política. 



## <a name="cloud-apps"></a>Aplicativos na nuvem 

Um aplicativo na nuvem é um site ou serviço. Os sites protegidos pelo Proxy de Aplicativo do Azure AD também são aplicativos na nuvem. Para uma descrição detalhada dos aplicativos na nuvem compatíveis, consulte [atribuições de aplicativos em nuvem](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

A condição **de aplicativos na nuvem** é obrigatória em uma política de acesso condicional. Em sua política, é possível selecionar **Todos os aplicativos em nuvem** ou escolher aplicativos específicos.

![Incluir aplicativos de nuvem](./media/conditions/03.png)

Selecione:

- **Todos os aplicativos na nuvem** para as políticas de linha de base a serem aplicadas a toda a organização. Um caso de uso comum para essa seleção é uma política que exige autenticação multifator quando o risco de entrada for detectado para qualquer aplicativo de nuvem. Uma política aplicada a **Todos os aplicativos na nuvem** se aplica ao acesso a todos os sites e serviços. Essa configuração não se limita aos aplicativos em nuvem exibidos na lista **Selecionar aplicativos**. 

- **Selecionar aplicativos** para destinar sua política a serviços específicos. Por exemplo, você pode exigir que os usuários tenham um [dispositivo compatível](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) para acessar o SharePoint Online. Essa política também é aplicada a outros serviços quando eles acessam o conteúdo do SharePoint. Um exemplo é o Microsoft Teams. 

Você pode excluir aplicativos específicos de uma política. No entanto, esses aplicativos ainda estão sujeitos às políticas aplicadas aos serviços que acessam. 



## <a name="sign-in-risk"></a>Risco de entrada

Um risco de entrada é um indicador de probabilidade (alta, média ou baixa) de que uma tentativa de entrada não tenha sido feita pelo proprietário legítimo de uma conta de usuário. O Azure AD calcula o nível de risco de entrada durante a entrada do usuário. Você pode usar o nível de risco de conexão calculado como uma condição em uma política de acesso condicional.

![Níveis de risco de login](./media/conditions/22.png)

Para usar essa condição, você precisa ter a [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) habilitada.
 
Casos de uso comuns para essa condição são políticas que possuem as seguintes proteções: 

- Bloquear usuários com alto risco de login. Essa proteção impede que usuários potencialmente não legítimos acessem seus aplicativos na nuvem. 
- Exigir autenticação multifatorial para usuários com risco médio de login. Ao aplicar a autenticação multifator, você pode fornecer confiança adicional de que o login é feito pelo proprietário legítimo de uma conta.

Para obter mais informações, veja [bloquear acesso quando for detectado um risco na sessão](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Plataformas de dispositivo

A plataforma do dispositivo é caracterizada pelo sistema operacional que é executado no seu dispositivo. O Microsoft Azure Active Directory identifica a plataforma usando as informações fornecidas pelo dispositivo, como o agente do usuário. Esta informação não é verificada. Recomendamos que todas as plataformas tenham uma política aplicada a elas. A política deve bloquear o acesso, exigir conformidade com as políticas do Microsoft Intune ou exigir que o dispositivo seja associado ao domínio. O padrão é aplicar uma política a todas as plataformas de dispositivos. 


![Configurar plataformas de dispositivos](./media/conditions/24.png)

Para obter uma lista das plataformas de dispositivos compatíveis, consulte [condição da plataforma de dispositivos](technical-reference.md#device-platform-condition).


Um caso de uso comum para essa condição é uma política que restringe o acesso aos seus aplicativos de nuvem para [dispositivos gerenciados](require-managed-devices.md). Para ver mais cenários, incluindo a condição de plataforma do dispositivo, confira [Acesso condicional com base em aplicativo do Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>Estado do dispositivo

A condição de estado do dispositivo exclui dispositivos e dispositivos associados híbridos do Azure AD e dispositivos marcados como compatíveis a partir de uma política de acesso condicional. 


![Configurar o estado do dispositivo](./media/conditions/112.png)

Essa condição é útil quando uma política deve se aplicar apenas a um dispositivo não gerenciado para fornecer segurança adicional à sessão. Por exemplo, apenas impor o controle de sessão do Microsoft Cloud App Security quando um dispositivo não é gerenciado. 

## <a name="locations"></a>Locais

Usando locais, você pode definir condições com base em onde uma conexão foi tentada. 

![Configurar locais](./media/conditions/25.png)

Casos de uso comuns para essa condição são políticas com as seguintes proteções:

- Exigir autenticação multifatorial para usuários que acessam um serviço quando estão fora da rede corporativa.  

- Bloquear o acesso para usuários que acessam um serviço de países ou regiões específicas. 

Para obter mais informações, consulte [Qual é a condição de local no acesso condicional do Active Directory do Azure?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Aplicativos cliente

Por padrão, uma política de acesso condicional se aplica aos seguintes aplicativos:

- **[Aplicativos de navegador](technical-reference.md#supported-browsers)** – aplicativos de navegador incluem sites usando os protocolos SSO da Web OpenID Connnect, Web Services Federation ou SAML. Isso também se aplica a qualquer site ou serviço Web registrado como um cliente confidencial do OAuth. Por exemplo, o site do Office 365 SharePoint. 

- **[Aplicativos móveis e da área de trabalho usando a autenticação moderna](technical-reference.md#supported-mobile-applications-and-desktop-clients)** – esses aplicativos incluem os aplicativos de telefone e aplicativos desktop do Office. 


Além disso, você pode direcionar uma política para aplicativos cliente específicos que não estão usando autenticação moderna, por exemplo:

- **[Clientes do Exchange ActiveSync](conditions.md#exchange-activesync-clients)** – quando uma política bloqueia usando o Exchange ActiveSync, os usuários afetados recebem um email de quarentena único com informações sobre o motivo pelo qual estão bloqueados. Se necessário, o email inclui instruções para inscrever o dispositivo no Intune.

- **[Outros clientes](block-legacy-authentication.md)** – esses aplicativos incluem clientes que usam autenticação básica com protocolos de email, como IMAP, MAPI, POP, SMTP e aplicativos mais antigos do Office que não usam autenticação moderna. Para obter mais informações, veja [Como funciona a autenticação moderna para os aplicativos cliente do Office 2013 e do Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![Aplicativos cliente](./media/conditions/41.png)

Casos de uso comuns para essa condição são políticas com os seguintes requisitos:

- **[Exigir um dispositivo gerenciado](require-managed-devices.md)** para aplicativos móveis e de área de trabalho que baixem dados para um dispositivo. Ao mesmo tempo, permita o acesso do navegador a partir de qualquer dispositivo. Este cenário impede o salvamento e a sincronização de documentos para um dispositivo não gerenciado. Com esse método, você pode reduzir a probabilidade de perda de dados se o dispositivo é perdido ou roubado.

- **[Exigir um dispositivo gerenciado](require-managed-devices.md)** para aplicativos usando o ActiveSync para acessar o Exchange Online.

- **[Bloquear a autenticação herdada](block-legacy-authentication.md)** ao Azure AD (outros clientes)

- Bloqueie o acesso de aplicativos da Web, mas permita o acesso de aplicativos móveis e de desktop.



### <a name="exchange-activesync-clients"></a>Clientes do Exchange ActiveSync

Você só pode selecionar **clientes do Exchange ActiveSync** se:


- O Microsoft Office 365 Exchange Online é o único aplicativo de nuvem que você selecionou.

    ![Aplicativos na nuvem](./media/conditions/32.png)

- Você não tem outras condições configuradas em uma política. No entanto, você pode restringir o escopo dessa condição para se aplicar apenas a [plataformas compatíveis](technical-reference.md#device-platform-condition).
 
    ![Aplicar política apenas a plataformas suportadas](./media/conditions/33.png)


Quando o acesso é bloqueado porque um [dispositivo gerenciado](require-managed-devices.md) é necessário, os usuários afetados obtêm um único email orientando-os a usar o Intune. 

Se um aplicativo aprovado é necessário, os usuários afetados obtêm diretrizes para instalar e usar o cliente móvel do Outlook.

Em outros casos, por exemplo, se a MFA é necessária, os usuários afetados são bloqueados, porque os clientes que usam autenticação básica não dão suporte à MFA.

Você só pode direcionar essa configuração a usuários e grupos. Não oferece suporte a convidados ou funções. Se uma condição de convidado ou de função estiver configurada, todos os usuários poderão ser bloqueados porque o acesso condicional não poderá determinar se a política deve se aplicar ao usuário ou não.


 Para obter mais informações, consulte:

- [Configure o SharePoint Online e o Exchange Online para acesso condicional do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Acesso condicional baseado no aplicativo do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 



## <a name="next-steps"></a>Próximas etapas

- Para descobrir como configurar uma política de acesso condicional, consulte [Início Rápido: exigir MFA para aplicativos específicos com acesso condicional ao Azure Active Directory](app-based-mfa.md).

- Para configurar políticas de acesso condicional para o seu ambiente, consulte as [Práticas recomendadas para acesso condicional no Active Directory do Azure](best-practices.md). 

