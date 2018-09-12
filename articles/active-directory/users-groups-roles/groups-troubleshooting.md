---
title: Solução de problemas de associação dinâmica para grupos| Microsoft Docs
description: Solução de problemas para a associação dinâmica para grupos no Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/01/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 82a5c57ce874e77a7912945a6fca07acee339197
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444480"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Solucionando problemas de associações dinâmicas a grupos

**Configurei uma regra em um grupo, mas nenhuma associação foi atualizada no grupo**<br/>Verifique os valores dos atributos de usuário na regra: há usuários que atendem à regra? Se tudo estiver correto, aguarde alguns instantes para que o grupo seja populado. Dependendo do tamanho do seu locatário, o grupo pode levar até 24 horas para ser populado pela primeira vez ou depois de uma alteração de regra.

**Configurei uma regra, mas agora os membros da regra existentes foram removidos**<br/>Este comportamento é esperado. Membros existentes do grupo são removidos quando uma regra é habilitada ou alterada. Os usuários retornados da avaliação da regra são adicionados como membros ao grupo.

**Não vejo as alterações da associação instantaneamente quando adiciono ou altero uma regra, por que não?**<br/>A avaliação de associação dedicada é feita periodicamente em um processo assíncrono em segundo plano. O tempo que o processo leva é determinado pelo número de usuários no diretório e pelo tamanho do grupo criado como resultado da regra. Normalmente, os diretórios com um pequeno número de usuários verão as alterações de associação de grupo em poucos minutos. Os diretórios com um grande número de usuários podem levar até 30 minutos ou mais para serem populados.

**Eu encontrei uma erro de processamento de regra**<br/>A seguinte tabela relacionará os erros de regra de associação e como corrigi-los.

| Erro do analisador de regra | Erro de uso | Uso corrigido |
| --- | --- | --- |
| Erro: O atributo não tem suportado. |(user.invalidProperty -eq "Valor") |(user.department -eq "value") A propriedade <br/><br/>Verifique se o atributo está na [lista de propriedades com suporte](groups-dynamic-membership.md#supported-properties). |
| Erro: Operador não é tem suportada no atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true) A propriedade <br/><br/>Não há suporte para o operador usado para o tipo de propriedade (neste exemplo, -contains não pode ser usado no tipo booliano). Use os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consulta. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. Operador ausente. Use -and ou -or para unir predicados<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Erro na expressão regular usada com -match<br>(user.userPrincipalName -match "*@domain.ext")<br>ou alternativamente: (user.userPrincipalName -match "@domain.ext") |

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](../fundamentals/active-directory-manage-groups.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](../active-directory-apps-index.md)
* [O que é o Active Directory do Azure?](../fundamentals/active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](../connect/active-directory-aadconnect.md)
