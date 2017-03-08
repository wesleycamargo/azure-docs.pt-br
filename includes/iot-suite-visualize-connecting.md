## <a name="view-device-telemetry-in-the-dashboard"></a>Exibir telemetria do dispositivo no painel
O painel da solução de monitoramento remoto permite que você exiba a telemetria que seus dispositivos enviam para o Hub IoT.

1. Em seu navegador, retorne para o painel da solução de monitoramento remoto, clique em **Dispositivos** no painel esquerdo para navegar até a **Lista de dispositivos**.
2. Na **Lista de dispositivos**, você deve ver que o status de seu dispositivo agora é **Executando**. Caso contrário, clique em **Habilitar Dispositivo** no painel de **Detalhes do Dispositivo**.
   
    ![Exibir status do dispositivo][18]
3. Clique em **Painel** para voltar ao painel, selecione seu dispositivo no menu suspenso **Dispositivo para Exibição** para exibir sua telemetria. A telemetria do aplicativo de exemplo é de 50 unidades de temperatura interna, 55 unidades de temperatura externa e 50 unidades de umidade.
   
    ![Exibir telemetria de dispositivo][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Invocar um método no seu dispositivo
O painel da solução de monitoramento remoto permite que você invoque métodos nos seus dispositivos através do Hub IoT. Por exemplo, na solução de monitoramento remoto, você pode invocar um método para simular a reinicialização de um dispositivo.

1. No painel da solução de monitoramento remoto, clique em **Dispositivos** no painel esquerdo para navegar até a **Lista de dispositivos**.
2. Clique em **ID de dispositivo** para seu dispositivo na **Lista de dispositivos**.
3. No painel **Detalhes do dispositivo**, clique em **Métodos**.
   
    ![Métodos do dispositivo][13]
4. Na lista suspensa **Método**, selecione **InitiateFirmwareUpdate** e, em seguida, insira uma URL fictícia em **FWPACKAGEURI**. Clique em **Invocar Método** para chamar o método no dispositivo.
   
    ![Invocar um método do dispositivo][14]
   

5. Você verá uma mensagem no console que está executando o código do dispositivo quando o dispositivo manipular o método. Os resultados do método serão adicionados ao histórico no portal da solução:

    ![Exibir o histórico do método][img-method-history]

## <a name="next-steps"></a>Próximas etapas
O artigo [Personalizando soluções pré-configuradas][lnk-customize] descreve algumas maneiras de estender este exemplo. Extensões possíveis incluem usar sensores reais e implementar comandos adicionais.

Você pode aprender mais sobre as [permissões no site azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
