---
title: Começar a usar a solução de simulação de dispositivo – Azure | Microsoft Docs
description: Os aceleradores de solução de IoT são uma ferramenta que pode ser usada para ajudar no desenvolvimento e no teste de uma solução de IoT. O serviço de simulação é uma oferta autônoma que pode ser usada em conjunto com outros aceleradores de solução ou com suas próprias soluções personalizadas.
author: troyhopwood
manager: ''
ms.author: troyhop
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/15/2017
ms.topic: conceptual
ms.openlocfilehash: c427f2640e605533324eb349579c6a40a2a6a47f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627118"
---
# <a name="device-simulation-walkthrough"></a>Instruções passo a passo de simulação de dispositivo

A simulação de dispositivo do Azure IoT é uma ferramenta que pode ser usada para ajudar no desenvolvimento e no teste de uma solução de IoT. A Simulação de dispositivo é uma oferta autônoma que pode ser usada em conjunto com outros aceleradores de solução ou com suas próprias soluções personalizadas.

Este tutorial percorre alguns dos recursos da simulação de dispositivo. Ele mostra como ela funciona e capacita-o para testar suas próprias soluções de IoT.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Configurar uma simulação
> * Iniciando e parando uma simulação
> * Exibir as métricas da telemetria

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisa de uma instância implantada da simulação de dispositivo do Azure IoT em sua assinatura do Azure.

Se você ainda não implantou a simulação de dispositivo, conclua o tutorial [Deploy Azure IoT Device Simulation](iot-accelerators-device-simulation-deploy.md) (Implantar a simulação de dispositivo do Azure IoT).

## <a name="configuring-device-simulation"></a>Configurando a simulação de dispositivo

