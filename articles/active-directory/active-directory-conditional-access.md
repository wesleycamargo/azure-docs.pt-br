---
title: "Acesso condicional no portal clássico do Azure | Microsoft Docs"
description: "Use o controle de acesso condicional no portal clássico do Azure para verificar se há condições específicas ao autenticar para acessar aplicativos."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: da3f0a44-1399-4e0b-aefb-03a826ae4ead
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: calebb
ms.custom: oldportal
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: b19fd8c0c41638ffec5fda32b6b1a855be2abc7b
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017

---
# <a name="conditional-access-in-the-azure-classic-portal"></a>Acesso condicional no portal clássico do Azure

Este tópico é sobre acesso condicional no portal clássico do Azure. Para obter informações mais recentes sobre o acesso condicional no Azure Active Directory, consulte [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).


Os recursos de controle de acesso condicional do Azure AD (Azure Active Directory) oferecem maneiras simples para ajudar a proteger recursos na nuvem e local. Políticas de acesso condicional, como autenticação multifator, podem ajudar a proteger contra o risco de roubo e captura de credenciais. Outras políticas de acesso condicional podem ajudar a proteger os dados de sua organização. Por exemplo, além de exigir credenciais, você pode ter uma política para que apenas dispositivos registrados em um sistema de gerenciamento de dispositivos móveis, como o Microsoft Intune, possam acessar serviços confidenciais de sua organização.

