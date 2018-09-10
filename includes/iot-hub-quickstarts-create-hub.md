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
ms.openlocfilehash: 63acf0297a694ff442d56e67d52fd9b4e49f812d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008579"
---
A primeira etapa é usar o portal do Azure para criar um hub IoT na sua assinatura. O hub IoT permite a ingestão de grandes volumes de telemetria de vários dispositivos pela nuvem. O hub, em seguida, permite que um ou mais serviços de back-end em execução na nuvem leiam e processem essa telemetria.

1. Entre no [Portal do Azure](http://portal.azure.com).

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

    ![Selecione para instalar o Hub IoT][1]

1. No painel **Hub IoT**, insira as seguintes informações para o Hub IoT:

   * **Assinatura**: escolha a assinatura que deseja usar para criar esse hub IoT.
   * **Grupo de recursos**: crie um grupo de recursos para conter o hub IoT ou usar um existente. Ao colocar todos os recursos relacionados em um grupo, como **TestResources**, você poderá gerenciá-los juntos. Por exemplo, a exclusão do grupo de recursos exclui todos os recursos contidos nesse grupo. Para obter mais informações, consulte [Usar grupos de recursos para gerenciar seus recursos do Azure][lnk-resource-groups].
   * **Região**: selecione o local mais próximo de seus dispositivos.
   * **Nome**: crie um nome exclusivo para o hub IoT. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Janela Noções básicas do Hub IoT][2]

2. Selecione **Próximo: Tamanho e escala** para continuar criando o hub IoT. 

3. Escolha **Tipo de preço e escala**. Para este artigo, selecione a camada **F1 – Gratuita**, caso ela ainda esteja disponível em sua assinatura. Para obter mais informações, confira [Tipo de escala e preço][lnk-pricing].

   ![Janela Tamanho e escala do Hub IoT][3]

4. Selecione **Examinar + criar**.

1. Examine as informações do hub IoT e clique em **Criar**. A criação do seu hub IoT pode levar alguns minutos. Você pode monitorar o progresso no painel **Notificações**.


<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md