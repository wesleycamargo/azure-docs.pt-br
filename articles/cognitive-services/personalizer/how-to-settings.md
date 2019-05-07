---
title: Configurar definições
titleSuffix: Azure Cognitive Services
description: A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 0cd08e1191c68c57975d3e68648134925155e7f2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025594"
---
# <a name="personalizer-settings"></a>Configurações do Personalizador

A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.

Crie um recurso do Personalizador para cada loop de comentários. 

## <a name="configure-service-settings-in-the-azure-portal"></a>Definir as configurações do serviço no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Encontre o recurso do Personalizador. 
1. Na seção **Gerenciamento de recursos**, selecione **Configurações**.

    ![Entre no Portal do Azure. Encontre o recurso do Personalizador. Na seção Gerenciamento de recursos, selecione Configurações.](media/settings/how-to-configure.png)

### <a name="reward-settings-for-the-feedback-loop"></a>Configurações de recompensa para o loop de comentários

Defina as configurações do serviço para uso do loop de comentários de recompensas. As alterações nas seguintes configurações redefinirão o modelo atual do Personalizador e o treinarão novamente com os dois últimos dias de dados:

![Definir as configurações de recompensa para o loop de comentários](media/settings/configure-model-reward-settings.png)

|Configuração|Finalidade|
|--|--|
|Tempo de espera de recompensa|Define o período de tempo durante o qual Personalizador coletará valores de recompensa para uma chamada de Classificação, a partir do momento em que ocorre a chamada de Classificação. Esse valor é definido pelas seguintes perguntas: "Por quanto tempo o Personalizador deve aguardar as chamadas de recompensas?" Qualquer recompensa recebida após essa janela será registrada em log, mas não será usada para o aprendizado.|
|Recompensa padrão|Se nenhuma chamada de recompensa for recebida pelo Personalizador durante a janela de Tempo de Espera de Recompensa associada a uma Chamada de classificação, o Personalizador atribuirá a Recompensa Padrão. Por padrão e na maioria dos cenários, a Recompensa Padrão é zero.|
|Agregação de recompensas|Se várias recompensas forem recebidas para a mesma chamada à API de Classificação, este método de agregação será usado: **soma** ou **mais anterior**. A opção "mais anterior" escolhe a pontuação mais anterior recebida e descarta o restante. Isso é útil se você deseja obter uma recompensa exclusiva entre chamadas possivelmente duplicadas. |

Depois de alterar essas configurações, selecione **Salvar**.

### <a name="exploration-setting"></a>Configuração de exploração 

A personalização pode descobrir novos padrões e se adaptar às alterações no comportamento do usuário ao longo do tempo explorando alternativas. A configuração **Exploração** determina qual percentual de chamadas de Classificação são respondidas com a exploração. 

As alterações nessa configuração redefinirão o modelo atual do Personalizador e o treinarão novamente com os dois últimos dias de dados.

![A configuração de exploração determina qual percentual de chamadas de Classificação são respondidas com a exploração](media/settings/configure-exploration-setting.png)

Depois de alterar essa configuração, selecione **Salvar**.

### <a name="model-update-frequency"></a>Frequência de atualização do modelo

A **frequência de atualização do modelo** define a frequência com que um novo modelo do Personalizador é treinado novamente. 

![A frequência de atualização do modelo define a frequência com que um novo modelo do Personalizador é treinado novamente.](media/settings/configure-model-update-frequency-settings.png)

Depois de alterar essa configuração, selecione **Salvar**.

### <a name="data-retention"></a>Retenção de dados

O **Período de retenção de dados** define por quantos dias o Personalizador mantém os logs de dados. Os logs de dados passados são necessários para realizar [avaliações offline](concepts-offline-evaluation.md), que são usados para medir a eficácia do Personalizador e otimizar a Política de Aprendizado.

Depois de alterar essa configuração, selecione **Salvar**.

## <a name="export-the-personalizer-model"></a>Exportar o modelo do Personalizador

Na seção do Gerenciamento de recursos de **Modelo e Política**, examine a criação do modelo e a data da última atualização e exporte o modelo atual.

![Exportar o modelo atual do Personalizador](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importar e exportar a política de aprendizado

Na seção do Gerenciamento de recursos de **Modelo e Política**, importe uma nova política de aprendizado ou exporte a política de aprendizado atual.

## <a name="next-steps"></a>Próximas etapas

[Aprendizado de reforço](concepts-reinforcement-learning.md) 
