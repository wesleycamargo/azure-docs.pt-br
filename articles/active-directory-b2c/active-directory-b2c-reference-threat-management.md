---
title: Gerenciar ameaças a recursos e dados no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre técnicas de detecção e mitigação de ataques de negação de serviço e ataques de senha no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 65c74b7451c5a605ca8c2e866296c87c0320b730
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316894"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerenciar ameaças a recursos e dados no Azure Active Directory B2C

O Microsoft Azure Active Directory (Azure AD) O B2C possui recursos internos que podem ajudá-lo a proteger contra ameaças a seus recursos e dados. Essas ameaças incluem ataques de negação de serviço e ataques de senha. Os ataques de negação de serviço podem tornar os recursos não disponíveis para usuários pretendidos. Os ataques de senha ocasionam o acesso não autorizado aos recursos. 

## <a name="denial-of-service-attacks"></a>Ataques de negação de serviço

Azure Active Directory B2C protege contra ataques de inundação SYN usando um cookie SYN. Azure Active Directory B2C também protege contra ataques de negação de serviço usando limites de taxas e conexões.

## <a name="password-attacks"></a>Ataques de senha

As senhas definidas pelos usuários devem ser de complexidade razoável. O Azure AD B2C possui técnicas de atenuação em vigor para ataques de senha. A mitigação inclui ataques de senhas de força bruta e ataques de senhas de dicionário. Ao usar vários sinais, o Azure AD B2C analisa a integridade das solicitações. O Azure AD B2C foi projetado para diferenciar, de forma inteligente, os usuários pretendidos de hackers e botnets. 

O Azure AD B2C usa uma estratégia sofisticada para bloquear contas. As contas são bloqueadas com base no IP da solicitação e nas senhas inseridas. A duração do bloqueio também aumenta com base na probabilidade de ser um ataque. Depois que uma senha é tentada 10 vezes sem sucesso, ocorre um bloqueio de um minuto. Na próxima vez que um login for malsucedido depois que a conta for desbloqueada, outro bloqueio de um minuto ocorrerá e continuará para cada login malsucedido. Digitar a mesma senha repetidamente não conta como vários logins malsucedidos. 

Os primeiros 10 períodos de bloqueio têm um minuto de duração. Os próximos 10 períodos de bloqueio são ligeiramente mais longos e aumentam em duração após cada 10 períodos de bloqueio. O contador de bloqueio é redefinido para zero após um login bem-sucedido quando a conta não está bloqueada. Períodos de bloqueio podem durar até cinco horas. 

No momento, você não pode:

- Acionar um bloqueio com menos de 10 logins com falha
- Recuperar uma lista de contas bloqueadas
- Configurar a política de bloqueio

Para obter mais informações, consulte o [Centro de Confiabilidade da Microsoft](https://www.microsoft.com/trustcenter/default.aspx).
