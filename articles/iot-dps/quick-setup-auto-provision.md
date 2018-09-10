---
title: Configurar Provisionamento de Dispositivos no portal do Azure | Microsoft Docs
description: Guia de Início Rápido do Azure - configurar o serviço de Provisionamento de Dispositivos do Hub IoT do Azure no Portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 07/12/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ce1586e472e1d1ea5ddd9ca5a426b1bea2b5b931
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42023792"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Configurar o Serviço de Provisionamento de Dispositivos do Hub IoT com o portal do Azure

Estas etapas mostram como configurar os recursos de nuvem do Azure no portal para provisionar seus dispositivos. Este artigo inclui etapas para criar um novo IoT Serviço de Provisionamento de Dispositivos no Hub IoT e vincular os dois serviços. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Criar uma nova instância para o Serviço de Provisionamento de Dispositivos do Hub IoT

1. Clique no botão **Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.

2. *Pesquise no Marketplace* o **serviço de Provisionamento de Dispositivos**. Selecione o botão **Serviço de Provisionamento de Dispositivos no Hub IoT** e clique em **Criar**. 

3. Forneça as seguintes informações para a nova instância de serviço de provisionamento de dispositivo e clique em **Criar**.

    * **Nome:** forneça um nome exclusivo para sua nova instância de serviço de provisionamento do dispositivo. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.
    * **Assinatura:** escolha a assinatura que você deseja usar para criar essa instância de Serviço de Provisionamento do Dispositivo.
    * **Grupo de recursos:** este campo permite que você crie um novo grupo de recursos ou escolha um existente para conter a nova instância. Escolha o mesmo grupo de recursos que contenha o hub Iot criado acima, por exemplo, **TestResources**. Ao colocar todos os recursos relacionados em um grupo, você pode gerenciá-los juntos. Por exemplo, a exclusão do grupo de recursos exclui todos os recursos contidos nesse grupo. Para saber mais, veja [Usar os grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/resource-group-portal.md).
    * **Local**: selecione o local mais próximo para seus dispositivos.
    * **Fixar no painel:** selecione esta opção para que a instância fixada no painel tornando mais fácil de encontrar.

    ![Inserir as informações básicas sobre sua instância do Serviço Provisionamento de Dispositivos na folha do portal](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Depois que o serviço é implantado com êxito, a folha de resumo é aberta automaticamente.


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Vincular o Hub IoT e o Serviço de Provisionamento de Dispositivos

Nesta seção, você adicionará uma configuração para a instância do serviço de provisionamento de dispositivos. Essa configuração define o hub IoT para o qual os dispositivos serão provisionados.

1. Clique no botão **Todos os recursos** no menu à esquerda do portal do Azure. Selecione a instância de serviço de Provisionamento de Dispositivos que você criou na seção anterior.  

2. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Hubs IoT Vinculados**. Clique no botão **+ Adicionar** na parte superior. 

3. Sobre o **Adicionar link para o hub IoT**, forneça as informações a seguir para vincular a nova instância de serviço de provisionamento de dispositivo a um hub IoT. Em seguida, clique em **Salvar**. 

    * **Assinatura:** selecione a assinatura que contém o hub IoT que você deseja vincular com a nova instância de serviço de provisionamento do dispositivo.
    * **Hub Iot:** selecione o hub IoT para vincular com a nova instância de serviço de provisionamento do dispositivo.
    * **Política de acesso:** selecione **iothubowner** como credenciais para estabelecer o link com o hub IoT.  

    ![Vincular o nome do hub para vincular à instância do serviço de Provisionamento de Dispositivos na folha do portal](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Agora você deve ver o hub selecionado na folha **Hubs IoT Vinculados**. Talvez você precise clicar em **Atualizar** para mostrar **Hubs IoT vinculados**.



## <a name="clean-up-resources"></a>Limpar recursos

Outros Guias de Início Rápido na coleção aproveitam este Guia de Início Rápido. Se você planeja continuar trabalhando com Guias de Início Rápido ou tutoriais subsequentes, não limpe os recursos criados neste Guia de Início Rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse Guia de Início Rápido no portal do Azure.

1. No menu à esquerda no portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento do Dispositivo. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  
2. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  

## <a name="next-steps"></a>Próximas etapas

Neste Guia de Início Rápido, você implantou um Hub IoT e uma instância do Serviço de Provisionamento de Dispositivos, e vinculou os dois recursos. Para aprender a usar essa configuração a fim de provisionar um dispositivo simulado, prossiga para o Guia de Início Rápido de criação de dispositivo simulado.

> [!div class="nextstepaction"]
> [Guia de Início Rápido para criar dispositivo simulado](./quick-create-simulated-device.md)
