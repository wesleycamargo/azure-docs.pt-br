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
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46310375"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Alerta "Os dados do serviço de integridade não estão atualizados"

## <a name="overview"></a>Visão geral
<li>O Azure AD Connect Health gera alerta de dados atualizados quando não recebe todos os pontos de dados do servidor por duas horas. O título do alerta é **Os dados do serviço de integridade não estão atualizados**. </li>
<li>O alerta de status de **Aviso** aciona se o Connect Health não receber elementos de dados parciais enviados pelo servidor por duas horas. O alerta de status de aviso não dispara notificações por email ao administrador do locatário. </li>
<li>O alerta de status de **Erro** aciona se o Connect Health não receber nenhum elemento de dados enviado do servidor por duas horas. O alerta de status de erro dispara notificações por email ao administrador do locatário. </li>

>[!IMPORTANT] 
> Esse alerta segue a [política de retenção de dados](reference-connect-health-user-privacy.md#data-retention-policy) do Connect Health

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas 
* Certifique-se de analisar e atender a [ seção de requisitos](how-to-connect-health-agent-install.md#requirements).
* Use a [ferramenta de teste de conectividade](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para descobrir problemas de conectividade.


## <a name="next-steps"></a>Próximas etapas
* [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md)