## <a name="prerequisites"></a>Pré-requisitos
O acesso condicional do Azure AD é um recurso do [Azure Active Directory Premium](http://www.microsoft.com/identity). Cada usuário que acessa um aplicativo com políticas de acesso condicional aplicadas deve ter uma licença do Azure AD Premium. Você pode saber mais sobre os requisitos de licença no [relatório de usuário sem licença](https://aka.ms/utc5ix).

## <a name="how-is-conditional-access-control-enforced"></a>Como o controle de acesso condicional é imposto?
Com o controle de acesso condicional em vigor, o Azure AD verifica condições específica que você define para um usuário acessar um aplicativo. Depois que os requisitos de acesso são atendidos, o usuário é autenticado e pode acessar o aplicativo.  

![Visão geral do acesso condicional](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Conditions
Estas são as condições que podem ser incluídas em uma política de acesso condicional:

* **Associação de grupo**. Controle o acesso do usuário com base na associação em grupo.
* **Local**. Use o local do usuário para disparar a autenticação multifator e usar os controles quando um usuário não estiver em uma rede confiável.
* **Plataforma de dispositivos**. Use a plataforma do dispositivo, como iOS, Android, Windows Mobile ou Windows, como uma condição para a aplicação de política.
* **Habilitado pelo dispositivo**. O estado do dispositivo, se está habilitado ou desabilitado, é validado durante a avaliação da política de dispositivo. Se você desabilitar um dispositivo perdido ou roubado no diretório, ele não poderá mais atender aos requisitos de política.
* **Risco de entrada e usuário**. Você pode usar o [Azure AD Identity Protection](active-directory-identityprotection.md) para políticas de risco de acesso condicional. Políticas de risco de acesso condicional ajudam a oferecer a proteção avançada da organização com base em eventos de risco e atividades de entrada incomuns.

## <a name="controls"></a>Controles
Estes são controles que podem ser usados para impor uma política de acesso condicional:

* **Autenticação multifator**. Você pode exigir autenticação forte por meio da autenticação multifator. Você pode usar a autenticação multifator com a autenticação multifator do Azure ou usando um provedor de autenticação multifator local, combinado a AD FS (Serviços de Federação do Active Directory). Usar a autenticação multifator ajuda a proteger recursos para impedir que sejam acessados por um usuário não autorizado que obteve acesso às credenciais de um usuário válido.
* **Bloquear**. Você pode aplicar condições como o local do usuário para bloquear o acesso do usuário. Por exemplo, você pode bloquear o acesso quando um usuário não está em uma rede confiável.
* **Dispositivos em conformidade**. Você pode definir políticas de acesso condicional no nível do dispositivo. Você pode configurar uma política para que somente os computadores que ingressaram no domínio ou dispositivos móveis que são registrados em um aplicativo de gerenciamento de dispositivo móvel possam acessar os recursos de sua organização. Por exemplo, você pode usar o Intune para verificar a conformidade do dispositivo e relatá-lo ao Azure AD para imposição quando o usuário tenta acessar um aplicativo. Para obter orientações detalhadas sobre como usar o Intune para proteger aplicativos e dados, confira [Proteger os aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). Você também pode usar o Intune para aplicar a proteção de dados a dispositivos perdidos ou roubados. Para saber mais, confira [Ajudar a proteger seus dados com apagamento completo ou seletivo usando o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Aplicativos
Você pode impor uma política de acesso condicional no nível do aplicativo. Defina níveis de acesso para aplicativos e serviços na nuvem ou no local. A política é aplicada diretamente ao site ou serviço. A política é aplicada para acesso ao navegador e aos aplicativos que acessam o serviço.

## <a name="device-based-conditional-access"></a>Acesso condicional com base em dispositivo
Você pode restringir o acesso a aplicativos de dispositivos que estejam registrados no Azure AD e que atendam a condições específicas. O acesso condicional com base em dispositivo protege os recursos de uma organização de usuários que tentam acessar os recursos de:

* Dispositivos desconhecidos ou não gerenciados.
* Dispositivos que não atendem às políticas de segurança que sua organização configurou.

Você pode definir políticas com base nestes requisitos:

* **Dispositivos associados ao domínio**. Defina uma política para restringir o acesso a dispositivos que fazem parte de um domínio do Active Directory local e que também estão registrados no Azure AD. Essa política se aplica a desktops Windows, laptops e tablets empresariais.
  Para saber mais sobre como configurar o registro automático de dispositivos Windows ingressados no domínio com o Azure AD, confira [Configurar o registro automático de dispositivos Windows ingressados no domínio com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
* **Dispositivos em conformidade**. Defina uma política para restringir o acesso a dispositivos que estão marcados como **em conformidade** no diretório do sistema de gerenciamento. Essa política garante que apenas os dispositivos que atendam às políticas de segurança como imposição de criptografia de arquivos em um dispositivo tenham permissão para acesso. Você pode usar esta política para restringir o acesso dos seguintes dispositivos:
  
  * **Dispositivos associados ao domínio do Windows**. Gerenciado pelo System Center Configuration Manager (na ramificação atual) implantado em uma configuração híbrida.
  * **Dispositivos pessoais ou de trabalho do Windows Mobile 10**. Gerenciado pelo Intune ou por um sistema de gerenciamento de dispositivo móvel com suporte de terceiros.
  * **iOS and Android devices**. Gerenciado pelo Intune.

Usuários que acessam aplicativos protegidos por um dispositivo com base em política de autoridade de certificação devem acessar o aplicativo em um dispositivo que atende aos requisitos desta política. Acesso negado se tentado em um dispositivo que não atende aos requisitos de política.

Para obter informações sobre como configurar uma política de autoridade de certificação baseada em dispositivo, no Azure AD, confira [Definir a política de acesso condicional com base no dispositivo para aplicativos conectados ao Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Recursos
Confira as seguintes categorias de recursos e artigos para saber mais sobre como configurar o acesso condicional para sua organização.

### <a name="multi-factor-authentication-and-location-policies"></a>Políticas de local e de autenticação multifator
* [Introdução ao acesso condicional para aplicativos conectados ao Azure AD com base em grupo, local e políticas de autenticação multifator](active-directory-conditional-access-azuread-connected-apps.md)
* [Aplicativos e navegadores com suporte](active-directory-conditional-access-supported-apps.md)

### <a name="device-based-conditional-access"></a>Acesso condicional com base em dispositivo
* [Definir a política de acesso condicional com base no dispositivo para controle de acesso de aplicativos conectados no Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
* [Configurar o registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Solução de problemas para problemas de acesso do Azure Active Directory](active-directory-conditional-access-device-remediation.md)
* [Ajudar a proteger dados em dispositivos perdidos ou roubados usando o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### <a name="protect-resources-based-on-sign-in-risk"></a>Proteger recursos com base em risco de entrada
* [Azure AD Identity Protection](active-directory-identityprotection.md)

### <a name="next-steps"></a>Próximas etapas
* [Perguntas frequentes sobre acesso condicional](active-directory-conditional-faqs.md)
* [Referência técnica](active-directory-conditional-access-technical-reference.md)


