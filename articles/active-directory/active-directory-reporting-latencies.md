---
title: Latências do relatório do Active Directory do Azure | Microsoft Docs
description: Quantidade de tempo que leva para relatar eventos aparecendo no seu Active Directory do Azure
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
# Latências de relatório do Active Directory do Azure
*Esta documentação é parte do [Guia de Relatórios do Active Directory do Azure](active-directory-reporting-guide.md).*

| Relatório | Mínimo | Média | Máximo |
| --- | --- | --- | --- |
| **Relatórios de segurança** | | | |
| Atividades de entrada irregulares |2 horas |4 horas |8 horas |
| Entradas de fontes desconhecidas |2 horas |4 horas |8 horas |
| Entradas após várias falhas |2 horas |4 horas |8 horas |
| Entradas de várias geografias |2 horas |4 horas |8 horas |
| Entradas de endereços IP com atividade suspeita |2 horas |4 horas |8 horas |
| Entradas de dispositivos possivelmente infectados |2 horas |4 horas |8 horas |
| Usuários com atividade de entrada anômala |2 horas |4 horas |8 horas |
| Usuários com credenciais vazadas |2 horas |4 horas |8 horas |
| **Relatórios de aplicativo** | | | |
| Atividade de provisionamento de conta |2 horas |4 horas |8 horas |
| Erros de provisionamento de conta |2 horas |4 horas |8 horas |
| Uso do aplicativo |2 horas |4 horas |8 horas |
| Status de substituição de senha |2 horas |4 horas |8 horas |
| **Auditoria e relatórios de atividade** | | | |
| Relatório de auditoria |1 minuto |15 minutos |30 minutos |
| Atividade de redefinição de senha (Azure AD) |2 horas |4 horas |8 horas |
| Atividade de redefinição de senha (Identity Manager) |2 horas |4 horas |8 horas |
| Atividade de registro de redefinição de senha (Azure AD) |2 horas |4 horas |8 horas |
| Atividade de registro de redefinição de senha (Identity Manager) |2 horas |4 horas |8 horas |
| Atividade de grupos de autoatendimento (Azure AD) |2 horas |4 horas |8 horas |
| Atividade de grupos de autoatendimento (Identity Manager) |2 horas |4 horas |8 horas |
| **Relatórios RMS** | | | |
| Usuários RMS mais ativos |2 horas |4 horas |8 horas |
| Uso do RMS |2 horas |4 horas |8 horas |
| Uso de dispositivo do RMS |2 horas |4 horas |8 horas |
| Uso de aplicativos habilitados para RMS |2 horas |4 horas |8 horas |
| **Relatórios de visualização particular** | | | |
| Toda atividade de entrada do usuário |2 horas |4 horas |8 horas |

<!---HONumber=AcomDC_0928_2016-->