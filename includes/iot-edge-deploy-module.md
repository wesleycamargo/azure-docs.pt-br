Um dos principais recursos do Azure IoT Edge é a possibilidade de implantar módulos em seus dispositivos IoT Edge na nuvem. Um módulo IoT Edge é um pacote executável implementado como contêiner. Nesta seção, você implanta um módulo que gera uma telemetria para seu dispositivo simulado. 

1. No portal do Azure, navegue para o hub IoT.
1. Vá para **IoT Edge (versão prévia)** e selecione o dispositivo IoT Edge.
1. Selecione **Definir módulos**.
1. Selecione **Adicionar Módulo IoT Edge**.
1. No campo **Nome**, insira `tempSensor`. 
1. No campo **URI da Imagem**, insira `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Deixe as outras configurações inalteradas e selecione **Salvar**.
1. De volta à etapa **Adicionar módulos**, clique em **Avançar**.
1. Na etapa **Especificar rotas**, selecione **Avançar**.
1. Na etapa **Revisar modelo**, selecione **Enviar**.
1. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Você deverá ver o novo módulo tempSensor em execução junto com o IoT Edge. 

   ![Exibir o TempSensor na lista de módulos implantados][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png