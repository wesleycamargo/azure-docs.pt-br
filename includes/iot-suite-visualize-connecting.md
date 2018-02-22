## <a name="view-device-telemetry"></a>Exibir telemetria de dispositivo

Você pode exibir a telemetria enviada do dispositivo na página **Dispositivos** na solução.

1. Selecione o dispositivo que você provisionou na lista de dispositivos na página **Dispositivos**. Um painel exibe informações sobre seu dispositivo, incluindo um gráfico de telemetria do dispositivo:

    ![Consultar detalhes do dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Escolha **Pressão** para alterar a exibição de telemetria:

    ![Exibir telemetria de pressão](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para exibir informações de diagnóstico sobre o dispositivo, role para baixo até **Diagnóstico**:

    ![Exibir diagnóstico do dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir no dispositivo

Para invocar métodos em seus dispositivos, use a página **Dispositivos** na solução de monitoramento remoto. Por exemplo, na solução de monitoramento remoto, os dispositivos **Chiller** implementam um método **FirmwareUpdate**.

1. Escolha **Dispositivos** para navegar até a página **Dispositivos** na solução.

1. Selecione o dispositivo que você provisionou na lista de dispositivos da página **Dispositivos**:

    ![Selecionar o dispositivo físico](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para exibir uma lista dos métodos que você pode chamar em seu dispositivo, escolha **Agendar**. Para agendar um método a ser executado em vários dispositivos, você pode selecionar vários dispositivos na lista. O painel **Agendar** mostra os tipos de método comuns a todos os dispositivos selecionados.

1. Escolha **FirmwareUpdate**, defina o nome do trabalho para **UpdatePhysicalChiller**. Defina **Firmware Version** para **2.0.0**, defina **Firmware URI** para **http://contoso.com/updates/firmware.bin** e, em seguida, escolha **Aplicar**:

    ![Agendar a atualização do firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Uma sequência de mensagens é exibida no console executando o código de dispositivo, enquanto o dispositivo simulado manipula o método.

1. Quando a atualização estiver concluída, a nova versão do firmware é exibida na página **Dispositivos**:

    ![Atualização concluída](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Para acompanhar o status do trabalho na solução, escolha **Exibir**.

## <a name="next-steps"></a>Próximas etapas

O artigo [Personalizar a solução de monitoramento remoto pré-configurada](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) descreve algumas maneiras de personalizar a solução pré-configurada.