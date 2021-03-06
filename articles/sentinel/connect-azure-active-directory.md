---
title: Coletar dados do Azure AD na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados do Azure Active Directory no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: b42fb982abb916503d530d5040641ba1dcce3c92
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575123"
---
# <a name="collect-data-from-azure-active-directory"></a>Coletar dados do Azure Active Directory

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinela do Azure permite que você colete dados do [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmita-a para o Azure Sentinel. Você pode escolher fluxo [logs de entrada](../active-directory/reports-monitoring/concept-sign-ins.md) e [logs de auditoria](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Pré-requisitos

- Se você quiser exportar dados de entrada do Active Directory, você deve ter uma licença do Azure AD P1 ou P2.

- Usuário com permissões globais de administrador de segurança ou de administrador no locatário que você deseja transmitir os logs de.


## <a name="connect-to-azure-ad"></a>Conecte-se ao AD do Azure

1. No Azure Sentinel, selecione **coleta de dados** e, em seguida, clique no **Azure Active Directory** lado a lado.

2. Ao lado dos logs que você deseja transmitir para o Azure Sentinel, clique em **Connect**.

6. Para usar o esquema relevante no Log Analytics para os alertas do Azure AD, pesquise **SigninLogs** e **AuditLogs**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure AD ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
