---
title: incluir arquivo (versão prévia dos fluxos do dispositivo)
description: incluir arquivo (versão prévia dos fluxos do dispositivo)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: d1cfe3d998c08aef2b845315a16d881dea1cd1f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124347"
---
Esta seção descreve como criar um hub IoT usando o [portal do Azure](https://portal.azure.com).

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Escolha +**Criar um recurso**, então escolha **Internet das Coisas**.

3. Clique em **Hub IoT** na lista à direita. Você verá a primeira tela para a criação de um Hub IoT.

   ![Captura de tela mostrando a criação de um hub no portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-1.png)

   Preencha os campos:

   **Assinatura**: selecione a assinatura a ser usada para o hub IoT.

   **Grupo de Recursos**: é possível criar um grupo de recursos ou usar um grupo existente. Para criar um novo, clique em **Criar novo** e preencha o nome que deseja usar. Para usar um grupo de recursos existente, clique em **Usar existente** e selecione o grupo de recursos na lista suspensa. Para obter mais informações, veja [Gerenciar grupos de recursos do Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Região**: é a região na qual você deseja que o hub esteja localizado. Selecione uma região que dê suporte a visualização de fluxos de dispositivos do Hub IoT, seja Centro dos EUA ou EUA Central EUAP.

   **Nome do Hub IoT**: insira o nome do Hub IoT. Esse nome deve ser globalmente exclusivo. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Clique em **Avançar: tamanho e escala** para continuar criando o hub IoT.

   ![Captura de tela mostrando a configuração de tamanho e escala para um novo hub IoT usando o portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-2-free.png)

   Nesta tela, você pode usar os padrões e clicar em **Examinar + Criar** na parte inferior.

   **Tipo e escala de preço**: certifique-se de selecionar um tipo padrão (S1, S2, S3) ou o gratuito (F1). Essa opção também pode ser orientada pelo tamanho da sua frota e das cargas de trabalho não streaming que espera em seu hub (por exemplo, mensagens de telemetria). Por exemplo, o tipo gratuito destina-se a testes e avaliação. Ela permite que 500 dispositivos sejam conectados ao Hub IoT e até 8.000 mensagens por dia. Cada assinatura do Azure pode criar um Hub IoT na camada gratuita. 

   **Unidades do Hub IoT**: essa escolha depende da carga de trabalho não streaming que você espera no hub. Pode selecionar 1 por enquanto.

   Para obter detalhes sobre as outras opções da camada, consulte [Escolher a camada certa do Hub IoT](../articles/iot-hub/iot-hub-scaling.md).

5. Clique em **Revisar + criar** para examinar suas opções. Você verá algo semelhante a esta tela.

   ![Captura de tela examinando as informações sobre como criar o novo hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-3-free.png)

6. Clique em **Criar** para criar seu novo hub IoT. Criar o hub leva alguns minutos.
