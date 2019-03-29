---
title: Coletar dados de eventos de segurança do Windows na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados de eventos de segurança do Windows no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 90bff4cd98046a42fa31d98ff00e76d7fc9ac307
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576064"
---
# <a name="connect-windows-security-events"></a>Conectar eventos de segurança do Windows 

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir todos os eventos de segurança dos servidores Windows conectados ao espaço de trabalho do Azure Sentinel. Essa conexão permite que você exibir painéis, criar alertas personalizados e melhorará a investigação. Isso fornece mais informações sobre a rede da sua organização e aprimora sua capacidade de operação de segurança.  Você pode selecionar quais eventos de fluxo:

- **Todos os eventos** -Windows todos os eventos de segurança e AppLocker.
- **Common** -um conjunto padrão de eventos para fins de auditoria.
- **Mínimo** -um pequeno conjunto de eventos que podem indicar possíveis ameaças. Ao habilitar essa opção, você não poderá ter uma trilha de auditoria completa.
- **Nenhum** -nenhuma segurança ou eventos do AppLocker.

> [!NOTE]
> 
> - Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando Sentinel do Azure.

## <a name="set-up-the-windows-security-events-connector"></a>Configurar o conector de eventos de segurança do Windows

Para integrar totalmente os seus eventos de segurança do Windows com o Azure Sentinel:

1. No portal do Azure Sentinel, selecione **coleta de dados** e, em seguida, clique no **eventos de segurança do Windows** lado a lado. 
1. Selecione quais tipos de dados que você deseja transmitir.
1. Clique em **Atualizar**.
6. Para usar o esquema relevante no Log Analytics para os eventos de segurança do Windows, pesquise **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Ele pode levar cerca de 20 minutos até que seus logs comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

