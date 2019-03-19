---
title: Criar e executar trabalhos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Os trabalhos do Azure IoT Central permitem o gerenciamento de dispositivos em massa, como atualizar uma propriedade do dispositivo, configurar ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: bb5eb2904ae969c3ffcd4ee8e365a51853add2fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182227"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar trabalhos em seu aplicativo Azure IoT Central

É possível usar o Microsoft Azure IoT Central para gerenciar seus dispositivos conectados em escala usando trabalhos. A funcionalidade de trabalhos permite que você execute atualizações em massa de propriedades, configurações e comandos do dispositivo. Este artigo explica como começar a usar os trabalhos em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

Esta seção mostra como criar e executar um trabalho. Cada etapa percorre um exemplo que demonstra como executar um trabalho em dispositivos de máquina de vendas refrigeradas que precisam a velocidade da ventoinha aumentada.

1. Navegue até o painel Trabalhos no painel de navegação.

1. Selecione **+ novo** para começar a criar um novo trabalho.

    ![Criar novo trabalho](./media/howto-run-a-job/createnewjob.png)

1. Insira um nome e uma descrição que o ajudem a identificar o trabalho que está criando.

1. Selecione o conjunto de dispositivos a que você deseja que o trabalho se aplique. Depois de selecionar o dispositivo, consulte o lado direito popular com os dispositivos dentro do conjunto de dispositivo selecionado. Se você selecionar um conjunto de dispositivos quebrada, nenhum dispositivo exibido e você verá uma mensagem explicando que o seu conjunto de dispositivos é interrompido.

1. Em seguida, escolha o tipo de trabalho para definir (uma configuração, uma propriedade ou um comando). Selecione **+** ao lado do tipo de trabalho selecionado e adicione suas operações desejadas.

    ![Configurar trabalho](./media/howto-run-a-job/configurejob.png)

1. No lado direito, escolha os dispositivos em que você gostaria de executar o trabalho. Selecionando a caixa de seleção superior, todos os dispositivos são selecionados no conjunto de dispositivo inteiro. Marcando a caixa de seleção perto **nome**, todos os dispositivos na página atual estão selecionados.

1. Depois de selecionar seus dispositivos, escolha **executados** ou **salvar**. O trabalho agora aparece em sua principal **trabalhos** página. Nesta exibição, você pode ver seu trabalho em execução no momento e o histórico de qualquer anteriormente executar trabalhos. Seu trabalho em execução sempre aparece na parte superior da lista. Seu trabalho salvo pode ser aberto novamente a qualquer momento para continuar a editar ou executar.

    ![Exibir trabalho](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Você pode exibir o histórico de seus trabalhos executados anteriormente por até 30 dias.

1. Para obter uma visão geral do seu trabalho, selecione o nome do trabalho que você deseja exibir na lista. Esta visão geral contém os detalhes do trabalho, os dispositivos e os valores de status do dispositivo. Esta visão geral, você pode selecionar também **detalhes do trabalho de Download** para baixar um arquivo. csv de detalhes do seu trabalho, incluindo os dispositivos e seus valores de status. Essas informações podem ser úteis para solução de problemas.

    ![Exibir status do dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Parar um trabalho em execução

Se você quiser interromper um trabalho que está sendo executado, selecione o nome do trabalho em execução que você gostaria de interromper. Escolha o botão **Parar** no painel. Você verá que o status do trabalho foi alterado para refletir que o trabalho foi interrompido.

   ![Parar o trabalho](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Executar um trabalho interrompido

Para executar um trabalho que está parado no momento, selecione o nome do trabalho parado que você deseja executar. Escolha o botão **Executar** no painel. As alterações de status do trabalho para refletir que o trabalho está em execução novamente.

   ![Trabalho retomado](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Um trabalho de cópia

Para copiar um trabalho existente que você criou, selecioná-lo na página principal de trabalhos e selecione **cópia**. Isso abre uma nova cópia da configuração do trabalho para edição. Você pode salvar ou executar o novo trabalho. Se todas as alterações foram feitas ao seu conjunto de dispositivo selecionado, eles serão refletidos neste trabalho copiado para edição.

   ![trabalho de cópia](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Exibir o status do trabalho

Depois que um trabalho tiver sido criado, o **Status** atualizações de coluna com a mensagem de status mais recente do trabalho. As mensagens de status significam o seguinte:

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

Para exibir o status de cada dispositivo no trabalho, selecione o nome do trabalho. Você ver os detalhes do trabalho e todos os dispositivos que foram uma parte dele. Você pode selecionar **baixar detalhes do trabalho** para baixar um arquivo. csv que inclui os detalhes do trabalho, os dispositivos e seus valores de status. Ao lado de cada nome de dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Com falha               | A execução do trabalho falhou neste dispositivo. A mensagem de erro que acompanha este artigo mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                  |

> [!NOTE]
> Se um dispositivo tiver sido excluído, não é possível selecionar o dispositivo e o exibe como excluído com a ID do dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar trabalhos no aplicativo Azure IoT Central, estas são algumas das próximas etapas:

- [Usar conjuntos de dispositivos](howto-use-device-sets.md)
- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-devicetemplate.md)
