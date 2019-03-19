---
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 242f601ced4838a1b4e559774c25d05de04ddb77
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "57016356"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Os grupos de consumidores são utilizados pelas aplicações para obter dados do Hub IoT do Azure. Neste tutorial, você cria um grupo de consumidores a ser usado por um serviço Azure que entra para ler dados do hub IoT.

Para adicionar um grupo de consumidores ao hub IoT, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. No painel esquerdo, clique em **Pontos de extremidade internos**, selecione **Eventos** no painel superior e insira um nome em **Grupos de consumidores** no painel direito. Clique em **Salvar** depois de alterar o valor de **TTL Padrão** e retorne-o para o valor original.

   ![Criar um grupo de consumidores no hub IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
