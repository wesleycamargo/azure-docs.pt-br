---
title: Pesquisa de Relatórios do Active Directory do Azure
description: Como pesquisar relatórios de auditoria, atividade e segurança do Active Directory do Azure
services: active-directory
documentationcenter: ''
author: dhanyahk
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk

---
# Pesquisa de Relatórios do Active Directory do Azure
*Esta documentação é parte do [Guia de Relatórios do Active Directory do Azure](active-directory-reporting-guide.md).*

O Azure AD (Azure Active Directory) oferece aos administradores de diretório a capacidade de pesquisar eventos de auditoria, atividade e segurança do usuário em vários relatórios.

Para localizar o painel de pesquisa, navegue até o **portal clássico do Azure -> seu diretório do Azure AD > Relatórios.** O painel pode ser encontrado na parte superior da lista de relatórios.

Para pesquisar eventos de auditoria ou atividade para um determinado usuário, selecione um intervalo de datas nos campos De e Para, digite o nome de exibição ou o UPN do usuário e selecione o botão da marca de seleção (OK).

## Relatórios incluídos na pesquisa
Ainda nem todos os relatórios estão incluídos nos resultados da pesquisa. Esta tabela indica quais relatórios estão incluídos.

| Relatório | Incluso |
| --- | --- |
| Entradas de fontes desconhecidas |Não |
| Entradas após várias falhas |Não |
| Entradas de várias geografias |Não |
| Entradas de endereços IP com atividade suspeita |Não |
| Entradas de dispositivos possivelmente infectados |Não |
| Atividades de entrada irregulares |Não |
| Usuários com atividade de entrada anômala |Não |
| Usuários com credenciais vazadas |Não |
| Relatório de auditoria |Sim |
| Atividade de redefinição de senha |Sim |
| Atividade de registro de redefinição de senha |Sim |
| Atividade de grupos de autoatendimento |Sim |
| Uso do aplicativo |Não |
| Atividade de provisionamento de conta |Sim |
| Status de substituição de senha |Não |
| Erros de provisionamento de conta |Não |
| Uso do RMS |Não |
| Usuários RMS mais ativos |Não |
| Uso de dispositivo do RMS |Não |

## Saiba mais
* [Relatórios do Active Directory do Azure](active-directory-view-access-usage-reports.md)
* [Eventos de Auditoria de Relatórios do Active Directory do Azure](active-directory-reporting-audit-events.md)

<!---HONumber=AcomDC_0928_2016-->