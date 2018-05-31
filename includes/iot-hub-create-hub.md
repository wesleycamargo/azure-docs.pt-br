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
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371143"
---
1. Entre no [Portal do Azure][lnk-portal].
1. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.
   
    ![Captura de tela de navegação do portal do Azure para Hub IoT][1]

1. No painel **Hub IoT**, insira as seguintes informações para o Hub IoT:

   * **Assinatura**: escolha a assinatura que deseja usar para criar esse Hub IoT.

   * **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Para obter mais informações, confira [Usar grupos de recursos para gerenciar seus recursos do Azure][lnk-resource-groups].

   * **Região**: selecione o local mais próximo a você.

   * **Nome**: cria um nome para o hub IoT. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Janela Noções básicas do Hub IoT][2]

2. Selecione **Próximo: Tamanho e escala** para continuar criando o Hub IoT. 

3. Escolha **Tipo de preço e escala**. Para este artigo, selecione a camada **F1 – Gratuita**, caso ela ainda esteja disponível em sua assinatura. Para obter mais informações, confira [Tipo de escala e preço][lnk-pricing].

   ![Janela Tamanho e escala do Hub IoT][3]

4. Selecione **Examinar + criar**.

1. Examine as informações do Hub IoT e, em seguida, clique em **Criar**. A criação do seu hub IoT pode levar alguns minutos. Você pode monitorar o progresso no painel **Notificações**.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md