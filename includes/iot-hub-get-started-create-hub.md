---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 08afdcf91499fdb6f2da6e9ccc82313286f5c964
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43111964"
---
## <a name="create-an-iot-hub"></a>Crie um hub IoT
Crie um Hub IoT ao qual o aplicativo de dispositivo simulado poderá se conectar. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.
   
    ![Barra de Navegação do portal do Azure](./media/iot-hub-get-started-create-hub/create-iot-hub1.png)

3. No painel **Hub IoT**, insira as seguintes informações para o Hub IoT:

   * **Assinatura**: escolha a assinatura que deseja usar para criar esse hub IoT.

   * **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Para saber mais, veja [Usar os grupos de recursos para gerenciar seus recursos do Azure](../articles/azure-resource-manager/resource-group-portal.md).

   * **Região**: selecione o local mais próximo de você.

   * **Nome**: cria um nome para o hub IoT. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Janela Noções básicas do Hub IoT](./media/iot-hub-get-started-create-hub/create-iot-hub2.png)

4. Selecione **Próximo: Tamanho e escala** para continuar criando o hub IoT. 

5. Escolha **Tipo de preço e escala**. Para este artigo, selecione a camada **F1 – Gratuita**, caso ela ainda esteja disponível em sua assinatura. Para saber mais, confira [Tipo de escala e preço](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Janela Tamanho e escala do Hub IoT](./media/iot-hub-get-started-create-hub/create-iot-hub3.png)

6. Selecione **Examinar + criar**.

7. Examine as informações do hub IoT e clique em **Criar**. A criação do seu hub IoT pode levar alguns minutos. Você pode monitorar o progresso no painel **Notificações**.

8. Quando seu novo Hub IoT estiver pronto, clique no bloco dele no Portal do Azure para abrir a janela Propriedades. Agora que você criou um hub IoT, localize as informações importantes que você usa para se conectar a dispositivos e aplicativos para o hub IoT. Clique em **Políticas de acesso compartilhado**.
   
9. Em **Políticas de acesso compartilhado**, selecione a política **iothubowner**. Copie a **Cadeia de conexão – chave primária** do Hub IoT para uso futuro. Para obter mais informações, consulte [Controle de acesso](../articles/iot-hub/iot-hub-devguide-security.md) no "Guia do desenvolvedor do Hub IoT".
   
    ![Políticas de acesso compartilhado](./media/iot-hub-get-started-create-hub/create-iot-hub5.png)