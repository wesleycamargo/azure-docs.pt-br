---
title: Criar e executar trabalhos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Os trabalhos do Azure IoT Central permitem o gerenciamento de dispositivos em massa, como atualizar uma propriedade do dispositivo, configurar ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/15/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 35db7bf87c7b72fc31d820c9058b1df8415bd553
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031298"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar trabalhos em seu aplicativo Azure IoT Central

É possível usar o Microsoft Azure IoT Central para gerenciar seus dispositivos conectados em escala usando trabalhos. A funcionalidade de trabalhos permite que você execute atualizações em massa de propriedades, configurações e comandos do dispositivo. Este artigo explica como começar a usar os trabalhos em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

Esta seção mostra como criar e executar um trabalho. Cada etapa percorre um exemplo que demonstra como executar um trabalho em dispositivos de máquina de vendas refrigeradas que precisam a velocidade da ventoinha aumentada.

1. Navegue até o painel Trabalhos no painel de navegação.

1. Clique em **+ Novo** para começar a criar um novo trabalho.

    ![Criar novo trabalho](./media/howto-run-a-job/createnewjob.png)

1. Insira um nome e uma descrição que o ajudem a identificar o trabalho que está criando.

1. Selecione o conjunto de dispositivos a que você deseja que o trabalho se aplique. Depois de selecionar o conjunto de dispositivos, você verá o lado direito ser populado com os dispositivos do conjunto selecionado. Se você selecionar um conjunto de dispositivos desfeito, nenhum dispositivo será exibido e você verá uma mensagem explicando que o conjunto de dispositivos foi desfeito.

1. Em seguida, escolha o tipo de trabalho que será definido (uma configuração, propriedade ou comando). Clique em **+** ao lado do tipo de trabalho selecionado e adicione suas operações desejadas.

    ![Configurar trabalho](./media/howto-run-a-job/configurejob.png)

1. No lado direito, escolha os dispositivos em que você gostaria de executar o trabalho. Ao clicar na caixa de seleção superior, todos os dispositivos do conjunto são selecionados. Ao clicar na caixa de seleção ao lado do Nome, todos os dispositivos na página atual são selecionados.

1. Depois de selecionar os dispositivos desejados, escolha **Executar**. O trabalho aparecerá na sua página de **Trabalhos** principal. Nesta exibição, você pode ver seu trabalho em execução no momento e o histórico de qualquer trabalho executado anteriormente. Seu trabalho em execução sempre será exibido na parte superior da lista.

    ![Executar trabalho](./media/howto-run-a-job/runjob.png)

    ![Exibir trabalho](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Você poderá exibir o histórico de seus trabalhos executados anteriormente por até 30 dias.

1. Para obter uma visão geral do seu trabalho, clique no nome do trabalho que deseja exibir na lista. Esta visão geral contém os detalhes do trabalho, dispositivos e os status do dispositivo.

    ![Exibir status do dispositivo](./media/howto-run-a-job/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>Parar um trabalho em execução

Se você quiser parar um trabalho que está sendo executado, clique no nome do trabalho que gostaria de interromper. Escolha o botão **Parar** no painel. Você verá que o status do trabalho foi alterado para refletir que o trabalho foi interrompido.

> [!NOTE]
> Depois de interrompido, o trabalho não pode ser reiniciado. Você precisa criar outro trabalho com as operações e dispositivos desejados.

## <a name="view-the-job-status"></a>Exibir o status do trabalho

Quando um trabalho tiver sido criado, a coluna **Status** será atualizada com a mensagem de status mais recente do trabalho. As mensagens de status significam o seguinte:

| Mensagem de status       | Significado do status                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Com falha               | Esse trabalho falhou e não foi totalmente executado nos dispositivos.  |
| Pendente              | Esse trabalho ainda não começou a ser executado em dispositivos.        |
| Executando              | Esse trabalho está sendo executado nos dispositivos.             |
| Parado              | Este trabalho foi interrompido manualmente por um usuário.           |

A mensagem de status é seguida por uma visão geral dos dispositivos dentro do trabalho. Os status do dispositivo significam o seguinte:

| Mensagem de status       | Significado do status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Bem-sucedida            | O número de dispositivos em que o trabalho foi executado com êxito.  |
| Com falha               | O número de dispositivos em que o a execução do trabalho falhou.      |

### <a name="view-the-device-status"></a>Exibir o status do dispositivo

Para exibir o status de cada dispositivo no trabalho, clique no nome do trabalho. Aqui, você verá os detalhes do trabalho e todos os dispositivos que faziam parte desse trabalho específico. Ao lado de cada nome de dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Com falha               | A execução do trabalho falhou neste dispositivo. A mensagem de erro que acompanha este artigo mostrará mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                  |

> [!NOTE]
> Se um dispositivo tiver sido excluído, não será possível selecionar o dispositivo e ele será exibido como excluído com a ID do dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar trabalhos no aplicativo Azure IoT Central, estas são algumas das próximas etapas:

- [Usar conjuntos de dispositivos](howto-use-device-sets.md)
- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-devicetemplate.md)
