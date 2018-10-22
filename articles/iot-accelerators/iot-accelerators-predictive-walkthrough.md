---
title: Visão geral do acelerador da solução de Manutenção Preditiva - Azure | Microsoft Docs
description: Uma visão geral do acelerador da solução de Manutenção Preditiva do Azure IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: dobett
ms.openlocfilehash: 822e02d42be8ce516901190af4be579d13ac841d
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888315"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Visão geral do acelerador de solução de Manutenção Preditiva

O acelerador de solução de Manutenção Preditiva é uma solução de ponta a ponta para um cenário comercial e prevê o ponto no qual há a probabilidade de ocorrer uma falha. Você pode usar esse acelerador de solução de forma pró-ativa para atividades como a manutenção de otimização. A solução combina os principais serviços de aceleradores de solução do Azure IoT, como o IoT Hub, Stream Analytics e um workspace do[Azure Machine Learning][lnk-machine-learning]. Esse workspace contém um modelo, com base em um conjunto de dados de exemplo público, para prever a RUL (Vida Útil Restante) de um motor de aeronave. A solução implementa totalmente o cenário de negócios IoT como um ponto de partida para você planejar e implementar uma solução que atenda aos seus requisitos de negócios específicos.

## <a name="logical-architecture"></a>Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos do acelerador de solução:

![][img-architecture]

Os itens azuis são serviços do Azure provisionados na região em que você implantou o acelerador de solução. Exibe a lista de regiões em que você pode implantar o acelerador de solução no [página provisionamento][lnk-azureiotsuite].

O item em verde é um dispositivo simulado que representa um motor de aeronave. Você pode aprender mais sobre esses dispositivos simulados na seção [Dispositivos simulados](#simulated-devices).

Os itens em cinza representam os componentes que implementam os recursos de *gerenciamento do dispositivo*. A versão atual do acelerador de solução de Manutenção Preditiva não provisiona esses recursos. Para saber mais sobre o gerenciamento do dispositivo, confira o [acelerador da solução de monitoramento remoto][lnk-remote-monitoring].

## <a name="azure-resources"></a>Recursos do Azure

No portal do Azure, navegue até o grupo de recursos com o nome da solução escolhido para exibir os recursos provisionados.

![Recursos do acelerador][img-resource-group]

Quando você provisiona o acelerador da solução, recebe um email com um link para o workspace de Machine Learning. Você também pode navegar até o Workspace do Machine Learning na página [Aceleradores de Solução do Microsoft Azure IoT][lnk-azureiotsuite] para sua solução provisionada. Um bloco fica disponível nessa página quando a solução entra no estado **pronto**.

![Modelo de Machine Learning][img-machine-learning]

## <a name="simulated-devices"></a>Dispositivos simulados

No acelerador de solução, um dispositivo simulado representa um motor de aeronave. A solução é provisionada com dois motores que mapeiam uma única aeronave. Cada motor emite quatro tipos de telemetria: o Sensor 9, Sensor 11, Sensor 14 e Sensor 15 fornecem os dados necessários para o modelo de Machine Learning calcular a RUL do motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao Hub IoT:

*Contagem de ciclos*. Um ciclo representa um voo concluído com uma duração entre duas e dez horas. Durante o voo, dados de telemetria são capturados a cada meia hora.

*Telemetria*. Há quatro sensores que representam os atributos do motor. Os sensores são rotulados genericamente de Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Esses quatro sensores representam telemetria suficiente para obter resultados úteis do modelo de regra. O modelo usado no acelerador de solução é criado de um conjunto de dados público que inclui dados de sensor do mecanismo real. Para saber mais sobre como o modelo foi criado no conjunto de dados original, confira o [Modelo de manutenção preditiva da Galeria do Cortana Intelligence][lnk-cortana-analytics].

Os dispositivos simulados podem lidar com os seguintes comandos enviados do hub IoT na solução:

| Comando | DESCRIÇÃO |
| --- | --- |
| StartTelemetry |Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O Hub IoT fornece reconhecimento de comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Trabalho do Stream Analytics do Azure

**Trabalho: Telemetria** funciona no fluxo de entrada da telemetria do dispositivo usando duas instruções:

* O primeiro seleciona toda a telemetria dos dispositivos e envia os dados para o armazenamento de blobs. Aqui, ele é visualizado no aplicativo Web.
* A segunda calcula os valores médios do sensor em uma janela deslizante de dois minutos e envia esses dados por meio do Hub de Eventos para um **processador de eventos**.

## <a name="event-processor"></a>Processador de eventos
O **host do processador de eventos** é executado em um trabalho de Web do Azure. O **processador de eventos** obtém os valores do sensor médios para um ciclo completo. Ele passa esses valores para uma API que expõe o modelo treinado para calcular a RUL para um motor. A API é exposta por um workspace do Machine Learning é provisionado como parte da solução.

## <a name="machine-learning"></a>Machine Learning
O componente de Machine Learning usa um modelo derivado dos dados coletados de mecanismos de aeronave real. Você pode navegar até o workspace do Machine Learning do bloco da sua solução na página [azureiotsuite.com][lnk-azureiotsuite]. O bloco fica disponível quando a solução fica no estado **Pronto**.

O modelo do Azure Machine Learning existente está disponível como modelo para mostrar esses recursos trabalhando desde a telemetria de dispositivo coletada até os serviços de aceleradores da solução de IoT. A Microsoft criou um [modelo de regressão][lnk_regression_model] de um mecanismo de aeronave baseado em dados<sup>\[1\]</sup> disponíveis ao público e diretrizes passo a passo sobre como usar o modelo.

O acelerador da solução de Manutenção Preditiva de IoT do Azure usa o modelo de regressão criado com base neste modelo. O modelo é implantado em sua assinatura do Azure e exposto por meio de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste que representa 4 (do total de 100) 4 (de total de 21) e de mecanismos de fluxos de dados de sensor. Esses dados são suficientes para fornecer um resultado preciso do modelo treinado.

*\[1\] A. Saxena e K. Goebel (2008). “Turbofan Engine Degradation Simulation Data Set” (Conjunto de dados da simulação de degradação do turbofan), Repositório de dados de prognóstico da NASA Ames (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Próximas etapas
Agora você já viu os principais componentes do acelerador de solução de Manutenção Preditiva, convém personalizá-lo.

Você também pode explorar alguns dos outros recursos dos aceleradores de solução de IoT:

* [Perguntas frequentes sobre os aceleradores de solução do IoT][lnk-faq]
* [Segurança IoT desde o início][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
