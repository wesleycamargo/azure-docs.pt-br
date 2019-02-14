---
title: Criar fluxos de trabalho com o conector da IoT Central do Azure no Microsoft Flow | Microsoft Docs
description: Use o conector do IoT Central no Microsoft Flow para acionar fluxos de trabalho e criar, atualizar e excluir dispositivos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 76b1940ea1724c7eee8d2784c0e2c2328dd4c8bf
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771838"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Criar fluxos de trabalho com o conector da IoT Central no Microsoft Flow

*Este tópico aplica-se a construtores e administradores.*

Use o Microsoft Flow para automatizar fluxos de trabalho entre os vários aplicativos e serviços dos quais os usuários empresariais dependem. Usando o conector de IoT Central no Microsoft Flow, você pode disparar fluxos de trabalho quando uma regra é acionada na IoT Central. Em um fluxo de trabalho acionado pela IoT Central ou qualquer outro aplicativo, você pode usar as ações no conector da IoT Central para criar um dispositivo, atualizar as propriedades e configurações de um dispositivo ou excluir um dispositivo. Confira [estes modelos do Microsoft Flow](https://aka.ms/iotcentralflowtemplates) que conectam a IoT Central a outros serviços, tais como notificações móveis e o Microsoft Teams.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo com pagamento conforme o uso
- Uma conta pessoal da Microsoft ou uma conta corporativa ou de estudante para entrar no Flow ([saiba mais sobre os planos do Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Disparar um fluxo de trabalho quando uma regra é acionada

Esta seção mostra como disparar uma notificação móvel no aplicativo móvel do Flow quando uma regra é acionada na IoT Central.

1. Comece [criando uma regra na IoT Central](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Depois de salvar as condições da regra, clique na **ação do Microsoft Flow** como uma nova ação. Uma nova janela ou guia deve se abrir no navegador, conduzindo você ao Microsoft Flow.

    ![Criar uma nova ação do Microsoft Flow](media/howto-add-microsoft-flow-experimental/createflowaction.png)

1. Entre no Microsoft Flow. Essa não precisa ser a mesma conta que você usa na IoT Central. Você será levado para uma página de visão geral mostrando um conector da IoT Central conectando-se a uma ação personalizada.

1. Entre no conector da IoT Central e clique em **Continuar**. Você será direcionado para o designer do Microsoft Flow para criar seu fluxo de trabalho. O fluxo de trabalho tem um gatilho da IoT Central que contém seu aplicativo e a regra já preenchidos.

1. Escolha **+ Nova Etapa**, **Adicionar uma ação**. Neste ponto, você pode adicionar qualquer ação que deseja a seu fluxo de trabalho. Por exemplo, enviaremos uma notificação móvel. Pesquise por **notificação** e escolha **Notificações – enviar-me uma notificação móvel**.

1. Na ação, preencha o campo de texto com a mensagem que você deseja que seja transmitida pela notificação. Você pode incluir *conteúdo dinâmico* da regra da IoT Central, passando informações importantes como o nome do dispositivo e o carimbo de data/hora para a notificação.

    > [!NOTE]
    > Clique no texto "Veja mais" na janela Conteúdo dinâmico para obter os valores de medida e de propriedade que acionaram a regra.

    ![Flow editando a ação com o painel dinâmico aberto](./media/howto-add-microsoft-flow-experimental/flowdynamicpane.png)

1. Quando você terminar de editar a ação, clique em **Salvar**. Você será direcionado à página de visão geral do fluxo de trabalho. Aqui você pode ver o histórico de execução e compartilhá-lo com outros colegas.

    > [!NOTE]
    > Se desejar que outros usuários em seu aplicativo de Central da IoT editem essa regra, você precisará compartilhá-la com eles no Microsoft Flow. Adicione as contas do Microsoft Flow deles como proprietários em seu fluxo de trabalho.

1. Se você voltar ao seu aplicativo da IoT Central, você verá que essa regra tem uma ação do Microsoft Flow na área Ações.

Você sempre pode começar a criar um fluxo de trabalho usando o conector da IoT Central no Microsoft Flow. Em seguida, você pode escolher a qual aplicativo da IoT Central e a qual regra se conectar.

## <a name="create-a-device-in-a-workflow"></a>Criar um dispositivo em um fluxo de trabalho

Esta seção mostra como criar um novo dispositivo na IoT Central por meio de um botão em um dispositivo móvel usando o aplicativo móvel Microsoft Flow. Você pode usar essa ação no Flow para integrar sistemas ERP, tais como o Dynamics, com a IoT Central, criando um novo dispositivo quando um dispositivo é adicionado em outro lugar.

1. Comece criando um fluxo de trabalho em branco no Microsoft Flow.

1. Pesquise por **botão do Flow para dispositivos móveis** como um gatilho.

1. Nesse gatilho, adicione uma entrada de texto chamada **Nome do dispositivo**. Altere o texto da descrição, substituindo-o por **Insira o nome do dispositivo do seu novo dispositivo**.

1. Adicione uma nova ação. Pesquise pela ação **Azure IoT Central – Criar um dispositivo**.

1. Escolha seu aplicativo e escolha um modelo de dispositivo do qual criar um dispositivo nas listas suspensas. Você verá a ação se expandir para mostrar todas as propriedades e configurações do dispositivo.

1. Selecione o campo Nome do Dispositivo. No painel de conteúdo dinâmico, escolha **Nome do Dispositivo**. Esse valor será passado da entrada inserida pelo usuário por meio do aplicativo móvel e será o nome de seu novo dispositivo na IoT Central. Neste exemplo, o único campo obrigatório é o nome do dispositivo, indicado por um asterisco vermelho. Outro modelo de dispositivo pode ter vários campos obrigatórios que precisam ser preenchidos para criar um novo dispositivo.

    ![Painel dinâmico da ação de criar dispositivo do Flow](./media/howto-add-microsoft-flow-experimental/flowcreatedevice.png)
1. (Opcional) Preencha outros campos conforme considerar adequado para criar novos dispositivos.

1. Por fim, salve o fluxo de trabalho.

1. Experimente o fluxo de trabalho no aplicativo móvel do Microsoft Flow. Navegue até a guia **botões** no aplicativo. Você deve ver o Botão -> Criar um novo fluxo de trabalho do dispositivo. Insira o nome do novo dispositivo e assista enquanto ele aparece na IoT Central!

    ![Captura de tela de dispositivo móvel para criação de dispositivo do Flow](./media/howto-add-microsoft-flow-experimental/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Atualizar um dispositivo em um fluxo de trabalho

Esta seção mostra como atualizar as configurações e propriedades de um dispositivo na IoT Central apenas pressionando um botão em um dispositivo móvel usando o aplicativo móvel Microsoft Flow.

1. Comece criando um fluxo de trabalho em branco no Microsoft Flow.

1. Pesquise por **botão do Flow para dispositivos móveis** como um gatilho.

1. Nesse gatilho, adicione uma entrada como uma entrada de texto **Local** que corresponda a uma configuração ou a propriedade que você deseja alterar. Altere o texto de descrição.

1. Adicione uma nova ação. Pesquise pela ação **Azure IoT Central – Atualizar um dispositivo**.

1. Selecione o aplicativo na lista suspensa. Agora, você precisará da ID do dispositivo existente que deseja atualizar. É possível obter a ID do dispositivo da IoT Central na URL do **Device Explorer**.

    ![ID do dispositivo do Device Explorer IoT Central](./media/howto-add-microsoft-flow-experimental/iotcdeviceid.png)

1. Você pode atualizar o nome do dispositivo. Para atualizar qualquer uma das configurações e propriedades do dispositivo, você precisa selecionar o modelo de dispositivo do dispositivo que você deseja atualizar na lista suspensa **Modelo de Dispositivo**. O bloco de ação se expande para mostrar todas as propriedades e configurações que você pode atualizar.

    ![O fluxo de trabalho do fluxo de atualização de dispositivo](./media/howto-add-microsoft-flow-experimental/flowupdatedevice.png)

1. Selecione cada uma das propriedades e configurações que você deseja atualizar. No painel de conteúdo dinâmico, escolha a entrada correspondente do gatilho. Neste exemplo, o valor Localização é propagado para baixo para atualizar a propriedade Localização do dispositivo.

1. Por fim, salve o fluxo de trabalho.

1. Experimente o fluxo de trabalho no aplicativo móvel do Microsoft Flow. Navegue até a guia **botões** no aplicativo. Você deverá ver o Botão -> Atualizar um fluxo de trabalho do dispositivo. Insira as entradas e veja o dispositivo ser atualizado na IoT Central!

## <a name="delete-a-device-in-a-workflow"></a>Excluir um dispositivo em um fluxo de trabalho

Você pode excluir um dispositivo por sua ID do dispositivo usando a ação **Azure IoT Central – Excluir um dispositivo**. Aqui está um fluxo de trabalho de exemplo que exclui um dispositivo apenas pressionando um botão no aplicativo móvel Microsoft Flow.

   ![Fluxo de trabalho de exclusão de dispositivo do Flow](./media/howto-add-microsoft-flow-experimental/flowdeletedevice.png)
    
## <a name="troubleshooting"></a>solução de problemas

Se você está tendo problemas ao criar uma conexão para o conector do Azure IoT Central, aqui estão algumas dicas para ajudá-lo.

1. Contas pessoais da Microsoft (assim como os domínios @hotmail.com, @live.com, @outlook.com) não são compatíveis no momento. Você precisa usar uma conta corporativa ou de estudante do Azure AD.

2. Para usar o conector da IoT Central no Microsoft Flow, você precisa ter entrado no aplicativo da IoT Central pelo menos uma vez. Caso contrário, o aplicativo não aparecerá nas listas suspensas de aplicativos.

3. Se você estiver recebendo um erro enquanto estiver usando uma conta do AAD, tente abrir o Windows PowerShell e executar os commandlets a seguir como administrador.
    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```
    
## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como usar o Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerenciar dispositivos](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

