---
title: Coletar dados do Azure ATP na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados do Azure ATP no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: 154af3988084792331db082b99cae0ae06126f1b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242166"
---
# <a name="collect-data-from-azure-advanced-threat-protection-atp"></a>Coletar dados do Azure Advanced Threat ATP (proteção)

> [!IMPORTANT]
> Sentinela do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


É possível transmitir logs do [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) em Azure Sentinel com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador de segurança ou de administrador global
- Você deve ser um cliente de visualização privada do Azure ATP

## <a name="connect-to-azure-atp"></a>Conectar-se para o Azure ATP

Verifique se é a versão de visualização privada do Azure ATP [habilitado na sua rede](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se o Azure ATP é implantado e ingestão de seus dados, os alertas suspeitos podem facilmente ser transmitidos em Sentinel do Azure. Pode levar até 24 horas para os alertas iniciar o streaming em Sentinel do Azure.



1. No Azure Sentinel, selecione **coleta de dados** e, em seguida, clique no **do Azure ATP** lado a lado.

2. Clique em **Conectar**.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure Advanced Threat Protection para Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

