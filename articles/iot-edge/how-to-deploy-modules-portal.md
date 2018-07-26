---
title: Implantar módulos do Azure IoT Edge (portal) | Microsoft Docs
description: Usar o portal do Azure para implantar módulos em um dispositivo do IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4082189d451f670c1ae3f76b8ec785d8bd0518b3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034244"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implantar módulos do Azure IoT Edge do portal do Azure

Após criar os módulos do IoT Edge com a lógica de negócios, você pretende implantá-los nos dispositivos para operar na borda. Se houver vários módulos trabalhando em conjunto para coletar e processar dados, será possível implantá-los todos de uma vez e declarar as regras de roteamento que conecta-os. 

Este artigo mostra como o portal do Azure guia você por meio da criação de um manifesto de implantação e de empurrar a implantação a um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação voltada a vários dispositivos com base nas marcações compartilhadas, consulte [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure. 
* Um [Dispositivo do IoT Edge](how-to-register-device-portal.md) com o tempo de execução do IoT Edge instalado. 

## <a name="select-your-device"></a>Selecionar o dispositivo

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
2. Selecionar **IoT Edge** do menu.
3. Clique no ID do dispositivo alvo da lista de dispositivos. 
4. Selecione **Definir Módulos**.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

O portal do Azure tem um assistente que ajuda você por meio da criação de manifesto de implantação, em vez de compilar manualmente o documento JSON. Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**. 

### <a name="add-modules"></a>Adicionar módulos

1. No **as configurações do registro** seção da página, forneça as credenciais para quaisquer registros de contêiner privado que contêm as imagens de módulo. 
2. Na seção **Módulos de Implantação** da página, selecione **Adicionar**. 
3. Selecione o tipo de módulo na lista suspensa: 
   * **Módulo IoT Edge** - a opção padrão.
   * **Módulo Azure Stream Analytics** - apenas módulos gerados da carga de trabalho do Azure Stream Analytics. 

4. Forneça um nome para o módulo, então especifique a imagem do contêiner. Por exemplo:  
   * **Nome** - tempSensor
   * **Imagem URI** - mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
5. Preencha os campos opcionais se necessário. Para mais informações sobre opções de criação de contêiner, políticas de reinício, e status desejados consulte [propriedades desejadas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Para mais informações sobre o módulo gêmeo consulte [Defina ou atualize propriedades desejadas](module-composition.md#define-or-update-desired-properties).
6. Clique em **Salvar**.
7. Repita etapas 2-6 para adicionar módulos adicionais à sua implantação. 
8. Selecione **Avançar** para continuar para a seção de rotas.

### <a name="specify-routes"></a>Especificar Rotas

Por padrão, o assistente fornece uma rota chamada **rota** e definida como **FROM /\* INTO $upstream**, que significa que quaisquer mensagens de saída por quaisquer módulos são enviadas para o seu Hub IoT.  

Adicionar ou atualizar as rotas com informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próximo** para continuar para a seção de revisão.

### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Note que há dois módulos declarados que você não adicionou: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o [tempo de execução IoT Edge](iot-edge-runtime.md) e são padrões requeridos em toda implantação. 

Examine as informações da implantação e, em seguida, selecione **Enviar**. 

## <a name="view-modules-on-your-device"></a>Exibir módulos no dispositivo

Após implantar os módulos no dispositivo, será possível exibir todos eles na seção **Dispositivos do Hub IoT**. Esta página mostra o nome de cada módulo implantado, como também informações úteis como o status de implantação e o código de saída. 

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor.md)
