---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0e8db8779958afe1fd3cf4bf12f2a6fd4a97c61c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165883"
---
<!--author=alkohli last changed: 9/16/15-->

#### <a name="to-cable-for-power"></a>Para cabear o recebimento de energia
1. Certifique-se de que os interruptores de energia em cada PCMs (Módulos de Energia e Refrigeração) estejam na posição de DESLIGADO.
2. Conecte os cabos de energia a cada PCMs no compartimento principal.
3. Conecte os cabos de energia às PDUs, conforme mostrado na imagem a seguir. Verifique se os dois PCMs usam fontes de energia separadas.
   
   > [!IMPORTANT]
   > Para garantir a alta disponibilidade do seu sistema, recomendamos que siga fielmente o esquema de cabeamento de energia mostrado no diagrama que segue. 
   > 
   > 
   
    ![Cabear o dispositivo 2U para recebimento de energia](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Fiação de energia em um dispositivo 8100**
   
   | Rótulo | DESCRIÇÃO |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Controlador 1 |
   | 3 |Controlador 0 |
   | 4 |PCM 1 |
   | 5 |PDUs |
4. Para ativar o sistema, inverta os comutadores de energia em ambos os PCMs para a posição LIGADO.

