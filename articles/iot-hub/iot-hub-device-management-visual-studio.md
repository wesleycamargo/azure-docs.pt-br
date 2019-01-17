---
title: Gerenciamento de dispositivo do Azure IoT com o Cloud Explorer para Visual Studio | Microsoft Docs
description: Use o Cloud Explorer para Visual Studio para p gerenciamento de dispositivo do Hub IoT do Azure, apresentando os métodos Direto e as opções de gerenciamento de propriedades desejadas do Gêmeo.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: f0cb740f04970aeed4342511a046862f05aaa736
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109221"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Usar o Cloud Explorer para Visual Studio para gerenciamento de dispositivo do Hub IoT do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

O [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) para é uma extensão útil para Visual Studio que permite exibir os recursos do Azure, inspecionar suas propriedades e executar ações chave do desenvolvedor de dentro do Visual Studio. Ela é fornecida com opções de gerenciamento que podem ser usadas para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gerenciamento          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça com que um dispositivo comece ou pare de enviar mensagens ou seja reinicializado.                                        |
| Leitura de dispositivo gêmeo           | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando no momento.                                    |
| Atualizar dispositivo gêmeo         | Coloque um dispositivo em alguns estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Mensagens da nuvem para o dispositivo   | Envie notificações para um dispositivo. Por exemplo, “É muito provável que chova hoje. Não se esqueça de levar um guarda-chuva”.              |

Para obter explicações mais detalhadas sobre as diferenças e diretrizes sobre como usar essas opções, consulte [Diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e [Diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte [Introdução aos dispositivos gêmeos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a usar o Cloud Explorer para Visual Studio com várias opções de gerenciamento em seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Execute o Cloud Explorer para Visual Studio com várias opções de gerenciamento.

## <a name="what-you-need"></a>O que você precisa

- Uma assinatura ativa do Azure.
- Um Hub IoT do Azure em sua assinatura.
- Microsoft Visual Studio 2017 Atualização 8 ou posterior
- Componente de Cloud Explorer do Instalador do Visual Studio (selecionado por padrão com a Carga de Trabalho do Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Atualize o Cloud Explorer para a versão mais recente

O componente Cloud Explorer do Instalador do Visual Studio é compatível apenas com monitoramento de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo. Você precisará baixar e instalar o [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) mais recente para acessar as opções de gerenciamento.

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

1. Na janela **Cloud Explorer** do Visual Studio, clique no ícone de Gerenciamento de Conta. Você pode abrir a janela do Cloud Explorer usando o menu **Exibir** > **Cloud Explorer**.

    ![Clique em Gerenciamento de Conta](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Clique em **Gerenciar Contas** no Cloud Explorer.
1. Clique em **Adicionar uma conta…** na nova janela para entrar no Azure pela primeira vez.
1. Depois de entrar, sua lista de assinaturas do Azure será mostrada. Selecione as assinaturas do Azure que você deseja exibir e clique em **Aplicar**.
1. Expanda **Sua assinatura** > **Hubs IoT** > **Seu Hub IoT**; a lista de dispositivos será mostrada sob o nó do seu Hub IoT. Clique com o botão direito do mouse em um dispositivo para acessar as opções de gerenciamento.

    ![Opções de gerenciamento](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Métodos diretos

1. Clique com o botão direito do mouse no seu dispositivo e selecione **Invocar Método Direto de Dispositivo**.
1. Insira o nome do método e a carga na caixa de entrada.
1. Os resultados serão mostrados no painel de saída do **Hub IoT**.

## <a name="read-device-twin"></a>Leitura de dispositivo gêmeo

1. Clique com o botão direito e selecione **Editar Gêmeo de Dispositivo**.
1. Um arquivo **azure-iot-dispositivo-twin.json** será aberto com o conteúdo do dispositivo gêmeo.

## <a name="update-device-twin"></a>Atualizar dispositivo gêmeo

1. Faça algumas edições das **marcas** ou do campo **properties.desired** para o arquivo **azure-iot-dispositivo-twin.json**.
1. Pressione **Ctrl+S** para atualizar o dispositivo gêmeo.
1. Os resultados serão mostrados no painel de saída do **Hub IoT**.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo

Para enviar uma mensagem do Hub IoT para o dispositivo, siga estas etapas:

1. Clique com o botão direito do mouse no seu dispositivo e selecione **Enviar Mensagem de C2D**.
1. Insira a mensagem na caixa de entrada.
1. Os resultados serão mostrados no painel de saída do **Hub IoT**.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a usar o Cloud Explorer para Visual Studio com várias opções de gerenciamento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]