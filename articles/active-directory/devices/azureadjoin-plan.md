---
title: Cenários de uso e considerações de implantação para a Junção do Azure AD| Microsoft Docs
description: Explica como os administradores podem configurar a Junção do AD do Azure para seus usuários finais (funcionários, estudantes, outros usuários). Ele também discute os diferentes cenários reais para usar a Junção do AD do Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
tags: azure-classic-portal
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: markvi
ms.openlocfilehash: a145b4184fbebd9c4f447face1c47bec20c0ec32
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415629"
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Cenários de uso e considerações de implantação para a Junção do Azure AD
## <a name="usage-scenarios-for-azure-ad-join"></a>Cenários de uso da Junção do Azure AD
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Cenário 1: empresas baseadas principalmente na nuvem
A Junção do Azure Active Directory (Junção do Azure AD) pode trazer benefícios se você operar e gerenciar identidades para seus negócios na nuvem ou se for migrar para a nuvem em breve. Você pode usar uma conta que já criou no Azure AD para entrar no Windows 10. Por meio de uma [experiência de FRX (experiência de primeira execução)](azuread-joined-devices-frx.md) ou unindo o Azure AD por meio do [menu de configurações](../user-help/device-management-azuread-joined-devices-setup.md), seus usuários podem unir suas máquinas ao Azure AD.  Agora seus usuários podem aproveitar as vantagens do SSO (logon único) a recursos de nuvem como o Office 365, no navegador ou em aplicativos do Office.

### <a name="scenario-2-educational-institutions"></a>Cenário 2: instituições de ensino
Instituições de ensino normalmente têm dois tipos de usuário: professores e alunos. Os professores são considerados integrantes de longo prazo da organização. É recomendável criar contas locais para eles. Os alunos, contudo, são membros temporários da organização e suas contas podem ser gerenciadas no Azure AD. Isso significa que a escala do diretório pode ser enviada para a nuvem em vez de ser armazenada localmente. Isso também significa que os alunos poderão se conectar no Windows com sua conta do Azure AD e obter acesso aos recursos do Office 365 em aplicativos do Office.

### <a name="scenario-3-retail-businesses"></a>Cenário 3: empresas de varejo
Lojas varejistas geralmente têm funcionários temporários e de longo prazo. Geralmente criamos contas locais e computadores unidos ao domínio para funcionários em tempo integral de longo prazo. Os funcionários temporários, contudo, são membros da organização temporariamente e, portanto, é recomendável gerenciar suas contas de maneira que seja possível mover as licenças de usuário mais facilmente. Ao criar essas contas de usuário na nuvem com licenças do Office 365, elas recebem os benefícios de entrar em aplicativos do Windows e do Office com uma conta do Azure AD, mantendo ainda uma maior flexibilidade das licenças quando eles deixam a empresa.

### <a name="scenario-4-additional-scenarios"></a>Cenário 4: outros cenários
Além dos benefícios discutidos acima, você pode aproveitar as vantagens de ter usuários com dispositivos unidos ao Azure AD devido à experiência simplificada de junção, o gerenciamento eficiente de dispositivos no Azure AD, o registro automático de gerenciamento de dispositivo móvel e o logon único no Azure AD e em recursos locais.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Considerações de implantação para a Junção do Azure AD
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Permitir que seus usuários unam um dispositivo da empresa diretamente ao Azure AD
As empresas podem fornecer contas somente de nuvem para organizações e empresas parceiras. Esses parceiros podem acessar facilmente os aplicativos da empresa e os recursos com o logon único. Esse cenário se aplica a usuários que acessam recursos primariamente na nuvem, tais como o Office 365 ou aplicativos SaaS que contam com o Azure AD para autenticação.

### <a name="prerequisites"></a>Pré-requisitos
**No nível corporativo (administrador)**

* Assinatura do Azure com o Azure Active Directory  

**No nível do usuário**

* Windows 10 (Professional e Enterprise Editions)

### <a name="administrator-tasks"></a>Tarefas do administrador
* [Configure o registro de dispositivos](device-management-azure-portal.md)

### <a name="user-tasks"></a>Tarefas do usuário
* [Configurar um novo dispositivo Windows 10 com o Azure AD durante a instalação](azuread-joined-devices-frx.md)
* [Configurar um dispositivo Windows 10 com o Azure AD no menu de Configurações](../user-help/device-management-azuread-registered-devices-windows10-setup.md)
* [Unir um dispositivo Windows 10 pessoal à sua organização](../user-help/device-management-azuread-joined-devices-setup.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Habilitar o BYOD na sua organização para o Windows 10
Você pode preparar seus funcionários e usuários para usarem seus dispositivos Windows pessoais (BYOD) para acessar aplicativos e recursos da empresa. Os usuários podem adicionar suas contas do Azure AD (contas corporativas ou de estudante) a um dispositivo Windows pessoal para acessar os recursos de maneira segura e compatível.

### <a name="prerequisites"></a>Pré-requisitos
**No nível corporativo (administrador)**

* Assinatura do Azure AD

**No nível do usuário**

* Windows 10 (Professional e Enterprise Editions)

### <a name="administrator-tasks"></a>Tarefas do administrador
* [Configure o registro de dispositivos](device-management-azure-portal.md)

### <a name="user-tasks"></a>Tarefas do usuário
* [Unir um dispositivo Windows 10 pessoal à sua organização](../user-help/device-management-azuread-joined-devices-setup.md)

## <a name="next-steps"></a>Próximas etapas

- [Gerenciamento de dispositivos](overview.md)

