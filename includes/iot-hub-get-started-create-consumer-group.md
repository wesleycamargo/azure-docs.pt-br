---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 57a5de4c590ff98429aa1d4a0b96cebed6084a0f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803225"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Os grupos de consumidores são utilizados pelas aplicações para obter dados do Hub IoT do Azure. Neste tutorial, você cria um grupo de consumidores a ser usado por um serviço Azure que entra para ler dados do hub IoT.

Para adicionar um grupo de consumidores ao hub IoT, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, clique em **Pontos de extremidade internos**, selecione **Eventos** no painel superior e insira um nome em **Grupos de consumidores** no painel direito. Clique em **Salvar** depois de alterar o valor de **TTL Padrão** e retorne-o para o valor original.

   ![Criar um grupo de consumidores no hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
