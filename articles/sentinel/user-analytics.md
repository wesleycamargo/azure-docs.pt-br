---
title: Investigar os usuários com a análise de usuário na visualização do Azure Sentinel | Microsoft Docs
description: Saiba mais sobre como investigar os usuários com a análise de usuário no Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ee871f90838b32be3c950ab23a0f01568c309269
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246314"
---
# <a name="investigate-users-with-user-analytics"></a>Investigar os usuários com a análise de usuário

> [!IMPORTANT]
> Sentinela do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os usuários são entidades que você deseja monitorar de perto. Para ajudá-lo a obter informações sobre os usuários, Sentinel do Azure integra-se perfeitamente com o Azure Advanced Threat Protection. Essa integração permite analisar o comportamento do usuário e a priorizar quais usuários você deve investigar primeiro, com base em seus alertas e padrões de atividade suspeita em Sentinel do Azure e o Microsoft 365.

Azure Sentinela enriquece os dados de usuário com a análise do Microsoft 365 para habilitar a análise de análise e o risco do usuário abrangente para todos os usuários no Azure Active Directory. 

## <a name="how-it-works"></a>Como ele funciona

1. Com base na sua segurança de regras de análises, quando uma correspondência for detectada, o Azure Sentinel envia alertas para o Azure ATP.
1. O Azure ATP verifica quais entidades de usuário estão relacionadas aos alertas e calcula a prioridade de investigação para esses usuários.
3. O Azure ATP, em seguida, recalcula a pontuação dos usuários depois que ele é aprimorado com os dados de suas regras de análise para o Azure Sentinel.


## <a name="prerequisites"></a>Pré-requisitos

- Uma licença do Azure Advanced Threat Protection 
- Para habilitar o recurso, você precisa de permissões de administrador global no locatário no qual você instalou o Azure Sentinel

> [!NOTE]
> - Para cada locatário do Azure ATP, você pode se conectar a uma instância do Azure Sentinel.
> - Análise do usuário está atualmente disponível apenas para usuários do Active Directory do Azure. 

## <a name="enable-user-analytics"></a>Habilitar a análise de usuário

1. Para habilitar a análise de usuário no Azure Sentinel, em que o portal, vá até o **análise de usuário** da página e clique em **habilitar**. Isso envia informações sobre o espaço de trabalho para o Azure ATP.

1. Em seguida, ele leva você para o Azure ATP. Sob **extensões de segurança** na **Sentinel do Microsoft Azure** , clique o **+ plus** para adicionar e, em seguida, selecione o espaço de trabalho. 
1. Clique em **Conectar**.

## <a name="investigate-a-user"></a>Investigar um usuário

1. No menu em Azure Sentinel **análise do usuário**, examine a lista de usuários de acordo com a prioridade de investigação. A pontuação baseia-se no Azure Sentinel alertas e alertas do Microsoft 365.

2. Pesquisar por um usuário que você deseja investigar. Clique no usuário para chegar à página do usuário. Examine o usuário atividades e alertas, ao longo do tempo, na linha do tempo. Você pode ver todas as atividades do Microsoft 365 e Azure Sentinel. Você também pode acessar a página de usuário, analisando os usuários de dentro de um caso.
      
    ![Usuários](./media/user-analytics/user-investigation.png)

 
3. Para que isso funcione bem, você precisa configurar corretamente os [regra de alerta personalizada](tutorial-detect-threats.md) para mapear os identificadores de usuário correto para o **conta** entidade.

    - Para eventos do Windows, mapeie **conta** para o **SID**
    - Mapear os dados do AD do Azure (que podem ser encontrados em muitos logs, incluindo a atividade do Azure) ou dados do Office 365, o **conta** propriedade para o **GUID**, ou o **Nome Principal do usuário**. 

   ![Consultar](./media/user-analytics/query-window.png)



Por exemplo:  
> [!NOTE]
> Nos logs de atividade de atividade do Azure, a entidade do chamador inclui o UPN.

1. Essa consulta pesquisa para a criação de um número anormal de recursos ou atividades de implantação no Log de atividades do Azure.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. Na regra de alerta personalizada, mapear os **conta** propriedade **chamador**.
   
   ![Consultar](./media/user-analytics/query-window.png)

3. Investigue o usuário na janela de investigação de usuário. Para obter orientações sobre como investigar os usuários, consulte [Tutorial: Investigar um usuário](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar-se o seu provedor de inteligência contra ameaças para Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os artigos a seguir.

- Para começar a Sentinela do Azure, você precisa de uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [integrar seus dados para Azure Sentinel](quickstart-onboard.md), e [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
