---
title: "Implantar a solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial mostra como provisionar a solução de monitoramento remoto pré-configurada do azureiotsuite.com."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 08/09/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 159397ba4fc93628acbf2bb53edf5eb88fd0cac7
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>Implantar a solução de monitoramento remoto pré-configurada

Este tutorial mostra como provisionar a solução pré-configurada de monitoramento remoto. Você pode implantar a solução do azureiotsuite.com. Você também pode implantar a solução usando a CLI, para saber mais sobre essa opção, consulte [Deploy a preconfigured solution from the command line](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line) (Implantar uma solução pré-configurada da linha de comando).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a solução pré-configurada
> * Implantar a solução pré-configurada
> * Entrar na solução pré-configurada

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-preconfigured-solution"></a>Implantar a solução pré-configurada

Antes de implantar a solução pré-configurada em sua assinatura do Azure, você deve escolher algumas opções de configuração:

1. Faça logon no [azureiotsuite.com](https://www.azureiotsuite.com) usando as credenciais da sua conta do Azure e clique em **+** para criar uma nova solução:

    ![Criar uma nova solução](media/iot-suite-remote-monitoring-deploy/createnewsolution.png)

1. Clique em **Selecionar** no bloco **Visualização do monitoramento remoto**.

    ![Escolher o monitoramento remoto](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. Na página **Criar solução de monitoramento remoto**, insira um **Nome da solução** para sua solução de monitoramento remoto pré-configurada.

1. Selecione uma implantação do tipo **Básico** ou **Standard**. Se você estiver implantando a solução para saber como ela funciona ou para executar uma demonstração, escolha a opção **Básico** para minimizar os custos.

1. Escolha **Java** ou **.NET** como a linguagem. Todos os microservices estão disponíveis como implementações Java ou .NET.

1. Examine o painel **Detalhes da solução** para obter mais informações sobre suas opções de configuração.

1. Selecione a **Assinatura** e a **Região** que você deseja usar para provisionar a solução.

1. Clique em **Criar Solução** para iniciar o processo de provisionamento. Este processo normalmente leva vários minutos para ser executado:

    ![Detalhes da solução de monitoramento remoto](media/iot-suite-remote-monitoring-deploy/createform.png)

Para obter informações de solução de problemas, consulte [What to do when a deployment fails](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) (O que fazer quando uma implantação falhar) no repositório do GitHub.

## <a name="sign-in-to-the-preconfigured-solution"></a>Entrar na solução pré-configurada

Quando o processo de provisionamento for concluído, você poderá entrar em sua solução de monitoramento remoto pré-configurada.

1. Na página **Soluções provisionadas**, escolha sua nova solução de monitoramento remoto:

    ![Escolher a nova solução](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. Você pode exibir informações sobre sua solução de monitoramento remoto no painel que é exibido. Escolha **Painel da solução** para conectar-se à solução de monitoramento remoto.

    > [!NOTE]
    > Você poderá excluir sua solução de monitoramento remoto usando este painel quando não precisar mais dela.

    ![Painel de solução](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. O painel da solução de monitoramento remoto será exibido no seu navegador.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar a solução pré-configurada
> * Implantar a solução pré-configurada
> * Entrar na solução pré-configurada

Agora que você implantou a solução de monitoramento remoto, a próxima etapa será [explorar os recursos do painel da solução](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->