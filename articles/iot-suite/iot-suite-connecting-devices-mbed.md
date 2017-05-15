---
title: Conectar um dispositivo usando C no mbed | Microsoft Docs
description: "Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em C em um dispositivo mbed."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: df9772796796f7383aafc583b01f299a53679d88
ms.openlocfilehash: 12535cbb6fa63c24dd63903380d697f8f38db6f9
ms.contentlocale: pt-br
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (mbed)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Criar e executar a solução de exemplo do C

As instruções a seguir descrevem as etapas para conectar um dispositivo [Freescale FRDM-K64F habilitado para mbed][lnk-mbed-home] para a solução de monitoramento remoto.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Conectar o dispositivo mbed ao computador desktop e à rede

1. Conecte o dispositivo mbed à sua rede usando um cabo Ethernet. Essa etapa é necessária porque o aplicativo de exemplo requer acesso à internet.

1. Confira [Getting Started with mbed][lnk-mbed-getstarted] (Introdução ao mbed) para conectar o dispositivo mbed ao seu computador.

1. Se o seu computador estiver executando o Windows, confira [PC Configuration][lnk-mbed-pcconnect] (Configuração de PC) para configurar o acesso de porta serial ao dispositivo mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Crie um projeto mbed e importe o código de exemplo

Siga estas etapas para adicionar um código de exemplo a um projeto mbed. Você importa o projeto inicial do monitoramento remoto e altera o projeto para usar o protocolo MQTT em vez do protocolo AMQP. Atualmente, você precisa usar o protocolo MQTT para usar os recursos de gerenciamento de dispositivo do Hub IoT.

1. No navegador da Web, vá até o [site de desenvolvedor](https://developer.mbed.org/)mbed.org. Caso ainda não tenha se inscrito, você verá uma opção para criar uma conta (é gratuita). Caso contrário, faça logon com suas credenciais de conta. Em seguida, clique em **Compilador** no canto superior direito da página. Essa ação exibe a interface do *Espaço de trabalho* .

1. Verifique se a plataforma de hardware que você está usando é exibida no canto superior direito da janela ou clique no ícone no canto superior direito para selecionar a plataforma de hardware.

1. Clique em **Importar** no menu principal. Em seguida, clique em **Clique aqui para importar da URL**.
   
    ![Iniciar a importação para o espaço de trabalho mbed][6]

1. Na janela pop-up, insira o link para o código de exemplo https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ e clique em **Importar**.
   
    ![Importar o código de exemplo para o espaço de trabalho mbed][7]

1. Você pode ver na janela do compilador mbed que a importação desse projeto também importa várias bibliotecas. Algumas são fornecidas e mantidas pela equipe do Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)[iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)[iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), enquanto outras são bibliotecas de terceiros disponíveis no catálogo de bibliotecas mbed.
   
    ![Exibir projeto mbed][8]

1. No **Espaço de Trabalho do Programa**, clique com o botão direito do mouse na biblioteca **iothub\_amqp\_transport**, clique em **Excluir**e em **OK** para confirmar.

1. No **Espaço de Trabalho do Programa**, clique com o botão direito do mouse na biblioteca **azure\_amqp\_c**, clique em **Excluir** e em **OK** para confirmar.

1. Clique com o botão direito do mouse no projeto **remote_monitoring** no **Espaço de Trabalho do Programa**, selecione **Importar Biblioteca** e selecione **Da URL**.
   
    ![Iniciar a importação da biblioteca para o espaço de trabalho mbed][6]

1. Na janela pop-up, insira o link para a biblioteca de transporte MQTT, https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/, e clique em **Importar**.
   
    ![Importar a biblioteca para o espaço de trabalho mbed][12]

1. Repita a etapa anterior para adicionar a biblioteca MQTT de https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c/.

1. Agora, seu espaço de trabalho é semelhante ao seguinte:

    ![Exibir espaço de trabalho mbed][13]

1. Abra o arquivo remote\_monitoring\remote_monitoring.c e substitua as instruções `#include` existentes pelo seguinte código:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. Exclua todo o código restante no arquivo remote\_monitoring\remote\_monitoring.c.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Adicione código para invocar a função **remote\_monitoring\_run** e compile e execute o aplicativo do dispositivo.

1. Adicionar uma função **main** com o seguinte código ao fim do controle arquivo remote\_monitoring.c para invocar a função **remote\_monitoring\_run**:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Clique em **Compilar** para criar o programa. Você pode ignorar os avisos com segurança, mas se a compilação gerar erros, corrija-os antes de continuar.

1. Se o build for bem-sucedido, o site do compilador mbed gerará um arquivo .bin com o nome do seu projeto e o baixa para o computador local. Copie o arquivo .bin para o dispositivo. Salvar o arquivo .bin no dispositivo faz com que o dispositivo reinicie e execute o programa contido no arquivo .bin. Você pode reiniciar manualmente o programa a qualquer momento pressionando o botão de reinicialização no dispositivo mbed.

1. Conecte-se ao dispositivo usando um aplicativo de cliente SSH, como o PuTTY. Você pode determinar a porta serial que o dispositivo usa verificando o Gerenciador de Dispositivos do Windows.
   
    ![][11]

1. Em PuTTY, clique no tipo de conexão **Serial** . Normalmente, o dispositivo se conecta a 9600 bauds, portanto, insira 9600 na caixa **Velocidade** . Em seguida, clique em **Abrir**.

1. O programa inicia a execução. Talvez seja necessário redefinir a placa (pressione CTRL+Break ou pressione o botão de redefinição da placa) se o programa não iniciar automaticamente quando você se conectar.
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

