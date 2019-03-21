---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: c9daa86bf36b260001d9969385b9e8a98a8ac0cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125360"
---
## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas etapas para conceder acesso a dados para uma entidade de usuário:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente do Time Series Insights. Digite **Time Series** na caixa **pesquisar**. Selecione **Ambiente do Time Series** nos resultados da pesquisa. 

3. Selecione o seu ambiente de Análise de Séries Temporais na lista.

4. Selecione **Políticas de Acesso a Dados** e depois **+Adicionar**.
    ![Gerenciar a fonte do Time Series Insights – ambiente](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. Selecione **Selecionar usuário**.  Pesquise o nome de usuário ou endereço de email para localizar o usuário que deseja adicionar. Clique em **Selecionar** para confirmar a seleção. 

    ![Gerenciar a fonte das Análise de Séries Temporais - adicionar](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. Selecione **Selecionar função**. Escolha a função de acesso apropriada para o usuário:
   - Selecione **Colaborador** se quiser permitir que o usuário altere os dados de referência e compartilhe consultas salvas e perspectivas com outros usuários do ambiente. 
   - Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais (não compartilhadas) no ambiente.

     Selecione **OK** para confirmar a escolha da função.

     ![Gerenciar a fonte das Análise de Séries Temporais - selecionar usuário](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. Selecione **Ok** na página **Selecionar Função do Usuário**.

    ![Gerenciar a fonte das Análise de Séries Temporais - selecionar função](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. A página **Políticas de Acesso de Dados** lista os usuários e suas funções.

    ![Gerenciar a fonte das Análise de Séries Temporais - resultados](media/iot-tsi-data-access/getstarted-grant-data-access5.png)