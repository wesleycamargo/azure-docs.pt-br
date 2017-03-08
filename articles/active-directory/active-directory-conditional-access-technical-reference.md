---
title: "Referência técnica do acesso condicional ao Azure Active Directory | Microsoft Docs"
description: "Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 4296bbc7123f6571ad564351612864a6315d8abf
ms.openlocfilehash: 450f3e001a0bc4a45fea4c4f0a81e676e9a80cc4
ms.lasthandoff: 03/02/2017


---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referência técnica do acesso condicional ao Azure Active Directory
## <a name="services-enabled-with-conditional-access"></a>Serviços habilitados com acesso condicional
Regras de acesso condicional têm suporte em vários tipos de aplicativos do AD do Azure. Essa lista inclui:


* Aplicativos registrados com o Proxy de Aplicativo do Azure
* Aplicativo Remoto do Azure
* Aplicativos de linha de negócios e de multilocação desenvolvidos registrados no AD do Azure
* Dynamics CRM
* Aplicativos federados da galeria de aplicativos do Azure AD
* Microsoft Office 365 Yammer
* Microsoft Office 365 Exchange Online
* Microsoft Office 365 SharePoint Online (inclui o OneDrive for Business)
* Microsoft Power BI 
* Aplicativos de SSO de senha da galeria de aplicativos do Azure AD
* Visual Studio Online


## <a name="enable-access-rules"></a>Habilitar regras de acesso
Cada regra pode ser habilitada ou desabilitada com base no aplicativo. Quando as regras estão **ATIVADAS** , elas são habilitadas e impostas aos usuários que acessam o aplicativo. Quando estão **DESATIVADAS** , elas não são usadas e não afetam a experiência de entrada dos usuários.

## <a name="applying-rules-to-specific-users"></a>Aplicar regras a usuários específicos
As regras podem ser aplicadas a conjuntos específicos de usuários baseados no grupo de segurança definindo **Aplicar a**. **Aplicar a** pode ser definido como **Todos os Usuários** ou **Grupos**. Quando definido como **Todos os Usuários** , as regras são aplicadas a qualquer usuário com acesso ao aplicativo. A opção **Grupos** permite que grupos de segurança e de distribuição específicos sejam selecionados e as regras sejam impostas somente para esses grupos.

Ao implantar uma regra, é comum aplicá-la primeiro a um conjunto limitado de usuários, que são membros de um grupo piloto. Após a conclusão, a regra pode ser aplicada a **Todos os Usuários**. Isso fará com que a regra a seja imposta a todos os usuários na organização.

Alguns grupos também podem ser isentos da política usando a opção **Exceto** . Todos os membros desses grupos estarão isentos mesmo que apareçam em um grupo incluído.

## <a name="at-work-networks"></a>Redes "No trabalho"
As regras de acesso condicional que usam uma rede "No trabalho" dependem de intervalos de endereços IP confiáveis que foram configurados no Azure AD ou que usam a declaração “inside corpnet” do AD FS. Essas regras incluem:

* Exigir autenticação multifator quando não está no trabalho
* Bloquear acesso quando não estiver no trabalho

Opções para especificar redes "no trabalho"

1. Configurar intervalos de endereços IP na [página de configurações da autenticação multifator](../multi-factor-authentication/multi-factor-authentication-whats-next.md). A política de acesso condicional usará os intervalos configurados em cada solicitação de autenticação e emissão de token para avaliar as regras. 
2. Configure o uso da declaração inside corpnet; essa opção pode ser usada com diretórios federados, usando o AD FS. Para saber mais sobre as declarações inside corpnet, veja [IPs confiáveis](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="rules-based-on-application-sensitivity"></a>Regras com base na confidencialidade do aplicativo
As regras são configuradas por aplicativo, permitindo que os serviços de alto valor sejam protegidos sem afetar o acesso a outros serviços. As regras de acesso condicional podem ser configuradas na guia **Configurar** do aplicativo. 

Regras oferecidas atualmente:

* **Exigir autenticação multifator**
  
  * Todos os usuários aos quais essa política se aplica deverão realizar a autenticação por meio da autenticação multifator pelo menos uma vez.
* **Exigir autenticação multifator quando não está no trabalho**
  
  * Se essa política for aplicada, todos os usuários terão que ter realizado a autenticação multifator pelo menos uma vez se acessarem o serviço de um local remoto não comercial. Se os usuários saírem de um local de trabalho para um local remoto, precisarão realizar a autenticação multifator ao acessar o serviço.
* **Bloquear acesso quando não estiver no trabalho** 
  
  * Quando os usuários saírem de um local de trabalho para um local remoto, eles serão bloqueados se a política “Bloquear acesso quando não estiver no trabalho” estiver aplicada a eles.  Eles terão o acesso permitido novamente quando estiverem em um local de trabalho.

## <a name="related-topics"></a>Tópicos relacionados
* [Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Active Directory do Azure](active-directory-conditional-access.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)


