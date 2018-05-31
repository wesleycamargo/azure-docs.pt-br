---
title: Criar e gerenciar regras de eventos no aplicativo Azure IoT Central | Microsoft Docs
description: As regras de eventos do Azure IoT Central permitem monitorar os dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email, quando a regra é disparada.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 932b1906b767ee7676f46ffd7242ad3d478d41c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201584"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Criar uma regra de Eventos e configurar notificações no aplicativo Azure IoT Central

É possível usar o Microsoft Azure IoT Central para monitorar remotamente os dispositivos conectados. As regras do Azure IoT Central permitem monitorar os dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email, quando a regra é disparada. Com apenas alguns cliques, você pode definir a condição para monitorar os dados do dispositivo e configurar a ação a ser invocada. Este artigo explica a regra de monitoramento de eventos em detalhes.

O Azure IoT Central usa a [medida de evento](howto-set-up-template.md) para capturar dados do dispositivo. Cada tipo de medida possui atributos de chave que definem a medida. É possível criar regras para monitorar cada tipo de medida de dispositivo e gerar alertas quando a regra for disparada. Uma regra de eventos é disparada quando o evento de dispositivo selecionado é relatado pelo dispositivo.

## <a name="create-an-event-rule"></a>Criar uma regra de eventos

Esta seção mostra como criar uma regra de eventos. Este exemplo usa um dispositivo de máquina de venda refrigerada que relata um evento de erro do motor da ventoinha. A regra monitora o evento relatado pelo dispositivo e enviará um email sempre que o evento for relatado.

1. Navegue até a página de detalhes do dispositivo para o dispositivo ao qual você está adicionando a regra.

1. Se você ainda não criou regras, a tela a seguir será exibida:

    ![Não há regras](media\howto-create-event-rules\image1.png)

1. Na guia **Regras**, escolha **+ Nova Regra** para ver os tipos de regras que você pode criar.

    ![Tipos de regras](media\howto-create-event-rules\image2.png)

1. Clique em **Evento** para abrir o formulário para criar a regra.

    ![Regra de eventos](media\howto-create-event-rules\image3.png)

1. Escolha um nome que ajude-o a identificar a regra neste modelo de dispositivo.

1. Para habilitar imediatamente a regra para todos os dispositivos criados a partir desse modelo, alterne **Habilitar regra**.

### <a name="configure-the-rule-condition"></a>Configurar a condição de regra

Esta seção mostra como adicionar uma condição para monitorar a medida de evento de erro do motor da ventoinha.

1. Escolha **+** próximo à **Condição**.

1. Escolha a medida de evento na lista suspensa que você deseja monitorar. Neste exemplo, foi selecionado o evento **Erro de Motor da Ventoinha**.

1. Opcionalmente, também é possível fornecer um valor se você quiser monitorar um valor específico do evento que está sendo relatado pelo dispositivo. Por exemplo, se o dispositivo relatar o mesmo evento com códigos de erro diferentes, fornecer o código de erro como um valor na condição da regra garantirá que a regra seja disparada somente quando o dispositivo enviar esse valor específico como carga do evento. Deixar em branco significa que a regra será disparada sempre que o dispositivo enviar o evento, independentemente do valor do evento.

    ![Adicionar a condição de evento](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > É necessário selecionar pelo menos uma medida de evento ao definir uma condição de regra de eventos.

### <a name="configure-the-action"></a>Configurar a ação

Esta seção mostra como especificar o que a regra faz quando a condição corresponde, adicionando uma ação.

1. Escolha **+** próximo a **Ações**. Aqui, você vê a lista de ações disponíveis. Durante a visualização pública, o **Email** é a única ação com suporte.

    ![Adicionar Ação](media\howto-create-event-rules\image5.png)

1. Escolha a ação **Email**, insira um endereço de email válido no campo **Para** e forneça uma observação para aparecer no corpo do email quando a regra for disparada.

    > [!NOTE]
    > Os emails serão enviados apenas aos usuários que foram adicionados ao aplicativo e fizeram logon pelo menos uma vez. Saiba mais sobre o [gerenciamento de usuários](howto-administer.md) no Azure IoT Central.

   ![Configurar Ação](media\howto-create-event-rules\image6.png)

1. Para salvar a regra, escolha **Salvar**. A regra entra em ação em alguns minutos e começa a monitorar os eventos enviados ao aplicativo. Quando a condição especificada na regra corresponde, a regra dispara a ação de email configurada.

## <a name="parameterize-the-rule"></a>Parametrizar a regra

As ações também podem ser configuradas usando a **Propriedade de Dispositivo** como um parâmetro. Se um endereço de email for armazenado como uma propriedade de dispositivo, ele poderá ser usado quando você definir o endereço **Para**.

## <a name="delete-a-rule"></a>Excluir uma regra

Se você não precisar mais de uma regra, exclua-a, abrindo a regra e escolhendo **Excluir**. Excluir a regra remove-a do modelo de dispositivo e de todos os dispositivos associados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Habilitar ou desabilitar uma regra para um modelo de dispositivo

Navegue até o dispositivo e escolha a regra que você deseja habilitar ou desabilitar. Alternar o botão **Habilitar regra para todos os dispositivos deste modelo** na regra, habilita ou desabilita a regra para todos os dispositivos associados ao modelo de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Navegue até o dispositivo e escolha a regra que você deseja habilitar ou desabilitar. Alterne o botão **Habilitar regra para este dispositivo** para habilitar ou desabilitar a regra para esse dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar regras no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Como gerenciar os dispositivos](howto-manage-devices.md).