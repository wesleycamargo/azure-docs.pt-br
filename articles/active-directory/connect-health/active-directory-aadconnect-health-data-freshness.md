---
title: Azure AD Connect Health - Alerta "Os dados do serviço de integridade não estão atualizados" | Microsoft Docs
description: Este documento descreve a causa do alerta "Os dados do serviço de integridade não estão atualizados" e como solucioná-lo.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
ms.locfileid: "29715004"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Alerta "Os dados do serviço de integridade não estão atualizados"

## <a name="overview"></a>Visão geral
<li>O Azure AD Connect Health gera alerta de dados atualizados quando não recebe todos os pontos de dados do servidor por duas horas. O título do alerta é **Os dados do serviço de integridade não estão atualizados**. </li>
<li>O alerta de status de **Aviso** aciona se o Connect Health não receber elementos de dados parciais enviados pelo servidor por duas horas. O alerta de status de aviso não dispara notificações por email ao administrador do locatário. </li>
<li>O alerta de status de **Erro** aciona se o Connect Health não receber nenhum elemento de dados enviado do servidor por duas horas. O alerta de status de erro dispara notificações por email ao administrador do locatário. </li>

>[!IMPORTANT] 
> Esse alerta segue a [política de retenção de dados](active-directory-aadconnect-health-gdpr.md#data-retention-policy) do Connect Health

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas 
* Certifique-se de analisar e atender a [ seção de requisitos](active-directory-aadconnect-health-agent-install.md#requirements).
* Use a [ferramenta de teste de conectividade](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para descobrir problemas de conectividade.


## <a name="next-steps"></a>Próximas etapas
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
