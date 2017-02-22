## <a name="view-device-telemetry-in-the-dashboard"></a>Exibir telemetria do dispositivo no painel
O painel da solução de monitoramento remoto permite que você exiba a telemetria que seu dispositivo envia para o Hub IoT.

1. Em seu navegador, retorne para o painel da solução de monitoramento remoto, clique em **Dispositivos** no painel esquerdo para navegar até a **Lista de dispositivos**.
2. Na **Lista de dispositivos**, você deve ver que o status de seu dispositivo agora é **Em execução**.
   
    ![][18]
3. Clique em **Painel** para voltar ao painel, selecione seu dispositivo no menu suspenso **Dispositivo para Exibição** para exibir sua telemetria. A telemetria do aplicativo de exemplo é de 50 unidades de temperatura interna, 55 unidades de temperatura externa e 50 unidades de umidade. Observe que, por padrão, o painel exibe apenas os valores de temperatura e umidade.
   
    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Enviar um comando para o dispositivo
O painel da solução de monitoramento remoto permite que você envie comandos para seus dispositivos através do Hub IoT. Por exemplo, na solução de monitoramento remoto, você pode enviar um comando para definir a temperatura interna de um dispositivo.

1. No painel da solução de monitoramento remoto, clique em **Dispositivos** no painel esquerdo para navegar até a **Lista de dispositivos**.
2. Clique em **ID de dispositivo** para seu dispositivo na **Lista de dispositivos**.
3. No painel **Detalhes de dispositivo**, clique em **Comandos**.
   
    ![][13]
4. Na lista suspensa **Comando**, selecione **Definir temperatura** e, em seguida, em **Temperatura**, insira um novo valor de temperatura. Clique em **Enviar comando** para enviar o comando para o dispositivo.
   
    ![][14]
   
   > [!NOTE]
   > O histórico de comandos inicialmente mostra o status de comando como **Pendente**. Quando o dispositivo reconhece o comando, o status é alterado para **Sucesso**.
   > 
   > 
5. No painel, verifique se o dispositivo está agora enviando 75 como o novo valor de temperatura.

## <a name="next-steps"></a>Próximas etapas
O artigo [Personalizando soluções pré-configuradas][lnk-customize] descreve algumas maneiras de estender este exemplo. Extensões possíveis incluem usar sensores reais e implementar comandos adicionais.

Saiba mais sobre as [permissões no site azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md


<!--HONumber=Nov16_HO3-->