Você pode configurar e executar a simulação de dispositivo completamente usando o painel. Abra o painel na página [Soluções provisionadas](https://www.azureiotsolutions.com/) dos aceleradores de solução de IoT. Clique em **Iniciar** em sua nova implantação da simulação de dispositivo.

### <a name="target-iot-hub"></a>Hub IoT de destino

Você pode usar a simulação de dispositivo com um Hub IoT pré-provisionado ou com qualquer outro Hub IoT:

![Hub IoT de destino](./media/iot-accelerators-device-simulation-explore/targethub.png)

> [!NOTE]
> A opção de usar um Hub IoT pré-provisionado só estará disponível se você tiver escolhido criar um novo Hub IoT na implantação da simulação de dispositivo. Se você não tiver um Hub IoT, será possível criar um novo por meio do [portal do Azure](https://portal.azure.com).

Para direcionar um Hub IoT específico, forneça a cadeia de conexão **iothubowner**. Você pode obter essa cadeia de conexão no [portal do Azure](https://portal.azure.com):

1. Na página de configuração do Hub IoT no portal do Azure, clique em **Políticas de acesso compartilhado**.
1. Clique em **iothubowner**.
1. Copie a chave primária ou a secundária.
1. Cole esse valor na caixa de texto em um Hub IoT de destino.

![Hub IoT de destino](./media/iot-accelerators-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>Modelo do dispositivo

O modelo de dispositivo permite que você escolha o tipo de dispositivo a ser simulado. Você pode escolher um dos modelos de dispositivo pré-configurados ou definir uma lista de sensores para um modelo de dispositivo personalizado:

![Modelo do dispositivo](./media/iot-accelerators-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>Modelos de dispositivos pré-configurados

A simulação de dispositivo fornece três modelos de dispositivos pré-configurados. Há modelos de dispositivos para resfriadores, elevadores e caminhões disponíveis.

Modelos de dispositivos pré-configurados incluem vários sensores com comportamentos avançados definidos em um arquivo JavaScript. Não há suporte para esses comportamentos personalizados na interface do usuário da web. 

A tabela a seguir mostra uma lista de configurações para cada modelo de dispositivo pré-configurado:

| Modelo do dispositivo | Sensor | Unidade | 
| -------------| ------ | -----| 
| Resfriador | umidade | % |
| | pressure | psig | 
| | temperatura | F | 
| Elevador | Piso | 
| | Vibração | MM | 
| | Temperatura | F | 
| Caminhão | Latitude | |
| | Longitude | | 
| | velocidade | mph | 
| | cargotemperature | F | 

#### <a name="custom-device-model"></a>Modelo de dispositivo personalizado

Os modelos de dispositivos personalizados permitem modelar sensores que representam melhor seus próprios dispositivos. Um dispositivo personalizado pode ter até 10 sensores personalizados.

Ao selecionar o tipo de modelo de dispositivo personalizado, você pode adicionar novos sensores clicando em **+Adicionar sensor**.

![Adicionar sensores](./media/iot-accelerators-device-simulation-explore/customsensors.png)

Os sensores personalizados têm as seguintes propriedades:

| Campo | DESCRIÇÃO |
| ----- | ----------- |
| Nome do sensor | Um nome amigável para o sensor, como **temperatura** ou **velocidade**. |
| Comportamento | Os comportamentos permitem que os dados de telemetria variem de uma mensagem para a próxima para simular dados reais. O **Incremento** aumenta o valor em um para cada mensagem enviada começando no valor mínimo. Depois que o valor máximo é atendido, ele começa novamente no valor mínimo. O **Decremento** comporta-se da mesma maneira que o **Incremento**, mas com contagem regressiva. O comportamento **Aleatório** gera um valor aleatório entre o valor máximo e o valor mínimo. |
| Valor mínimo | O menor número que representa o intervalo aceitável. |
| Valor máximo | O maior número que representa o intervalo aceitável. |
| Unidade | A unidade de medida do sensor como °F ou MPH. |

### <a name="number-of-devices"></a>Número de dispositivos

A simulação de dispositivo permite atualmente que você simule até 20.000 dispositivos.

![Número de dispositivos](./media/iot-accelerators-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Frequência de telemetria

A frequência de telemetria permite que você especifique a frequência na qual os dispositivos simulados devem enviar dados para o Hub IoT. Você pode enviar dados frequentemente, como a cada 10 segundos, ou raramente, como a cada 99 horas, 59 minutos e 59 segundos.

![Frequência de telemetria](./media/iot-accelerators-device-simulation-explore/frequency.png)

> [!NOTE]
> Se estiver usando um Hub IoT que não seja o Hub IoT pré-provisionado, você deverá considerar os limites de mensagem do Hub IoT de destino. Por exemplo, se você tiver 1.000 dispositivos simulados enviando telemetria a cada 10 segundos para um hub S1, o limite de telemetria será atingido para o hub em cerca de uma hora.

### <a name="simulation-duration"></a>Duração da simulação

Você pode optar por executar a simulação durante um período de tempo determinado ou executar até que você a interrompa explicitamente. Ao escolher um período de tempo específico, você pode escolher qualquer duração de 10 minutos até 99 horas, 59 minutos e 59 segundos.

![Duração da simulação](./media/iot-accelerators-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>Iniciar e parar a simulação

Quando você tiver adicionado todos os dados de configuração necessários no formulário, o botão **Iniciar Simulação** será habilitado. Para iniciar a simulação, clique neste botão.

![Iniciar simulação](./media/iot-accelerators-device-simulation-explore/start.png)

Se você tiver especificado uma duração específica para a simulação, ela será interrompida automaticamente quando o tempo for atingido. Também é possível parar a simulação a qualquer momento clicando em **Parar Simulação.**

Se você escolher executar a simulação indefinidamente, ela será executada até que você clique em **Parar Simulação**. Você pode fechar o navegador e voltar para a página de simulação de dispositivo para parar a simulação a qualquer momento.

![Parar a simulação](./media/iot-accelerators-device-simulation-explore/stop.png)

> [!NOTE]
> Apenas uma simulação pode ser executada por vez. Você deve parar a simulação em execução no momento antes de iniciar uma nova simulação.
