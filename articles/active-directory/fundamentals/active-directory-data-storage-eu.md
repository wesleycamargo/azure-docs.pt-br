---
title: Armazenamento de dados de identidade para os clientes europeus – Azure Active Directory | Microsoft Docs
description: Saiba mais sobre onde o Azure Active Directory armazena dados relacionados à identidade para os seus clientes europeus.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/17/2018
ms.custom: it-pro, seodec18
ms.openlocfilehash: 51658fe686e2826618b2044cd969e6b4d463b93f
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063159"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para os clientes europeus no Azure Active Directory
O Azure AD (Azure Active Directory) ajuda você a gerenciar identidades do usuário e a criar políticas de acesso inteligentes que ajudam a proteger os recursos da organização. Os dados de identidade são armazenados em um local que tem base no endereço de sua organização fornecida quando você se inscreveu no serviço. Por exemplo, quando você se inscreveu no Office 365 ou no Microsoft Azure. Para obter informações específicas sobre onde os dados de identidade estão armazenados, você pode usar a seção [Onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) do Microsoft Trust Center.

Enquanto os dados de identidade europeus relacionados ao Microsoft Azure Active Directory permanecerem em data centers europeus, há cinco atributos relacionados ao usuário que normalmente são armazenados em data centers dos EUA. Esses atributos são GivenName, Surname, userPrincipalName, Domain, e PasswordHash. O atributo PasswordHash pode ser uma exceção e não armazenado nos EUA se alguém usa um local, método de autenticação federada que interrompe o valor PasswordHash da sincronização com o Microsoft Azure Active Directory. Além disso, há alguns dados operacionais, específicos do serviço que é necessário para a operação normal do Microsoft Azure Active Directory que é armazenada nos EUA e não inclui todos os dados pessoais.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Dados armazenados fora de data centers europeus para clientes europeus

A maioria dos dados de identidade europeus relacionados ao Microsoft Azure Active Directory para organizações com os endereços europeus, permanecente nos datacenters europeus. Os dados do Microsoft Azure Active Directory que são armazenados em bancos de dados europeus e também são replicados para datacenters dos Estados Unidos, incluem:

- **Identificar atributos relacionados**

    Os seguintes atributos de identidade serão replicados para os Estados Unidos:

    - GivenName
    - Sobrenome
    - userPrincipalName
    - Domínio
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **Autenticação de multifator (MFA) do Microsoft Azure e redefinição de senha de autoatendimento (SSPR) do Microsoft Azure Active Directory** 
    
    O MFA armazena todos os dados de usuário em repouso em data centers europeus. No entanto, alguns dados de serviço específicos do MFA são armazenados nos EUA, incluindo:
    
    - Autenticação de dois fatores e seus dados pessoais relacionados podem ser armazenados nos EUA se você estiver usando o MFA ou SSPR.
        - Todas as autenticações de dois fatores usando chamadas telefônicas ou SMS podem ser concluídas por operadoras dos EUA.
        - Notificações por push usando o aplicativo Microsoft Authenticator exigem notificações do serviço de notificação do fabricante (Apple ou Google), que pode estar fora da Europa.
        - Códigos OATH sempre são validados nos EUA. 
    - Alguns logs SSPR e MFA são armazenados nos EUA por 30 dias, independentemente do tipo de autenticação.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    O Azure AD B2C armazena todos os dados de usuário em repouso em data centers europeus. No entanto, os logs operacionais (com dados pessoais removidos) permanecem no local de onde a pessoa que está acessando os serviços. Por exemplo, se um usuário B2C acessar o serviço nos Estados Unidos, os logs operacionais permanecem nos EUA. Além disso, todos os dados de configuração de política não contêm dados pessoais e são armazenados somente nos EUA. Para obter mais informações sobre as configurações de política, consulte o artigo [Azure Active Directory B2C: políticas internas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    O Microsoft Azure Active Directory B2B armazena todos os dados de usuário em repouso em data centers europeus. No entanto, o B2B armazena seus metadados não pessoais nas tabelas de datacenters dos EUA. Essa tabela inclui campos como redeemUrl, invitationTicket, Id de locatário de recursos, InviteRedirectUrl e InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    O Microsoft Azure AD DS armazena dados de usuário no mesmo que a Rede Virtual do Microsoft Azure selecionada pelo cliente. Portanto, se a rede estiver fora da Europa, os dados são replicados e armazenados fora da Europa.

- **Serviços e aplicativos integrados ao Microsoft Azure Active Directory**

    Quaisquer serviços e aplicativos que se integram com o Azure Active Directory têm acesso aos dados de identidade. Avalie cada serviço e aplicativo para determinar como os dados de identidade são processados por esse aplicativo e serviço específico e se elas atendem aos requisitos de armazenamento de dados da sua empresa.

    Para obter mais informações sobre a residência de dados de serviços da Microsoft, consulte o [Onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) seção do Microsoft Trust Center.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre qualquer um dos recursos e funcionalidades descritos acima, consulte esses artigos:
- [O que é a Autenticação Multifator?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
- [Redefinição da senha de autoatendimento do Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)
- [O que é o Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [O que é a colaboração B2B do AD do Azure?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
