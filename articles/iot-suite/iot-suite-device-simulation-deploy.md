---
title: Implantar a solução de simulação de dispositivo – Azure | Microsoft Docs
description: Este tutorial mostra como provisionar a solução de simulação de dispositivo do azureiotsuite.com.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 52890c51b06777a2d74fcf143bee4e5e8919ba75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Implantar a solução de simulação de dispositivo IoT do Azure

Este tutorial mostra como provisionar uma solução de simulação de dispositivo. Você pode implantar a solução do azureiotsuite.com.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a solução de simulação de dispositivo
> * Implantar a solução de simulação de dispositivo
> * Entrar na solução de simulação de dispositivo

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution"></a>Implantar a solução

Antes de implantar a solução na sua assinatura do Azure, você deve escolher algumas opções de configuração:

1. Entre no [azureiotsuite.com](https://www.azureiotsuite.com) usando as credenciais da sua conta do Azure e clique em **+** para criar uma nova solução:

    ![Criar uma nova solução](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. Clique em **Selecionar** no bloco **Simulação de Dispositivo**:

    ![Escolha a simulação de dispositivo](media/iot-suite-device-simulation-deploy/select.png)

1. Na página **Criar solução de simulação de dispositivo**, insira um **Nome da solução** para sua solução de simulação de dispositivo.

1. Selecione a **Assinatura** e a **Região** que você deseja usar para provisionar a solução.

1. Especifique se deseja que um novo Hub IoT seja implantado com a solução de simulação de dispositivo. Se você marcar esta caixa, um novo Hub IoT será implantado em sua assinatura. Independentemente da escolha, você sempre poderá apontar a simulação para qualquer Hub IoT.

1. Clique em **Criar Solução** para iniciar o processo de provisionamento. Este processo normalmente leva vários minutos para ser executado:

    ![Detalhes da solução de simulação de dispositivo](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>Entrar na solução

Quando o processo de provisionamento for concluído, você poderá entrar na solução de simulação de dispositivo.

1. Na página **Soluções provisionadas**, clique em **Iniciar** na nova solução de simulação de dispositivo:

    ![Escolher a nova solução](media/iot-suite-device-simulation-deploy/newsolution.png)

1. Você pode exibir informações sobre a solução de simulação de dispositivo no painel que aparece. Escolha **Painel da solução** para conectar-se à solução de simulação de dispositivo.

    > [!NOTE]
    > Você poderá excluir sua solução de simulação de dispositivo deste painel ao concluí-la.

    ![Painel de solução](media/iot-suite-device-simulation-deploy/properties.png)

1. O painel da solução de simulação de dispositivo é exibido em seu navegador.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar a solução
> * Implantar a solução
> * Entrar na solução

Agora que você implantou a solução de simulação de dispositivo, a próxima etapa será [explorar os recursos da solução de simulação de dispositivo](./iot-suite-device-simulation-explore.md).

<!-- Next tutorials in the sequence -->
