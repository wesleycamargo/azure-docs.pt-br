---
title: Monitoramento remoto de IoT e notificações com os Aplicativos Lógicos do Azure | Microsoft Docs
description: Use os Aplicativos Lógicos do Azure para monitoramento de temperatura de IoT em seu hub IoT e envio automático de notificações por email à sua caixa de correio sobre quaisquer anomalias detectadas.
author: robinsh
keywords: monitoramento de iot, notificações de iot, monitoramento de temperatura de iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125989"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoramento remoto IoT e notificações com os Aplicativos Lógicos do Azure conectando o hub IoT e a caixa de correio

![Diagrama de ponta a ponta](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/) pode ajudá-lo a orquestrar os fluxos de trabalho no local e serviços de nuvem, um ou mais empresas e entre vários protocolos. Um aplicativo lógico começa com um gatilho, que é seguido por uma ou mais ações que podem ser sequenciadas usando controles internos, como condições e iteradores. Essa flexibilidade torna os aplicativos lógicos uma solução de IoT ideal para cenários de monitoramento de IoT. Por exemplo, a chegada de dados de telemetria de um dispositivo em um ponto de extremidade do IoT Hub pode iniciar fluxos de trabalho de aplicativo de lógica para o depósito de dados em um blob de armazenamento do Azure, enviar alertas por email para avisar de anomalias de dados, agendar a visita de um técnico, se um dispositivo relata uma falha , e assim por diante.

## <a name="what-you-learn"></a>O que você aprenderá

Aprenda a criar um aplicativo lógico que conecta o hub IoT e a caixa de correio para monitoramento e notificações de temperatura.

O código do cliente em execução no seu dispositivo define uma propriedade de aplicativo, `temperatureAlert`, na telemetria de cada mensagem ele envia ao hub IoT. Quando o código do cliente detecta uma temperatura acima de 30 C, define essa propriedade como `true`; caso contrário, ele define a propriedade como `false`.

Neste tópico, você configura o roteamento no hub IoT para enviar mensagens em que `temperatureAlert = true` a um barramento de serviço de ponto de extremidade e você configurar um aplicativo lógico que dispara as mensagens que chegam ao ponto de extremidade do barramento de serviço e envia uma notificação por email.

## <a name="what-you-do"></a>O que fazer

* Criar um namespace do barramento de serviço e adicione uma fila do barramento de serviço a ele.
* Adicione um ponto de extremidade personalizado e uma regra de roteamento ao hub IoT para rotear mensagens que contêm um alerta de temperatura para a fila do barramento de serviço.
* Criar, configurar e testar um aplicativo lógico para consumir mensagens da fila do barramento de serviço e enviar emails de notificação para um destinatário desejado.

## <a name="what-you-need"></a>O que você precisa

