---
title: Criar fluxos de trabalho com o conector do IoT Central em Aplicativos Lógicos do Azure | Microsoft Docs
description: Use o conector do IoT Central em Aplicativos Lógicos do Azure para disparar fluxos de trabalho e criar, atualizar e excluir dispositivos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 1/3/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 971c01ec8002e73dbc6abfb66a0ede26f90a4d9c
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453203"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Criar fluxos de trabalho com o conector do IoT Central em Aplicativos Lógicos do Azure

*Este tópico aplica-se a construtores e administradores.*

Use Aplicativos Lógicos do Azure para criar fluxos de trabalho automatizados e escalonáveis que integram aplicativos e dados nos serviços de nuvem e sistemas locais. O recurso Aplicativos Lógicos do Azure tem muitos conectores em muitos serviços do Azure, serviços da Microsoft e outros produtos de SaaS (Software como Serviço). Usando o conector de IoT Central em Aplicativos Lógicos do Azure, você pode disparar fluxos de trabalho quando uma regra é acionada na IoT Central. Você também pode usar as ações no conector do IoT Central para criar um dispositivo, atualizar as propriedades e as configurações de um dispositivo ou excluir um dispositivo. 

Você pode usar o conector do IoT Central no Microsoft Flow. Tanto Aplicativos Lógicos do Azure quanto o Microsoft Flow são serviços de integração primeiro do designer, mas têm públicos-alvo um pouco diferentes. O Flow permite que qualquer trabalho possa criar os fluxos de trabalho de negócios de que precisa. O recurso Aplicativos Lógicos capacita os Profissionais de TI a criar as integrações de que eles precisam para conectar dados. Compare Flow e Aplicativos Lógicos [aqui](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Saiba mais sobre como criar fluxos de trabalho com o conector do IoT Central no Microsoft Flow [aqui](howto-add-microsoft-flow.md). 

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo com pagamento conforme o uso
- Uma conta e uma assinatura do Azure para criar e gerenciar Aplicativos Lógicos

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Disparar um fluxo de trabalho quando uma regra é acionada

Esta seção mostra como postar uma mensagem no Microsoft Teams quando uma regra é disparada. Você pode configurar seu fluxo de trabalho para usar outros conectores para realizar ações como enviar um evento para seu hub de eventos, criar um novo item de trabalho do Azure DevOps ou inserir uma nova linha no SQL server.

1. Comece [criando uma regra na IoT Central](howto-create-telemetry-rules.md). Depois de salvar as condições de regra, clique no bloco **Aplicativos Lógicos do Azure** como uma nova ação. Clique em **Criar no portal do Azure**. Você será levado ao portal do Azure para criar um novo aplicativo lógico. Talvez você precise entrar em sua conta do Azure.

1. Insira as informações necessárias para criar um novo aplicativo lógico. Você pode escolher uma assinatura do Azure na qual provisionar seu novo aplicativo lógico. Não precisa ser a mesma assinatura em que seu aplicativo IoT Central foi criado. Clique em **Criar**.

    ![Criar aplicativo lógico no portal do Azure](./media/howto-build-azure-logic-apps/createinazureportal.PNG)

1. Depois que seu aplicativo lógico foi criado com êxito, você será direcionado automaticamente para o Designer de Aplicativos Lógicos. Clique em **Aplicativo Lógico em Branco**. 

    ![Criar um aplicativo lógico em branco](./media/howto-build-azure-logic-apps/blanklogicapp.PNG)

1. No designer, pesquise "iot central" e escolha o gatilho **Quando uma regra for disparada**. Entrar no conector com a conta usada para entrar no aplicativo IoT Central. 

    ![Entrar no conector do IoT Central](./media/howto-build-azure-logic-apps/addtrigger.PNG)

1. Depois de entrar com êxito, você deverá ver campos. Selecione **Aplicativo** e **Regra** nas listas suspensas.

    ![Escolher o aplicativo e a regra](./media/howto-build-azure-logic-apps/pickappandrule.PNG)

1. Adicione uma nova ação. Pesquise **equipes de postar mensagem** e escolha **Postar uma mensagem** do conector do Microsoft Teams. Entre no conector com a conta usada no Microsoft Teams. 

1. Na ação, escolha a **Equipe** e o **Canal**. Preencha o campo **Mensagem** com o que você deseja que cada mensagem diga. Você pode incluir *conteúdo dinâmico* da regra da IoT Central, passando informações importantes como o nome do dispositivo e o carimbo de data/hora para a notificação.
    > [!NOTE]
    > Clique no texto "Veja mais" na janela Conteúdo dinâmico para obter os valores de medida e de propriedade que acionaram a regra.

    ![Ação de edição do aplicativo lógico com o painel dinâmico aberto](./media/howto-build-azure-logic-apps/buildworkflow.PNG)

1. Quando você terminar de editar a ação, clique em **Salvar**.

1. Se você voltar ao seu aplicativo da IoT Central, verá que essa regra tem uma ação de Aplicativos Lógicos do Azure na área Ações.

Você sempre pode começar a criar um fluxo de trabalho usando o conector do IoT Central em Aplicativos Lógicos no Portal do Azure. Em seguida, você pode escolher a qual aplicativo do IoT Central e a qual regra se conectar ao e ainda funcionará da mesma maneira. Não é necessário iniciar da página de regras do IoT Central a cada vez.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Criar, atualizar e excluir um dispositivo em um fluxo de trabalho

Quando você estiver criando um fluxo de trabalho em seu aplicativo lógico, poderá adicionar uma ação usando o conector do IoT Central. Você encontrará **Criar um dispositivo**, **Atualizar um dispositivo** e **Excluir um dispositivo**.
> [!NOTE]
> Para **Atualize um dispositivo** e **Excluir um dispositivo**, você precisará de uma ID de dispositivo existente que deseje atualizar ou excluir. É possível obter a ID do dispositivo da IoT Central na URL do navegador. Isso **NÃO** é igual à ID do Dispositivo listada na página do gerenciador.

![ID do dispositivo do Device Explorer IoT Central](./media/howto-build-azure-logic-apps/iotcdeviceid.PNG)
  

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como usar o Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerenciar dispositivos](howto-manage-devices.md).
