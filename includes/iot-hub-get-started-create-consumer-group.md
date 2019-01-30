---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 2d041ccbcf85f1931ca8ebc8b17595c9221c03cc
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54414487"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Os grupos de consumidores são utilizados pelas aplicações para obter dados do Hub IoT do Azure. Neste tutorial, você cria um grupo de consumidores a ser usado por um serviço Azure que entra para ler dados do hub IoT.

Para adicionar um grupo de consumidores ao hub IoT, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, clique em **Pontos de extremidade internos**, selecione **Eventos** no painel superior e insira um nome em **Grupos de consumidores** no painel direito. Clique em **Salvar** depois de alterar o valor de **TTL Padrão** e retorne-o para o valor original.

   ![Criar um grupo de consumidores no hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
