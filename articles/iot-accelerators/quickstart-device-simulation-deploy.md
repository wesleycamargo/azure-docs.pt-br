---
title: Experimentar e executar uma solução de simulação de dispositivo no Azure | Microsoft Docs
description: Neste início rápido, você implanta um acelerador de solução do Azure IoT para simulação de dispositivo. Você usa o painel da solução para criar uma simulação.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/05/2018
ms.author: dobett
ms.openlocfilehash: 549a1b867ad35c6de42969722ba5a2bd28c8f99a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213149"
---
# <a name="quickstart-deploy-and-run-a-cloud-based-device-simulation-solution"></a>Início Rápido: Implantar e executar uma solução de simulação de dispositivo baseada em nuvem

Este início rápido mostra como implantar o acelerador de solução de simulação de dispositivo do Azure IoT para usar no teste da sua soução IoT. Após ter implantado o acelerador de solução, você usa a página **Simulação** para criar e executar uma simulação.

Para concluir este início rápido, você precisará de uma assinatura do Azure ativa.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="deploy-the-solution"></a>Implantar a solução

Ao implantar o acelerador de solução em sua assinatura do Azure, você precisa definir algumas opções de configuração.

Entre no [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) usando suas credenciais de conta do Azure.

Clique em **Experimentar agora** no bloco **Simulação de dispositivo**.

![Escolha a simulação de dispositivo](./media/quickstart-device-simulation-deploy/devicesimulation.png)

Na página **Criar solução de simulação de dispositivo**, insira um **Nome da solução** exclusivo. Anote o nome da sua solução, este é o nome do grupo de recursos do Azure que contém todos os recursos da solução.

Selecione a **Assinatura** e a **Região** que você deseja usar para o acelerador de solução. Normalmente a região escolhida é a mais próxima de você. Você deve ser um [usuário ou administrador global ](iot-accelerators-permissions.md) na assinatura.

Marque a caixa para implantar um Hub IoT a ser usado com a sua solução de simulação de dispositivo. Você pode sempre alterar o Hub IoT usado pela sua simulação posteriormente.

Clique em **Criar solução** para começar a provisionar a sua solução. Esse processo leva pelo menos cinco minutos para ser executado:

![Detalhes da solução de simulação de dispositivo](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Entrar na solução

Quando o processo de provisionamento estiver concluído, você pode entrar no painel do acelerador de solução de simulação de dispositivo.

Na página **Soluções provisionadas**, clique no novo acelerador de solução de simulação de dispositivo:

![Escolher a nova solução](./media/quickstart-device-simulation-deploy/choosenew.png)

Você pode exibir as informações sobre o seu acelerador de solução de simulação de dispositivo no painel que será exibido. Escolha o **Painel da solução** para exibir o acelerador de solução de simulação de dispositivo:

![Painel de solução](./media/quickstart-device-simulation-deploy/solutionpanel.png)

Clique em **Aceitar** para aceitar a solicitação das permissões, o painel de solução de simulação de dispositivo será exibido no seu navegador:

[![Painel da solução](./media/quickstart-device-simulation-deploy/solutiondashboard-inline.png)](./media/quickstart-device-simulation-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="configure-the-simulation"></a>Configurar a simulação

A simulação é configurada e executada a partir do painel. Use os valores na tabela a seguir para configurar a sua simlação:

| Configuração             | Valor                       |
| ------------------- | --------------------------- |
| Hub IoT de destino      | Use o Hub IoT pré-provisionado |
| Modelo do dispositivo        | Resfriador                     |
| Número de dispositivos   | 10                          |
| Frequência de telemetria | 10 segundos                  |
| Duração da simulação | 5 minutos                   |

[![Configuração da simulação](./media/quickstart-device-simulation-deploy/simulationconfig-inline.png)](./media/quickstart-device-simulation-deploy/simulationconfig-expanded.png#lightbox)

## <a name="run-the-simulation"></a>Executar a simulação

Clique em **Iniciar simulação**. A simulação é executada durante um período escolhido por você. É possível interromper a simulação a qualquer momento clicando em **Interromper simulação**. A simulação mostra as estatísticas para a execução atual. Clique em **Exibir métricas do Hub IoT no portal do Azure** para ver as métricas relatadas pelo Hub IoT:

[![Execução da simulação](./media/quickstart-device-simulation-deploy/simulationrun-inline.png)](./media/quickstart-device-simulation-deploy/simulationrun-expanded.png#lightbox)

Só é possível executar uma simulação de cada vez de uma instância provisionada do acelerador de solução.

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja explorar ainda mais, deixe o acelerador de solução de simulação de dispositivo implantado.

Caso não precise mais do acelerador de solução, exclua-o da página [Soluções provisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard) selecionando-o e clicando em **Excluir solução**:

![Excluir solução](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou o acelerador de solução de simulação de dispositivo e executou uma simulação de dispositivo de IoT.

Para saber como usar um Hub IoT existente em uma simulação, consulte o seguinte guia de instruções:

> [!div class="nextstepaction"]
> [Usar um Hub IoT existente com o acelerador de solução de simulação de dispositivo](iot-accelerators-device-simulation-choose-hub.md)
