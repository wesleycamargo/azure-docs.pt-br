> [AZURE.SELECTOR]
- [C on Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C on Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C on mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## Visão geral do cenário

Nesse cenário, você criará um dispositivo que envia a telemetria a seguir para a [solução pré-configurada][lnk-what-are-preconfig-solutions] de monitoramento remoto:

- Temperatura externa
- Temperatura interna
- Umidade

Para simplificar, o código no dispositivo gera valores de exemplo, mas é recomendável estender o exemplo conectando sensores reais aos dispositivos e enviando telemetria real.

## Antes de começar

Antes de escrever qualquer código para o seu dispositivo, você deve provisionar sua solução pré-configurada de monitoramento remoto e, em seguida, um dispositivo na solução.

### Provisionar sua solução pré-configurada de monitoramento remoto

O dispositivo que você criar enviará dados a uma instância da solução pré-configurada de [monitoramento remoto][lnk-remote-monitoring]. Visite [Introdução ao Azure IoT Suite][lnk-getstarted] para criar uma conta do Azure e provisionar IoT Suite. Selecione **Monitoramento remoto** ao criar a nova solução.

Quando a solução de monitoramento remoto tiver sido configurada, clique em **Iniciar** para abrir o painel de solução.

![][img-dashboard]

### Provisionar o dispositivo na solução de monitoramento remoto

> [AZURE.NOTE]Se você já configurou um dispositivo em sua solução, poderá ignorar esta etapa. Você precisará conhecer as credenciais do dispositivo quando criar o aplicativo cliente.

Para um dispositivo conectar-se à solução pré-configurada, ele deve ser capaz de identificar-se usando credenciais válidas. Você pode obter as credenciais do dispositivo no painel de solução e, em seguida, incluí-las em seu aplicativo cliente.

Para adicionar um novo dispositivo à sua solução de monitoramento remoto, conclua as etapas a seguir no painel de solução:

1.  No canto inferior esquerdo do painel, clique em **Adicionar um dispositivo**.

    ![][1]

2.  No painel **Dispositivo personalizado**, clique em **Adicionar novo**.

    ![][2]

3.  Escolha **Permita-me definir meu próprio ID de dispositivo**, insira um ID de dispositivo como **mydevice**, clique em **Verificar ID** para verificar se esse nome não está em uso e, em seguida, clique em **Criar** para provisionar o dispositivo.

    ![][3]

5. Anote as credenciais de dispositivo (ID do dispositivo, nome de host do Hub IoT e chave do dispositivo), o aplicativo cliente precisará delas para conectar o dispositivo à solução de monitoramento remoto. Em seguida, clique em **Concluído**.

    ![][4]

6. Verifique se o dispositivo é exibido corretamente na seção de dispositivos. O status é **Pendente** até que o dispositivo estabeleça uma conexão com a solução de monitoramento remoto.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-getstarted]: http://www.microsoft.com/server-cloud/internet-of-things/getting-started.aspx
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md

<!---HONumber=Nov15_HO4-->