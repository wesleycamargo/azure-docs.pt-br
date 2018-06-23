---
title: Implantar a solução de Monitoramento Remoto – Azure | Microsoft Docs
description: Este tutorial mostra como provisionar o acelerador da solução de Monitoramento Remoto do azureiotsuite.com.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 42f6afcd3cb6880ba6c9cdd2a51e2a3e9ff2c2d4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626846"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Implantar o acelerador da solução de monitoramento remoto

Este tutorial mostra como provisionar o acelerador da solução de Monitoramento Remoto. Você pode implantar a solução do azureiotsuite.com. Você também pode implantar a solução usando a CLI. Para saber mais sobre essa opção, consulte [Implantar um acelerador de solução da linha de comando](iot-accelerators-remote-monitoring-deploy-cli.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o acelerador de solução
> * Implantar o acelerador de solução
> * Entrar no acelerador de solução

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>Implantar o acelerador de solução

Antes de implantar o acelerador de solução em sua assinatura do Azure, escolha algumas opções de configuração:

1. Faça logon no [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) usando suas credenciais de conta do Azure.

1. Clique em **Experimentar Agora** no bloco **Monitoramento Remoto**.

    ![Escolha Monitoramento Remoto](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. Na página **Criar solução de Monitoramento Remoto**, insira um **Nome da solução** para seu acelerador de solução de Monitoramento Remoto.

1. Selecione uma implantação do tipo **Básico** ou **Standard**. Se você estiver implantando a solução para saber como ela funciona ou para executar uma demonstração, escolha a opção **Básico** para minimizar os custos.

1. Escolha **Java** ou **.NET** como a linguagem. Todos os microservices estão disponíveis como implementações Java ou .NET.

1. Examine o painel **Detalhes da solução** para obter mais informações sobre suas opções de configuração.

1. Selecione a **Assinatura** e a **Região** que você deseja usar para provisionar a solução.

1. Clique em **Criar Solução** para iniciar o processo de provisionamento. Este processo normalmente leva vários minutos para ser executado:

    ![Detalhes da solução de monitoramento remoto](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

Para obter informações de solução de problemas, consulte [What to do when a deployment fails](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) (O que fazer quando uma implantação falhar) no repositório do GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Entrar no acelerador de solução

Após a conclusão do processo de provisionamento, você poderá se conectar a seu acelerador da solução de Monitoramento Remoto.

1. Na página **Soluções provisionadas**, escolha sua nova solução de Monitoramento Remoto:

    ![Escolher a nova solução](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. Você pode exibir informações sobre a solução de Monitoramento Remoto no painel exibido. Escolha **Painel da solução** para se conectar à solução de Monitoramento Remoto.

    > [!NOTE]
    > Você poderá excluir sua solução de Monitoramento Remoto usando este painel quando não precisar mais dela.

    ![Painel de solução](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. O painel da solução de Monitoramento Remoto será exibido em seu navegador.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar o acelerador de solução
> * Implantar o acelerador de solução
> * Entrar no acelerador de solução

Agora que você implantou a solução de Monitoramento Remoto, a próxima etapa será [explorar os recursos do painel da solução](iot-accelerators-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->