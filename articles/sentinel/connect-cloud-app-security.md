---
title: Coletar dados do Cloud App Security na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados do Cloud App Security em Sentinel do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b0033f5f8636053f88825541b8b2cfcbf2fc9f8b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245481"
---
# <a name="collect-data-from-microsoft-cloud-app-security"></a>Coletar dados do Microsoft Cloud App Security 

> [!IMPORTANT]
> Sentinela do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

É possível transmitir logs do [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) em Azure Sentinel com um único clique. Essa conexão permite que você transmita os alertas do Cloud App Security para Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador de segurança ou de administrador global

## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver o Cloud App Security, verifique se ele está [habilitado na sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se o Cloud App Security é implantado e ingestão de seus dados, os dados de alerta podem facilmente ser transmitidos em Sentinel do Azure.


1. No Azure Sentinel, selecione **coleta de dados** e, em seguida, clique no **Cloud App Security** lado a lado.

2. Clique em **Conectar**.

3. Para usar o esquema relevante no Log Analytics para os alertas do Cloud App Security, pesquise **SecurityAlert**.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
