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
ms.date: 05/22/2017
ms.author: dobett
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: ef7b78f85a787f8fbe22c0e26aa34f0cd1685d58
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017

---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (mbed)

## <a name="scenario-overview"></a>Visão geral do cenário
Nesse cenário, você cria um dispositivo que envia a telemetria a seguir para a [solução pré-configurada][lnk-what-are-preconfig-solutions] de monitoramento remoto:

* Temperatura externa
* Temperatura interna
* Umidade

Para simplificar, o código no dispositivo gera valores de exemplo, mas é recomendável estender o exemplo conectando sensores reais aos dispositivos e enviando telemetria real.

O dispositivo também é capaz de responder aos métodos invocados no painel da solução e aos conjuntos de valores de propriedade desejada no painel da solução.

Para concluir este tutorial, você precisa de uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

## <a name="before-you-start"></a>Antes de começar
Antes de escrever qualquer código para o seu dispositivo, você precisa provisionar a solução pré-configurada de monitoramento remoto e provisionar um novo dispositivo personalizado nessa solução.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Provisionar sua solução pré-configurada de monitoramento remoto
O dispositivo que você criar neste tutorial enviará dados a uma instância da solução pré-configurada de [monitoramento remoto][lnk-remote-monitoring]. Se você ainda não provisionou a solução pré-configurada de monitoramento remoto em sua conta do Azure, use as seguintes etapas:

1. Na página <https://www.azureiotsuite.com/>, clique em **+** para criar uma solução.
2. Clique em **Selecionar** no painel **Monitoramento remoto** para criar a solução.
3. Na página **Criar solução de Monitoramento remoto**, insira um **Nome de solução** de sua escolha, selecione a **Região** na qual você deseja implantar e selecione a assinatura do Azure a ser usada. Clique em **Criar solução**.
4. Aguarde até que o processo de provisionamento seja concluído.

> [!WARNING]
> As soluções pré-configuradas usam serviços faturáveis do Azure. Certifique-se de remover a solução pré-configurada da sua assinatura quando tiver terminado com ela para evitar encargos desnecessários. Você pode remover completamente uma solução pré-configurada de sua assinatura visitando a página <https://www.azureiotsuite.com/>.
> 
> 

Quando o processo de provisionamento para a solução de monitoramento remoto for concluído, clique em **Iniciar** para abrir o painel da solução em seu navegador.

![Painel da solução][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Provisionar o dispositivo na solução de monitoramento remoto
> [!NOTE]
> Se você já configurou um dispositivo em sua solução, poderá ignorar esta etapa. Você precisa saber as credenciais do dispositivo ao criar o aplicativo cliente.
> 
> 

Para um dispositivo conectar-se à solução pré-configurada, ele deve identificar-se no Hub IoT usando credenciais válidas. Você pode recuperar as credenciais do dispositivo no painel da solução. Você pode incluir as credenciais do dispositivo em seu aplicativo cliente posteriormente neste tutorial.

Para adicionar um dispositivo à sua solução de monitoramento remoto, realize as etapas a seguir no painel da solução:

1. No canto inferior esquerdo do painel, clique em **Adicionar um dispositivo**.
   
   ![Adicionar um dispositivo][1]
2. No painel **Dispositivo Personalizado**, clique em **Adicionar novo**.
   
   ![Adicionar um dispositivo personalizado][2]
3. Escolha **Deixe-me definir minha própria ID de Dispositivo**. Insira uma ID do Dispositivo como **mydevice**, clique em **Verificar ID** para verificar se esse nome ainda não está em uso e, em seguida, clique em **Criar** para provisionar o dispositivo.
   
   ![Adicionar ID do dispositivo][3]
4. Anote as credenciais do dispositivo (ID do dispositivo, nome do host do Hub IoT e Chave do Dispositivo). Seu aplicativo cliente precisa desses valores para se conectar à solução de monitoramento remoto. Em seguida, clique em **Concluído**.
   
    ![Exibir credenciais do dispositivo][4]
5. Selecione seu dispositivo na lista de dispositivos do painel da solução. Em seguida, no painel **Detalhes do Dispositivo**, clique em **Habilitar Dispositivo**. O status do seu dispositivo agora é **Executando**. Agora a solução de monitoramento remoto poderá receber telemetria do seu dispositivo e invocar métodos nele.

[img-dashboard]: ./media/iot-suite-connecting-devices-mbed/dashboard.png
[1]: ./media/iot-suite-connecting-devices-mbed/suite0.png
[2]: ./media/iot-suite-connecting-devices-mbed/suite1.png
[3]: ./media/iot-suite-connecting-devices-mbed/suite2.png
[4]: ./media/iot-suite-connecting-devices-mbed/suite3.png

[lnk-what-are-preconfig-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

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

