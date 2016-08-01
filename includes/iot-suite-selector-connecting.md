> [AZURE.SELECTOR]
- [C em Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C em Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C em mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## Visão geral do cenário

Nesse cenário, você cria um dispositivo que envia a telemetria a seguir para a [solução pré-configurada][lnk-what-are-preconfig-solutions] de monitoramento remoto:

- Temperatura externa
- Temperatura interna
- Umidade

Para simplificar, o código no dispositivo gera valores de exemplo, mas é recomendável estender o exemplo conectando sensores reais aos dispositivos e enviando telemetria real.

Para concluir este tutorial, você precisa de uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

## Antes de começar

Antes de escrever qualquer código para o seu dispositivo, você precisa provisionar sua solução pré-configurada de monitoramento remoto e, em seguida, um novo dispositivo personalizado na solução.

### Provisionar sua solução pré-configurada de monitoramento remoto

O dispositivo que você criar neste tutorial enviará dados a uma instância da solução pré-configurada de [monitoramento remoto][lnk-remote-monitoring]. Se você já não tiver provisionado a solução pré-configurada de monitoramento remoto em sua conta do Azure, siga as etapas abaixo:

1. Na página <https://www.azureiotsuite.com/>, clique em **+** para criar uma nova solução.

2. Clique em **Selecionar** no painel **Monitoramento remoto** para criar a nova solução.

3. Na página **Criar solução de "Monitoramento remoto"**, insira um **Nome da solução** de sua escolha, selecione a **Região** em que deseja implantar e selecione a assinatura do Azure a ser usada. Clique em **Criar solução**.

4. Aguarde até que o processo de provisionamento seja concluído.

> [AZURE.WARNING] As soluções pré-configuradas usam serviços faturáveis do Azure. Certifique-se de remover a solução pré-configurada da sua assinatura quando tiver terminado com ela para evitar encargos desnecessários. Você pode remover completamente uma solução pré-configurada da sua assinatura visitando a página <https://www.azureiotsuite.com/>.

Quando o processo de provisionamento para a solução de monitoramento remoto for concluído, clique em **Iniciar** para abrir o painel da solução em seu navegador.

![][img-dashboard]

### Provisionar o dispositivo na solução de monitoramento remoto

> [AZURE.NOTE] Se você já configurou um dispositivo em sua solução, poderá ignorar esta etapa. Você precisará conhecer as credenciais do dispositivo quando criar o aplicativo cliente.

Para um dispositivo conectar-se à solução pré-configurada, ele deve identificar-se no Hub IoT usando credenciais válidas. Você pode recuperar as credenciais do dispositivo no painel da solução. Você pode incluir as credenciais do dispositivo em seu aplicativo cliente posteriormente neste tutorial.

Para adicionar um novo dispositivo à sua solução de monitoramento remoto, conclua as etapas a seguir no painel de solução:

1.  No canto inferior esquerdo do painel, clique em **Adicionar um dispositivo**.

    ![][1]

2.  No painel **Dispositivo personalizado**, clique em **Adicionar novo**.

    ![][2]

3.  Escolha **Permita-me definir minha própria ID de dispositivo**, insira uma ID de dispositivo como **mydevice**, clique em **Verificar ID** para verificar se esse nome ainda não está em uso e, em seguida, clique em **Criar** para provisionar o dispositivo.

    ![][3]

5. Anote as credenciais de dispositivo (ID do dispositivo, nome de host do Hub IoT e chave do dispositivo), o aplicativo cliente precisa delas para conectar o dispositivo à solução de monitoramento remoto. Em seguida, clique em **Concluído**.

    ![][4]

6. Verifique se o dispositivo é exibido na seção de dispositivos. O status do dispositivo é **Pendente** até que o dispositivo estabeleça uma conexão com a solução de monitoramento remoto.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0720_2016-->