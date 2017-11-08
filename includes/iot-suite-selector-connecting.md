> [!div class="op_single_selector"]
> * [C em Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C em Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (genérico)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js no Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C no Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-c.md)

Neste tutorial, você implementará um dispositivo **Resfriador** que envia a telemetria a seguir para a [solução pré-configurada](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md) de monitoramento remoto:

* Temperatura
* Pressão
* Umidade

Para simplificar, o código gera valores de telemetria de exemplo para o **Resfriador**. Você pode estender o exemplo conectando sensores reais ao dispositivo e enviando telemetria real.

O dispositivo de exemplo também:

* Envia os metadados à solução para descrever seus recursos.
* Responde às ações disparadas da página **Dispositivos** na solução.
* Responde às alterações de configuração da página **Dispositivos** na solução.

Para concluir este tutorial, você precisa de uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Antes de começar

Antes de escrever qualquer código para o seu dispositivo, você precisa provisionar a solução pré-configurada de monitoramento remoto e provisionar um novo dispositivo personalizado nessa solução.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Provisionar sua solução pré-configurada de monitoramento remoto

O dispositivo **Resfriador** criado neste tutorial envia dados a uma instância da solução de [monitoramento remoto](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) pré-configurada. Se você ainda não tiver provisionado a solução de monitoramento remoto pré-configurada na sua conta do Azure, consulte [Implantar a solução de monitoramento remoto pré-configurada](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

Quando o processo de provisionamento para a solução de monitoramento remoto for concluído, clique em **Iniciar** para abrir o painel da solução em seu navegador.

![O painel da solução](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Provisionar o dispositivo na solução de monitoramento remoto

> [!NOTE]
> Se você já configurou um dispositivo em sua solução, poderá ignorar esta etapa. Você precisará das credenciais do dispositivo ao criar o aplicativo cliente.

Para um dispositivo conectar-se à solução pré-configurada, ele deve identificar-se no Hub IoT usando credenciais válidas. Você pode recuperar as credenciais do dispositivo na página **Dispositivos** da solução. Você pode incluir as credenciais do dispositivo em seu aplicativo cliente posteriormente neste tutorial.

Para adicionar um dispositivo à sua solução de monitoramento remoto, conclua as seguintes etapas na página **Dispositivos** na solução:

1. Escolha **Provisionar** e, em seguida, escolha **Físico** como o **Tipo de dispositivo**:

    ![Provisionar um dispositivo físico](media/iot-suite-selector-connecting/devicesprovision.png)

1. Insira **Physical-chiller** como a ID do dispositivo. Escolha as opções **Chave Simétrica** e **Gerar chaves automaticamente**:

    ![Escolher as opções do dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

Para localizar as credenciais que o dispositivo deverá usar para conectar-se à solução pré-configurada, navegue até o portal do Azure no navegador. Entre em sua assinatura.

1. Localize o grupo de recursos que contém os serviços do Azure que a solução de monitoramento remoto usa. O grupo de recursos tem o mesmo nome que a solução de monitoramento remoto provisionada.

1. Navegue até o Hub IoT neste grupo de recursos. Em seguida, escolha **Device Explorer**:

    ![Gerenciador de dispositivos](media/iot-suite-selector-connecting/deviceexplorer.png)

1. Escolha a **ID do dispositivo** criada na página **Dispositivos** na solução de monitoramento remoto.

1. Anote os valores da **ID do dispositivo** e da **Chave primária**. Você usará esses valores ao adicionar o código para conectar o dispositivo à solução.

Você acabou de provisionar um dispositivo físico na solução de monitoramento remoto pré-configurada. Nas seções a seguir, você implementará o aplicativo cliente que usa as credenciais do dispositivo para conectar-se à solução.

O aplicativo cliente implementa o modelo de dispositivo **Resfriador** interno. Um modelo de dispositivo da solução pré-configurada especifica o seguinte sobre o dispositivo:

* As propriedades que o dispositivo relata à solução. Por exemplo, um dispositivo **Resfriador** relata informações sobre seu firmware e sua localização.
* Os tipos de telemetria que o dispositivo envia para a solução. Por exemplo, um dispositivo **Resfriador** envia valores de temperatura, umidade e pressão.
* Os métodos que você pode agendar na solução para serem executados no dispositivo. Por exemplo, um dispositivo **Resfriador** deve implementar os métodos **Reiniciar**, **FirmwareUpdate**, **EmergencyValveRelease** e **IncreasePressuree**.