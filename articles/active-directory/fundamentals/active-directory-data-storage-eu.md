---
title: Armazenamento de dados de identidade para os clientes europeus – Azure Active Directory | Microsoft Docs
description: Saiba mais sobre onde o Azure Active Directory armazena dados relacionados à identidade para os seus clientes europeus.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93ac5ef5f03f800a8f90259db3e382b3bc5c5e2c
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875634"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para os clientes europeus no Azure Active Directory
Os dados de identidade são armazenados pelo AD do Azure em um local geográfico com base no endereço fornecido pela sua organização ao inscrever-se para um serviço Online da Microsoft, como Office 365 e Azure. Para obter informações sobre onde seus dados de identidade são armazenados, você pode usar o [é onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) seção do Microsoft Trust Center.

Para clientes que recebe um endereço na Europa, o Azure AD mantém a maioria dos dados de identidade em data centers europeus. Este documento fornece informações sobre todos os dados que são armazenados fora da Europa por serviços do Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure a autenticação multifator (MFA)
    
- Autenticação de dois fatores tudo usando chamadas telefônicas ou SMS se originam de datacenters nos EUA e também é roteado por provedores globais.
- Usando o aplicativo se originam nos data centers do Microsoft Authenticator de notificações por push. Além disso, os serviços específicos do fornecedor de dispositivo também podem entram em play e esses serviços talvez fora da Europa.
- Códigos OATH sempre são validados nos EUA. 

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Contêineres de chave e dados de configuração de política do AD B2C do Azure são armazenados em datacenters nos EUA. Eles não contêm quaisquer dados pessoais do usuário. Para obter mais informações sobre as configurações de política, consulte o artigo [Azure Active Directory B2C: políticas internas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Convites de repositórios de B2B do AD do Azure com o resgate vincular e redirecionar as informações de URL em datacenters nos EUA. Além disso, o endereço de email de usuários que cancelar a assinatura de receber convites de B2B também são armazenados em datacenters nos EUA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

O Microsoft Azure AD DS armazena dados de usuário no mesmo que a Rede Virtual do Microsoft Azure selecionada pelo cliente. Portanto, se a rede estiver fora da Europa, os dados são replicados e armazenados fora da Europa.

## <a name="other-considerations"></a>Outras considerações

Serviços e aplicativos que se integram com o Azure AD têm acesso aos dados de identidade. Avalie cada serviço e aplicativo que você usa para determinar como os dados de identidade são processados por esse serviço específico e o aplicativo e se eles atendem aos requisitos de armazenamento de dados da sua empresa.

Para obter mais informações sobre a residência de dados de serviços da Microsoft, consulte o [Onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) seção do Microsoft Trust Center.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre qualquer um dos recursos e funcionalidades descritos acima, consulte esses artigos:
- [O que é a Autenticação Multifator?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Redefinição da senha de autoatendimento do Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [O que é o Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [O que é a colaboração B2B do AD do Azure?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
