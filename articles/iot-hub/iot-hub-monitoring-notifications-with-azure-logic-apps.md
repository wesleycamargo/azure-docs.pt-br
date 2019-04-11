---
title: Monitoramento remoto de IoT e notificações com os Aplicativos Lógicos do Azure | Microsoft Docs
description: Use os Aplicativos Lógicos do Azure para monitoramento de temperatura de IoT em seu hub IoT e envio automático de notificações por email à sua caixa de correio sobre quaisquer anomalias detectadas.
author: robinsh
keywords: monitoramento de iot, notificações de iot, monitoramento de temperatura de iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 5d5b1d1579600767153fcf5ad751e1224631d611
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262509"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoramento remoto IoT e notificações com os Aplicativos Lógicos do Azure conectando o hub IoT e a caixa de correio

![Diagrama de ponta a ponta](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Os Aplicativos Lógicos do Azure fornecem uma maneira de automatizar processos como uma série de etapas. Um aplicativo lógico pode se conectar com vários serviços e protocolos. Ele começa com um gatilho como “Quando uma conta é adicionada”, seguido por uma combinação de ações semelhantes a “enviar uma notificação por push”. Esse recurso torna os aplicativos lógicos uma solução de IoT perfeita para monitoramento de IoT, como alertas a anomalias, entre outros cenários de uso.

## <a name="what-you-learn"></a>O que você aprenderá

Aprenda a criar um aplicativo lógico que conecta o hub IoT e a caixa de correio para monitoramento e notificações de temperatura. Quando a temperatura está acima de 30 C, o aplicativo cliente marca `temperatureAlert = "true"` na mensagem enviada para o hub IoT. A mensagem dispara o aplicativo lógico para enviar uma notificação por email a você.

## <a name="what-you-do"></a>O que fazer

* Crie um namespace de barramento de serviço e adicione uma fila a ele.
* Adicione um ponto de extremidade e uma regra de roteamento ao hub IoT.
* Crie, configure e teste um aplicativo lógico.

## <a name="what-you-need"></a>O que você precisa

* Tutorial [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluído que aborda os seguintes requisitos:

  * Uma assinatura ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * O aplicativo cliente que envia mensagens para o hub IoT do Azure.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Criar um namespace de barramento de serviço e adicionar uma fila a ele

### <a name="create-a-service-bus-namespace"></a>Criar um namespace de barramento de serviço

1. Sobre o [portal do Azure](https://portal.azure.com/), selecione **criar um recurso** > **Enterprise Integration** > **barramento de serviço**.

2. Forneça as seguintes informações:

   **Nome**: O nome do barramento de serviço.

   **Tipo de preço**: Selecione **básicas** > **selecione**. A camada básica é suficiente para este tutorial.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local que usa o hub IoT.

3. Selecione **Criar**.

   ![Criar um namespace de barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Adicionar uma fila do barramento de serviço

1. Abra o namespace do barramento de serviço e, em seguida, selecione **+ fila**.

1. Insira um nome para a fila e, em seguida, selecione **criar**.

1. Abra a fila do barramento de serviço e, em seguida, selecione **políticas de acesso compartilhado** > **+ adicionar**.

1. Insira um nome para a política, marque **Manage**e, em seguida, selecione **criar**.

   ![Adicionar uma fila do barramento de serviço ao portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-query-to-your-iot-hub"></a>Adicionar um ponto de extremidade e uma consulta de roteamento ao hub IoT

Agora, adicione um ponto de extremidade e uma consulta de roteamento ao hub Iot.

### <a name="add-an-endpoint"></a>Adicionar um ponto de extremidade

1. Abra o hub IoT, selecione **pontos de extremidade** > **+ adicionar**.

1. Insira as seguintes informações:

   **Nome**: O nome do ponto de extremidade.

   **Tipo de ponto de extremidade**: Selecione **Fila do Barramento de Serviço**.

   **Namespace do barramento de serviço**: Selecione o namespace que você criou.

   **Fila do Barramento de Serviço**: Selecione a fila que você criou.

3. Selecione **OK**.

   ![Adicionar um ponto de extremidade ao hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Adicionar uma regra de roteamento

1. No seu hub IoT, selecione **rotas** > **+ adicionar**.

2. Insira as seguintes informações:

   **Nome**: O nome da regra de roteamento.

   **Fonte de dados**: Selecione **DeviceMessages**.

   **Ponto de extremidade**: Selecione o ponto de extremidade que você criou.

   **Cadeia de caracteres de consulta**: Digite `temperatureAlert = "true"`.

3. Clique em **Salvar**.

   ![Adicionar uma regra de roteamento no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar um aplicativo lógico

Em seguida, você pode cria e configurar um aplicativo lógico.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico

1. No [portal do Azure](https://portal.azure.com/), selecione **criar um recurso** > **Enterprise Integration** > **aplicativo lógico**.

2. Insira as seguintes informações:

   **Nome**: O nome do aplicativo lógico.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local que usa o hub IoT.

3. Selecione **Criar**.

### <a name="configure-the-logic-app"></a>Configurar o aplicativo lógico

1. Abra o aplicativo lógico que é aberto no Designer de aplicativos lógicos.

2. No Designer de aplicativos lógicos, selecione **aplicativo lógico em branco**.

   ![Iniciar com um aplicativo lógico em branco no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

3. Selecione **Barramento de Serviço**.

   ![Selecionar o barramento de serviço para começar a criar o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

4. Selecione **barramento de serviço – quando uma ou mais mensagens chegam em uma fila (conclusão automática)**.

5. Crie uma conexão do barramento de serviço.

   1. Insira um nome de conexão.

   2. Selecione o namespace do barramento de serviço > a política de barramento de serviço > **criar**.

      ![Criar uma conexão do barramento de serviço para o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   3. Selecione **continuar** depois que a conexão do barramento de serviço é criado.

   4. Selecione a fila que você criou e insira `175` para **contagem máxima de mensagens**.

      ![Especificar a contagem máxima de mensagens para a conexão do barramento de serviço no aplicativo lógico](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   5. Selecione o "botão Salvar" para salvar as alterações.

6. Crie uma conexão de serviço SMTP.

   1. Selecione **Nova etapa** > **Adicionar uma ação**.

   2. Tipo de `SMTP`, selecione o **SMTP** no resultado da pesquisa de serviço e, em seguida, selecione **SMTP – Enviar Email**.

      ![Criar uma conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   3. Insira as informações de SMTP da caixa de correio e, em seguida, selecione **criar**.

      ![Inserir informações de conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenha as informações de SMTP para [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

   4. Insira seu endereço de email para **De** e **Para**, e `High temperature detected` para **Assunto** e **Corpo**.

   5. Clique em **Salvar**.

O aplicativo lógico está em funcionamento quando você o salva.

## <a name="test-the-logic-app"></a>Testar o aplicativo lógico

1. Inicie o aplicativo cliente que você implantar no dispositivo em [Conectar ESP8266 ao Hub IoT do Azure](iot-hub-arduino-huzzah-esp8266-get-started.md).

2. Aumente a temperatura ambiente em torno de SensorTag acima 30 C. Por exemplo, acenda uma vela em torno do SensorTag.

3. Você deve receber uma notificação por email enviada pelo aplicativo lógico.

   > [!NOTE]
   > O provedor de serviços de email talvez precise verificar a identidade do remetente para garantir que é você que envia o email.

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito um aplicativo lógico que conecta o hub IoT e sua caixa de correio para monitoramento e notificações de temperatura.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
