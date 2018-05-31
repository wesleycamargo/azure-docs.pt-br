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
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371228"
---
## <a name="create-an-iot-hub"></a>Crie um hub IoT
Crie um Hub IoT ao qual o aplicativo de dispositivo simulado poderá se conectar. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

1. Entre no [Portal do Azure][lnk-portal].

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.
   
    ![Barra de Navegação do portal do Azure][1]

1. No painel **Hub IoT**, insira as seguintes informações para o Hub IoT:

   * **Assinatura**: escolha a assinatura que deseja usar para criar esse hub IoT.

   * **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Para obter mais informações, consulte [Usar grupos de recursos para gerenciar seus recursos do Azure][lnk-resource-groups].

   * **Região**: selecione o local mais próximo de você.

   * **Nome**: cria um nome para o hub IoT. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Janela Noções básicas do Hub IoT][2]

2. Selecione **Próximo: Tamanho e escala** para continuar criando o hub IoT. 

3. Escolha **Tipo de preço e escala**. Para este artigo, selecione a camada **F1 – Gratuita**, caso ela ainda esteja disponível em sua assinatura. Para obter mais informações, confira [Tipo de escala e preço][lnk-pricing].

   ![Janela Tamanho e escala do Hub IoT][3]

4. Selecione **Examinar + criar**.

1. Examine as informações do hub IoT e clique em **Criar**. A criação do seu hub IoT pode levar alguns minutos. Você pode monitorar o progresso no painel **Notificações**.

1. Quando seu novo Hub IoT estiver pronto, clique no bloco dele no Portal do Azure para abrir a janela Propriedades. Agora que você criou um hub IoT, localize as informações importantes que você usa para se conectar a dispositivos e aplicativos para o hub IoT. Clique em **Políticas de acesso compartilhado**.
   
1. Em **Políticas de acesso compartilhado**, selecione a política **iothubowner**. Copie a **Cadeia de conexão – chave primária** do Hub IoT para uso futuro. Para saber mais, confira [Controle de acesso][lnk-access-control] no "Guia do desenvolvedor do Hub IoT".
   
    ![Políticas de acesso compartilhado][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
