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
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519362"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar trabalhos em seu aplicativo Azure IoT Central

É possível usar o Microsoft Azure IoT Central para gerenciar seus dispositivos conectados em escala usando trabalhos. Trabalhos permitem fazer atualizações para as propriedades do dispositivo, as configurações e comandos em massa. Este artigo explica como começar a usar os trabalhos em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

Esta seção mostra como criar e executar um trabalho. Ele mostra como aumentar a velocidade para várias máquinas de vendas refrigerated.

1. Navegue até o painel Trabalhos no painel de navegação.

1. Selecione **+ novo** para criar um novo trabalho.

    ![Criar novo trabalho](./media/howto-run-a-job/createnewjob.png)

1. Insira um nome e uma descrição para identificar o trabalho que você está criando.

1. Selecione o conjunto de dispositivo que você deseja que o trabalho para aplicar a. Depois de selecionar o dispositivo, consulte o lado direito popular com os dispositivos no conjunto de dispositivos. Se você selecionar um conjunto de dispositivos quebrada, nenhum dispositivo exibido e você verá uma mensagem que seu conjunto de dispositivo é interrompido.

1. Em seguida, escolha o tipo de trabalho para definir (uma configuração, uma propriedade ou um comando). Selecione **+** ao lado do tipo de trabalho selecionado e adicione suas operações.

    ![Configurar trabalho](./media/howto-run-a-job/configurejob.png)

1. No lado direito, escolha os dispositivos que você gostaria de executar o trabalho no. Selecionando a caixa de seleção superior, todos os dispositivos são selecionados no conjunto de dispositivo inteiro. Marcando a caixa de seleção perto **nome**, todos os dispositivos na página atual estão selecionados.

1. Depois de selecionar seus dispositivos, escolha **executados** ou **salvar**. O trabalho agora aparece em sua principal **trabalhos** página. Nesta exibição, você pode ver seu trabalho em execução no momento e o histórico de qualquer anteriormente executar trabalhos. Seu trabalho em execução sempre aparece na parte superior da lista. Seu trabalho salvo pode ser aberto novamente a qualquer momento para continuar a editar ou executar.

    ![Exibir trabalho](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Você pode exibir o histórico de seus trabalhos executados anteriormente por até 30 dias.

1. Para obter uma visão geral do seu trabalho, selecione o trabalho para exibir na lista. Esta visão geral contém os detalhes do trabalho, os dispositivos e os valores de status do dispositivo. Esta visão geral, você pode selecionar também **detalhes do trabalho de Download** para baixar um arquivo. csv de detalhes do seu trabalho, incluindo os dispositivos e seus valores de status. Essas informações podem ser úteis para solução de problemas.

    ![Exibir status do dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Parar um trabalho em execução

Para interromper um trabalho em execução, selecione-o e escolha **parar** no painel. As alterações de status do trabalho para refletir que o trabalho foi interrompido.

   ![Parar o trabalho](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Executar um trabalho interrompido

Para executar um trabalho que está parado no momento, selecione o trabalho parado. Escolher **executar** no painel. As alterações de status do trabalho para refletir o trabalho está em execução novamente.

   ![Trabalho retomado](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Um trabalho de cópia

Para copiar um trabalho existente que você criou, selecioná-lo na página principal de trabalhos e selecione **cópia**. Uma nova cópia da configuração do trabalho é aberto para edição. Você pode salvar ou executar o novo trabalho. Se todas as alterações foram feitas ao seu conjunto de dispositivo selecionado, eles estão refletidos neste trabalho copiado para edição.

   ![trabalho de cópia](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Exibir o status do trabalho

Depois que um trabalho é criado, o **Status** atualizações de coluna com a mensagem de status mais recente do trabalho. A tabela a seguir lista os valores de status possíveis:

| Mensagem de status       | Significado do status                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Com falha               | Esse trabalho falhou e não foi totalmente executado nos dispositivos.  |
| Pendente              | Esse trabalho ainda não tiver iniciado a execução em dispositivos.         |
| Executando              | Esse trabalho está sendo executado nos dispositivos.             |
| Parado              | Este trabalho foi interrompido manualmente por um usuário.           |

A mensagem de status é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir lista os valores de status do dispositivo:

| Mensagem de status       | Significado do status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Bem-sucedida            | O número de dispositivos que o trabalho foi executado com êxito.       |
| Com falha               | O número de dispositivos que o trabalho falhou ao executar em.       |

### <a name="view-the-device-status"></a>Exibir o status do dispositivo

Para exibir o status do trabalho e todos os dispositivos afetados, selecione o trabalho. Para baixar um arquivo. csv que inclui os detalhes do trabalho, incluindo a lista de dispositivos e seus valores de status, selecione **baixar detalhes do trabalho**. Ao lado de cada nome de dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Com falha               | A execução do trabalho falhou neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executada neste dispositivo.                                   |

> [!NOTE]
> Se um dispositivo tiver sido excluído, não é possível selecionar o dispositivo e o exibe como excluído com a ID do dispositivo.

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu como criar trabalhos em seu aplicativo do Azure IoT Central, aqui estão algumas das próximas etapas:

- [Usar conjuntos de dispositivos](howto-use-device-sets.md)
- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-devicetemplate.md)
