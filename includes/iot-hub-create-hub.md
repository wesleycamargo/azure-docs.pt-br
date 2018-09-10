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
ms.openlocfilehash: fce9e42e24be7f8678292a5d98a683ca4e579cd2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39357054"
---
1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.
   
    ![Captura de tela de navegação do portal do Azure para Hub IoT](./media/iot-hub-create-hub/create-iot-hub1.png)

3. No painel **Hub IoT**, insira as seguintes informações para o Hub IoT:

   * **Assinatura**: escolha a assinatura que deseja usar para criar esse hub IoT.

   * **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Para saber mais, veja [Usar os grupos de recursos para gerenciar seus recursos do Azure](../articles/azure-resource-manager/resource-group-portal.md).

   * **Região**: selecione o local mais próximo de você.

   * **Nome**: cria um nome para o hub IoT. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Janela Noções básicas do Hub IoT](./media/iot-hub-create-hub/create-iot-hub2.png)

4. Selecione **Próximo: Tamanho e escala** para continuar criando o hub IoT. 

5. Escolha **Tipo de preço e escala**. Para este artigo, selecione a camada **F1 – Gratuita**, caso ela ainda esteja disponível em sua assinatura. Para saber mais, confira [Tipo de escala e preço](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Janela Tamanho e escala do Hub IoT](./media/iot-hub-create-hub/create-iot-hub3.png)

6. Selecione **Examinar + criar**.

7. Examine as informações do hub IoT e clique em **Criar**. A criação do seu hub IoT pode levar alguns minutos. Você pode monitorar o progresso no painel **Notificações**.