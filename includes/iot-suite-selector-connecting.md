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

Para concluir este tutorial, você precisa de uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Antes de começar

Antes de gravar qualquer código para o dispositivo, implante a solução pré-configurada de monitoramento remoto e adicione um novo dispositivo físico à solução.

### <a name="deploy-your-remote-monitoring-preconfigured-solution"></a>Implantar a solução pré-configurada de monitoramento remoto

O dispositivo **Resfriador** criado neste tutorial envia dados a uma instância da solução de [monitoramento remoto](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) pré-configurada. Se você ainda não tiver provisionado a solução de monitoramento remoto pré-configurada na sua conta do Azure, consulte [Implantar a solução de monitoramento remoto pré-configurada](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

Quando o processo de implantação para a solução de monitoramento remoto for concluído, clique em **Inicialização** para abrir o painel da solução em seu navegador.

![O painel da solução](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Adicionar o dispositivo à solução de monitoramento remoto

> [!NOTE]
> Se você já adicionou um dispositivo em sua solução, poderá ignorar esta etapa. No entanto, a próxima etapa exige a cadeia de conexão do dispositivo. É possível recuperar a cadeia de conexão de um dispositivo a partir do [Portal do Azure](https://portal.azure.com) ou usando uma ferramente da CLI [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest).

Para um dispositivo conectar-se à solução pré-configurada, ele deve identificar-se no Hub IoT usando credenciais válidas. É possível salvar a cadeia de conexão do dispositivo contendo essas credenciais, quando você adiciona o dispositivo à solução. Você incluirá a cadeia de conexão de dispositivo no seu aplicativo cliente, mais adiante neste tutorial.

Para adicionar um dispositivo à sua solução de monitoramento remoto, conclua as seguintes etapas na página **Dispositivos** na solução:

1. Escolha **+ Novo dispositivo** e, em seguida, escolha **Físico** como o **Tipo de dispositivo**:

    ![Adicionar um dispositivo físico](media/iot-suite-selector-connecting/devicesprovision.png)

1. Insira **Physical-chiller** como a ID do dispositivo. Escolha as opções **Chave Simétrica** e **Gerar chaves automaticamente**:

    ![Escolher as opções do dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

1. Escolha **Aplicar**. Em seguida, anote os valores **ID do dispositivo**, **Chave Primária** e **Cadeia de conexão – Chave primária**:

    ![Recuperar credenciais](media/iot-suite-selector-connecting/credentials.png)

Você adicionou um dispositivo físico à solução pré-configurada de monitoramento remoto e observou a cadeia de conexão do dispositivo. Nas seções a seguir, você implementará o aplicativo cliente que usa a cadeia de conexão do dispositivo para conectar-se à solução.

O aplicativo cliente implementa o modelo de dispositivo **Resfriador** interno. Um modelo de dispositivo da solução pré-configurada especifica o seguinte sobre o dispositivo:

* As propriedades que o dispositivo relata à solução. Por exemplo, um dispositivo **Resfriador** relata informações sobre seu firmware e sua localização.
* Os tipos de telemetria que o dispositivo envia para a solução. Por exemplo, um dispositivo **Resfriador** envia valores de temperatura, umidade e pressão.
* Os métodos que você pode agendar na solução para serem executados no dispositivo. Por exemplo, um dispositivo **Resfriador** deve implementar os métodos **Reiniciar**, **FirmwareUpdate**, **EmergencyValveRelease** e **IncreasePressure**.