* Conclua o [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial ou um dos tutoriais dispositivo; por exemplo, [Raspberry Pi com Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes abrangem os seguintes requisitos:

  * Uma assinatura ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * Um aplicativo cliente em execução no seu dispositivo que envia mensagens de telemetria ao hub IoT do Azure.

## <a name="create-service-bus-namespace-and-queue"></a>Criar fila e o namespace do barramento de serviço

Criar um namespace do Barramento de Serviço e da fila. Neste tópico, você pode criar uma regra de roteamento no hub IoT para mensagens diretas que contêm um alerta de temperatura para a fila do barramento de serviço, onde serão captados por um aplicativo lógico e dispará-lo para enviar um email de notificação.

### <a name="create-a-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

1. Sobre o [portal do Azure](https://portal.azure.com/), selecione **+ criar um recurso** > **integração** > **barramento de serviço**.

1. Sobre o **criar namespace** painel, forneça as seguintes informações:

   **Nome**: O nome do namespace do barramento de serviço. O namespace deve ser exclusivo no Azure.

   **Tipo de preço**: Selecione **básica** na lista suspensa. A camada básica é suficiente para este tutorial.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local que usa o hub IoT.

   ![Criar um namespace de barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecione **Criar**. Aguarde a conclusão antes de passar para a próxima etapa da implantação.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Adicionar uma fila do barramento de serviço ao namespace

1. Abra o namespace do barramento de serviço. A maneira mais fácil de obter o namespace do barramento de serviço é selecionar **grupos de recursos** no painel de recursos, selecione o grupo de recursos e selecione o namespace do barramento de serviço da lista de recursos.

1. Sobre o **Namespace de barramento de serviço** painel, selecione **+ fila**.

1. Insira um nome para a fila e, em seguida, selecione **criar**. Quando a fila foi criada com êxito, o **criar fila** painel será fechado.

   ![Adicionar uma fila do barramento de serviço ao portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Volta a **Namespace de barramento de serviço** painel, em **entidades**, selecione **filas**. Abrir a fila do barramento de serviço na lista e, em seguida, selecione **políticas de acesso compartilhado** > **+ adicionar**.

1. Insira um nome para a política, marque **Manage**e, em seguida, selecione **criar**.

   ![Adicionar uma política de fila do barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Adicionar um ponto de extremidade personalizado e a regra de roteamento ao hub IoT

Adicione um ponto de extremidade personalizado para a fila do barramento de serviço ao seu hub IoT e crie uma regra de roteamento de mensagem para direcionar as mensagens que contêm um alerta de temperatura ao ponto de extremidade, onde eles serão captados pelo seu aplicativo lógico. A regra de roteamento usa uma consulta de roteamento `temperatureAlert = "true"`, para encaminhar mensagens com base no valor da `temperatureAlert` definida pelo código do cliente em execução no dispositivo de propriedade do aplicativo. Para obter mais informações, consulte [consulta de roteamento com base nas propriedades da mensagem da mensagem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Adicionar um ponto de extremidade personalizado

1. Abrir seu Hub IoT. A maneira mais fácil introdução ao hub IoT é selecionar **grupos de recursos** no painel de recursos, selecione o grupo de recursos e selecione seu hub IoT na lista de recursos.

1. Sob **Messaging**, selecione **roteamento de mensagens**. Sobre o **roteamento de mensagens** painel, selecione o **pontos de extremidade personalizados** e, em seguida, selecione **+ adicionar**. Na lista suspensa, selecione **fila do barramento de serviço**.

   ![Adicionar um ponto de extremidade ao hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Sobre o **adicionar um ponto de extremidade do barramento de serviço** painel, insira as seguintes informações:

   **Nome do ponto de extremidade**: O nome do ponto de extremidade.

   **Namespace do barramento de serviço**: Selecione o namespace que você criou.

   **Fila do barramento de serviço**: Selecione a fila que você criou.

   ![Adicionar um ponto de extremidade ao hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecione **Criar**. Depois que o ponto de extremidade é criado com êxito, vá para a próxima etapa.

### <a name="add-a-routing-rule"></a>Adicionar uma regra de roteamento

1. Volta a **roteamento de mensagens** painel, selecione o **rotas** e, em seguida, selecione **+ adicionar**.

1. Sobre o **adicionar uma rota** painel, insira as seguintes informações:

   **Nome**: O nome da regra de roteamento.

   **Ponto de extremidade**: Selecione o ponto de extremidade que você criou.

   **Fonte de dados**: Selecione **mensagens de telemetria do dispositivo**.

   **Consulta de roteamento**: Digite `temperatureAlert = "true"`.

   ![Adicionar uma regra de roteamento no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Clique em **Salvar**. Você pode fechar o **roteamento de mensagens** painel.

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar um aplicativo lógico

Na seção anterior, você configurar seu hub IoT para rotear mensagens contendo um alerta de temperatura à sua fila do barramento de serviço. Agora, você configurar um aplicativo lógico para monitorar a fila do barramento de serviço e enviar uma notificação por email sempre que uma mensagem for adicionada à fila.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico

1. Selecione **criar um recurso** > **integração** > **aplicativo lógico**.

1. Insira as seguintes informações:

   **Nome**: O nome do aplicativo lógico.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local que usa o hub IoT.

   ![Criar um aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecione **Criar**.

### <a name="configure-the-logic-app-trigger"></a>Configurar o gatilho do aplicativo lógico

1. Abra o aplicativo lógico. A maneira mais fácil de obter para o aplicativo lógico é selecionar **grupos de recursos** no painel de recursos, selecione o grupo de recursos e selecione seu aplicativo lógico na lista de recursos. Quando você seleciona o aplicativo lógico, o Designer de aplicativos lógicos é aberto.

1. No Designer de aplicativos lógicos, role para baixo até **modelos** e selecione **aplicativo lógico em branco**.

   ![Iniciar com um aplicativo lógico em branco no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecione o **todos os** e, em seguida, selecione **do barramento de serviço**.

   ![Selecionar o barramento de serviço para começar a criar o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Sob **disparadores**, selecione **quando uma ou mais mensagens chegam em uma fila (conclusão automática)**.

   ![Selecione o gatilho para seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Crie uma conexão do barramento de serviço.
   1. Insira um nome de conexão e selecione seu namespace do barramento de serviço na lista. Abre a próxima tela.

      ![Criar uma conexão do barramento de serviço para o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecione a política de barramento de serviço (RootManageSharedAccessKey). Em seguida, selecione **criar**.

      ![Criar uma conexão do barramento de serviço para o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na tela final, para **nome da fila**, selecione a fila que você criou na lista suspensa. Insira `175` para **contagem máxima de mensagens**.

      ![Especificar a contagem máxima de mensagens para a conexão do barramento de serviço no aplicativo lógico](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selecione **salvar** no menu na parte superior do Designer de aplicativos lógicos para salvar suas alterações.

### <a name="configure-the-logic-app-action"></a>Configurar a ação de aplicativo lógico

1. Crie uma conexão de serviço SMTP.

   1. Selecione **Nova etapa**. Na **escolher uma ação**, selecione o **todas as** guia.

   1. Tipo de `smtp` na caixa de pesquisa, selecione o **SMTP** no resultado da pesquisa de serviço e, em seguida, selecione **Enviar Email**.

      ![Criar uma conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Insira as informações de SMTP para sua caixa de correio e, em seguida, selecione **criar**.

      ![Inserir informações de conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenha as informações de SMTP para [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Você talvez precise desabilitar o SSL para estabelecer a conexão. Se esse for o caso e você deseja habilitar o SSL novamente depois que a conexão foi estabelecida, consulte a etapa opcional no final desta seção.

   1. Dos **adicionar novo parâmetro** lista suspensa na **Enviar Email** etapa, selecione **do**, **para**, **assunto**e **corpo**. Clique ou toque em qualquer lugar na tela para fechar a caixa de seleção.

      ![Escolha os campos de email de conexão SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Insira seu endereço de email para **De** e **Para**, e `High temperature detected` para **Assunto** e **Corpo**. Se o **adicionar conteúdo dinâmico a partir de aplicativos e conectores usados neste fluxo** caixa de diálogo é aberta, selecione **ocultar** para fechá-la. Você não usar o conteúdo dinâmico neste tutorial.

      ![Campos de email de conexão de SMTP de preenchimento](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selecione **salvar** para salvar a conexão SMTP.

1. (Opcional) Se você precisasse desabilitar o SSL para estabelecer uma conexão com seu provedor de email e deseja habilitá-la novamente, siga estas etapas:

   1. Sobre o **aplicativo lógico** painel, em **ferramentas de desenvolvimento**, selecione **conexões de API**.

   1. Na lista de conexões de API, selecione a conexão SMTP.

   1. Sobre o **smtp Conexão de API** painel, em **geral**, selecione **conexão de API editar**.

   1. Sobre o **Editar Conexão de API** painel, selecione **habilitar SSL?**, insira novamente a senha para sua conta de email e selecione **salvar**.

      ![Editar conexão de API do SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Seu aplicativo lógico agora está pronto para processar alertas de temperatura da fila do barramento de serviço e enviar notificações para sua conta de email.

## <a name="test-the-logic-app"></a>Testar o aplicativo lógico

1. Inicie o aplicativo cliente em seu dispositivo.

1. Se você estiver usando um dispositivo físico, traga cuidadosamente uma fonte de calor perto do sensor de calor até que a temperatura excede 30 ° c Se você estiver usando o simulador online, o código do cliente aleatoriamente produzirá mensagens de telemetria que excedem c 30.

1. Você deve começar a receber notificações de email enviadas pelo aplicativo lógico.

   > [!NOTE]
   > O provedor de serviços de email talvez precise verificar a identidade do remetente para garantir que é você que envia o email.

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito um aplicativo lógico que conecta o hub IoT e sua caixa de correio para monitoramento e notificações de temperatura.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
