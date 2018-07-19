---
title: Atualizar o Hub IoT | Microsoft Docs
description: Altere o tipo e escala de preço do Hub IoT para obter mais recursos de gerenciamento de dispositivo e mensagens.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 1f60b7d30c073c49d5e0a7d35e7263c2181ed744
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903057"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Como fazer upgrade do Hub IoT

Na medida em que a solução de IoT aumentar, o Hub IoT estará pronto para ajudá-lo a escalar verticalmente. O Hub IoT oferece duas camadas, básico (B) e standard (S), para acomodar clientes que desejam usar recursos diferentes. Dentro de cada camada há três tamanhos (1, 2 e 3) que determinam o número de mensagens que podem ser enviadas por dia. 

Quando você tiver mais dispositivos e precisar de mais recursos, haverá três maneiras de ajustar o Hub IoT para atender às suas necessidades:

* Adicione unidades no Hub IoT. Por exemplo, cada unidade adicional em um Hub IoT B1 permite mais 400.000 mensagens por dia. 
* Altere o tamanho do Hub IoT. Por exemplo, migre da camada B1 para a camada B2 para aumentar a quantidade de mensagens que cada unidade pode fornecer suporte por dia.
* Atualize para uma camada superior. Por exemplo, atualize da camada B1 para a camada S1 para a mesma capacidade de mensagens, mas com os recursos avançados que são fornecidos na camada Standard.

Todas essas mudanças podem ocorrer sem interromper as operações existentes.

Se você quiser fazer downgrade do Hub IoT, poderá remover as unidades e reduzir o tamanho do Hub IoT. No entanto, não é possível fazer downgrade para um nível inferior. Por exemplo, você pode mover da camada S2 para a camada de S1, mas não da camada S2 para o nível B1. 

Esses exemplos destinam-se a ajudá-lo a reconhecer como ajustar o Hub IoT na medida em que a solução altera. Para obter informações específicas sobre os recursos de cada camada, sempre consulte o [Preço do Hub IoT ](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Fazer upgrade do Hub IoT existente 

1. Entre no [Portal do Azure](https://portal.azure.com/) e navegue até o Hub IoT. 
2. Selecione **Preço e escala**. 

   ![Preços e dimensionamento](./media/iot-hub-upgrade/pricing-scale.png)

3. Para alterar a camada do hub, selecione **Tipo e escala de preço**. Escolha a nova camada e clique em**selecionar**.

   ![Preços e dimensionamento](./media/iot-hub-upgrade/select-tier.png)

4. Para alterar o número de unidades no hub, insira um novo valor em **Unidades do Hub IoT**. 
5. Selecione **Salvar** para salvar as alterações. 

O Hub IoT agora está ajustado e as configurações permanecem inalteradas. Observe que o limite de partições para a camada Básica do Hub IoT é 8. Esse limite permanecerá inalterado quando você migrar da camada Básica para a camada Standard.

## <a name="next-steps"></a>Próximas etapas

Obtenha mais detalhes sobre [Como escolher a camada correta do Hub IoT](iot-hub-scaling.md). 

