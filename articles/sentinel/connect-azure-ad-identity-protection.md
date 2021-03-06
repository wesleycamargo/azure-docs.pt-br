---
title: Coleta de dados do Azure AD Identity Protection na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados do Azure AD Identity Protection em Sentinel do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 609aced38b7e30f78d81934867196c568dcc85ca
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57239991"
---
# <a name="collect-data-from-azure-ad-identity-protection"></a>Coletar dados do Azure AD Identity Protection

> [!IMPORTANT]
> Sentinela do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

É possível transmitir logs do [do Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) em Azure Sentinel aos alertas de fluxo no Azure Sentinel exibir painéis, criar alertas personalizados e melhorará a investigação. Azure Active Directory Identity Protection fornece uma exibição consolidada de usuários em risco, eventos de risco e vulnerabilidades, com a capacidade de remediar o risco imediatamente e definir políticas para remediar automaticamente eventos futuros. O serviço foi criado na experiência da Microsoft protegendo as identidades do consumidor e ganha grande exatidão de sinal de mais de 13 bilhões logons por dia. 


## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter um [licença do Azure Active Directory Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Usuário com permissões de administrador de segurança ou de administrador global


## <a name="connect-to-azure-ad-identity-protection"></a>Conectar-se ao Azure AD Identity Protection

Se você já tiver o Azure AD Identity Protection, verifique se ele está [habilitado na sua rede](../active-directory/identity-protection/enable.md).
Se o Azure AD Identity Protection é implantado e recebendo dados, os dados de alerta podem facilmente ser transmitidos em Sentinel do Azure.


1. No Azure Sentinel, selecione **coleta de dados** e, em seguida, clique no **Azure AD Identity Protection** lado a lado.

2. Clique em **Connect** para iniciar o fluxo de eventos do Azure AD Identity Protection em Sentinel do Azure.


6. Para usar o esquema relevante no Log Analytics para os alertas do Azure AD Identity Protection, pesquise **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure AD Identity Protection para Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
