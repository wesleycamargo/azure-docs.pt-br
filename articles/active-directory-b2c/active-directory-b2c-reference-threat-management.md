---
title: "Gerenciamento de ameaças para Azure B2C | Microsoft Docs"
description: "Técnicas de mitigação e de detecção de ataques DOS e de ataques de senha no Azure B2C."
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C: gerenciamento de ameaças
O Gerenciamento de ameaças inclui proteção contra ataques contra o sistema e redes. A DOS (negação de serviço) pode afetar a disponibilidade e torna o recurso indisponível para os usuários pretendidos. Os ataques de senha ocasionam o acesso não autorizado aos recursos. O Microsoft Azure Active Directory B2C tem recursos internos para proteger seus dados contra essas ameaças de várias maneiras. 

## <a name="denial-of-service-attack"></a>Ataque de negação de serviço

O Azure AD B2C usa técnicas de detecção e de mitigação como cookies SYN, limites de taxa e de conexão para proteger os recursos subjacentes contra esses ataques.  

## <a name="password-attacks"></a>Ataques de senha

O Azure AD B2C também tem mitigações em vigor para ataques de senha.  Essa técnica inclui ataques de força bruta de senhas e ataques de dicionário de senhas.  As senhas definidas pelos usuários devem ser de complexidade razoável.  O Azure AD B2C analisa a integridade das solicitações para diferenciar com inteligência usuários pretendidos de hackers e botnets usando vários sinais. O B2C oferece uma estratégia sofisticada para bloquear contas com base nas senhas digitadas, na probabilidade de um ataque.

[More information on Microsoft's Threat Management (Mais informações sobre o Gerenciamento de ameaças da Microsoft)](https://www.microsoft.com/trustcenter/security/threatmanagement)
