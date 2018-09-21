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
ms.openlocfilehash: fb45ea02f365cf4e7b394e249f9b91a784e5469f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369786"
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