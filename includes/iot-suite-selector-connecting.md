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

Para concluir este tutorial, você precisará de uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

## Antes de começar

Antes de escrever qualquer código para o seu dispositivo, você precisa provisionar sua solução pré-configurada de monitoramento remoto e, em seguida, um dispositivo na solução.

### Provisionar sua solução pré-configurada de monitoramento remoto

O dispositivo que você criar enviará dados a uma instância da solução pré-configurada de [monitoramento remoto][lnk-remote-monitoring]. Visite [Introdução ao Azure IoT Suite][lnk-getstarted] para provisionar a solução pré-configurada de monitoramento em sua conta do Azure:

1. Na página https://www.azureiotsuite.com/, clique em **+** para criar uma nova solução.

2. Clique em **Selecionar** no painel **Monitoramento remoto** para criar a nova solução.

3. Na página **Criar solução de "Monitoramento remoto"**, insira um **Nome da solução** de sua escolha, selecione a **Região** em que deseja implantar e selecione a assinatura do Azure a ser usada. Clique em **Criar solução**.

4. Aguarde até que o processo de provisionamento seja concluído.

> [AZURE.WARNING] As soluções pré-configuradas usam serviços faturáveis do Azure. Certifique-se de remover a solução pré-configurada da sua assinatura quando tiver terminado com ela para evitar encargos desnecessários. Você pode remover completamente uma solução pré-configurada da sua assinatura visitando a página https://www.azureiotsuite.com/.

Quando a solução de monitoramento remoto tiver sido configurada, clique em **Iniciar** para abrir o painel de solução em seu navegador.

![][img-dashboard]

### Provisionar o dispositivo na solução de monitoramento remoto

> [AZURE.NOTE] Se você já configurou um dispositivo em sua solução, poderá ignorar esta etapa. Você precisará conhecer as credenciais do dispositivo quando criar o aplicativo cliente.

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

[lnk-getstarted]: https://www.azureiotsuite.com/
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0211_2016-->