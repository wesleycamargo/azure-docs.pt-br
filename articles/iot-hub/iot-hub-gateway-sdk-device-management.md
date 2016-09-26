<properties
	pageTitle="Gerenciamento de dispositivos com o SDK do Gateway | Microsoft Azure"
	description="Passo a passo do SDK do Gateway do Hub IoT do Azure mostrando como implementar o gerenciamento de dispositivos quando você está usando o SDK do Gateway"
	services="iot-hub"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>


# SDK do Gateway IoT (beta) – gerenciamento de dispositivos com o SDK do Gateway

Este tutorial mostra como usar os recursos do [gerenciamento de dispositivos][lnk-device-management] do Hub IoT juntamente com o [SDK do Gateway Hub IoT do Azure][lnk-gateway-sdk]. O tutorial usa um Módulo de Computação Intel Edison para hospedar um gateway com dispositivos simulados que envia a telemetria para o hub IoT. Você usa os recursos do gerenciamento de dispositivos no Hub IoT para executar uma atualização de firmware remota na placa Edison.

Este tutorial abrange:

- **Arquitetura**: informações importantes da arquitetura sobre o exemplo de gerenciamento de dispositivos.

- **Criar e executar**: as etapas necessárias para criar e executar a amostra.

## Arquitetura

Neste tutorial, você provisiona uma placa Intel Edison para atuar como um gateway do dispositivo IoT conectado ao Hub IoT. Também é possível configurar a placa Edison para que você possa gerenciá-la por meio do Hub IoT. O diagrama a seguir mostra os principais elementos da solução que você cria neste tutorial:

![Arquitetura do gerenciamento de dispositivos e do gateway][img-architecture]

### Dispositivos

Há três dispositivos IoT conectados ao Hub IoT nesta solução:

- A placa Edison é um dispositivo denominado **GW-device**. No tutorial, você usa os recursos do gerenciamento de dispositivos do Hub IoT para atualizar o firmware do dispositivo físico.

- Dois dispositivos simulados (**GW-ble1-demo** e **GW-ble2-demo**). Esses dispositivos simulam os dispositivos Bluetooth de baixo consumo de energia que se conectam ao Hub IoT através do gateway e enviam a telemetria da temperatura para o hub.

### Software do gateway

O software do gateway é executado como um serviço na placa Edison. Dois dispositivos simulados geram a telemetria da temperatura. O módulo de mapeamento mapeia esses dispositivos simulados para os dispositivos registrados no Hub IoT e o módulo HTTP lida com a comunicação com o ponto de extremidade do Hub IoT. O artigo [SDK do Gateway IoT – enviar mensagens do dispositivo para a nuvem com um dispositivo simulado][lnk-gateway-scenario] descreve esse cenário detalhadamente.

### Cliente de gerenciamento de dispositivos

O [cliente de gerenciamento de dispositivos][lnk-device-management] é executado como um serviço na placa Edison. Isso permite que você execute trabalhos remotos na placa Edison, tais como, [atualizações de firmware][lnk-dm-jobs], reinicializações e atualizações da configuração, assim como uma consulta das propriedades do dispositivo. Neste tutorial, o cliente de gerenciamento de dispositivos recebe e processa uma solicitação para executar uma atualização de firmware na placa Edison.

### Hub IoT

O [Hub IoT do Azure][lnk-iot-hub] é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end da solução. Neste tutorial, o Hub IoT:

- Permite que os três dispositivos conectem a nuvem.
- Recebe a telemetria gerada pelos dispositivos simulados a partir do gateway.
- Permite que você envie uma solicitação de atualização do firmware para a placa Edison.
- Monitora o andamento do trabalho de atualização do firmware.

### IU de exemplo do gerenciamento de dispositivos

A [IU de exemplo do gerenciamento de dispositivos][lnk-dm-sample-ui] é um aplicativo Web de exemplo que permite a você usar os recursos do gerenciamento de dispositivos do Hub IoT em uma IU da Web interativa. Por exemplo, você pode usar a IU de exemplo para consultar os dispositivos conectados ao seu Hub IoT e enviar trabalhos de atualização do firmware para seus dispositivos. Neste tutorial, você pode usar a IU de exemplo para enviar um trabalho de atualização do firmware para a placa Edison e monitorar o andamento do trabalho até o término.

## Criar e executar

Para executar esse exemplo, você deve criar uma imagem personalizada para sua placa Edison que inclui o software do gateway do Hub IoT e o cliente de gerenciamento de dispositivos.

Antes de começar, você deve verificar se pode conectar sua placa Edison à rede sem fio. Para configurar sua placa Edison, você precisa conectá-la a um computador host. Posteriormente, você usará o computador host para exibir sua placa Edison com a imagem personalizada criada. A Intel tem um conjunto de guias de início rápido para os seguintes sistemas operacionais:

- [Introdução à Placa de Desenvolvimento Intel Edison no Windows com 64 bits][lnk-setup-win64].
- [Introdução à Placa de Desenvolvimento Intel Edison no Windows com 32 bits][lnk-setup-win32].
- [Introdução à Placa Intel® Edison no Linux][lnk-setup-linux].

Para configurar sua placa Edison e familiarize-se com ela, você deve concluir todas as etapas nesses artigos de "Introdução", exceto para:

- Exibir o firmware mais recente. Você atualiza o firmware como parte deste tutorial, portanto, não é necessário concluir essa etapa no momento.
- A última etapa, "Escolher IDE", é desnecessária para o tutorial atual.

Quando você configurar a placa Edison e instalar os drivers necessários em sua máquina host, deverá assegurar que pode conectar a placa Edison usando um terminal serial. A página [Configurando um terminal serial][lnk-serial-connection] no site da Intel tem links para configurar as instruções para os sistemas operacionais de host, como o Windows e o Linux.

Você também precisa concluir estas tarefas

- [Criar um hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisa do nome de seu hub para concluir este tutorial. Se você ainda não tiver uma assinatura do Azure, é possível obter uma [conta gratuita][lnk-free-trial].
- Adicione três dispositivos (**GW-ble1-demo**, **GW-ble2-demo** e **GW-device**) ao seu hub IoT e anote suas ids e chaves do dispositivo. É possível usar as ferramentas [Gerenciador de Dispositivos ou iothub-explorer][lnk-explorer-tools] para adicionar esses dispositivos ao hub IoT criado na etapa anterior e recuperar suas chaves. Você usa dois desses dispositivos (**GW-ble1-demo** e **GW-ble2-demo**) como dispositivos BLE simulados conectados ao gateway e um dispositivo (**GW-device**) para identificar o dispositivo do gateway Edison como um cliente de gerenciamento de dispositivos que você pode gerenciar a partir de seu Hub IoT.

### Preparar o ambiente de criação e verificar se é possível criar uma imagem personalizada

Para criar uma imagem personalizada para sua placa Edison, você precisa de um ambiente Linux. Essas etapas pressupõem que você estivá usando o Ubuntu 14.04 que, no momento da criação deste texto, é a plataforma recomendada a usar. Você deve ter, pelo menos, 40 GB livres na partição base.

> [AZURE.NOTE] O script que cria a imagem personalizada pode levar até 6 horas para ser executado em uma máquina com 4 núcleos na CPU. Você pode reduzir esse tempo usando uma máquina mais potente com mais núcleos na CPU.

Para as etapas nesta seção, consultamos os seguintes artigos: [Pacote de Suporte da Placa Intel Edison][lnk-inteledison-bsp], [Criando Manualmente Imagens Yocto para a Placa Edison Intel a partir da Origem][lnk-hackgnar] e [Criando um Kernel do Linux Personalizado para a Edison (versão 2.1)][lnk-shawnhymel].

1. Entre em sua máquina Ubuntu 14.04 e execute o comando a seguir em sua pasta base para baixar o pacote de origem Edison:
    
    ```
    curl -O http://downloadmirror.intel.com/25028/eng/edison-src-ww25.5-15.tgz
    ```

2. Descompacte o pacote de origem para criar uma pasta **edison-src** na pasta base com o seguinte comando e navegue até a nova pasta **edison-src**:
    
    ```
    tar xfvz edison-src-ww25.5-15.tgz
    cd edison-src/
    ```

3. Instale os pacotes de pré-requisito:
    
    ```
    sudo apt-get -y install build-essential git diffstat gawk chrpath texinfo libtool gcc-multilib libsdl1.2-dev u-boot-tools
    ```

4. Crie dois novos diretórios na pasta **edison-src**:
    
    ```
    mkdir bitbake_download_dir
    mkdir bitbake_sstate_dir 
    ```

5. Execute o seguinte script para fazer o download de seu ambiente de compilação. Se você tiver mais de quatro núcleos na CPU, defina os argumentos de script **--parallel\_make** e **--bb\_number\_thread** para o número de núcleos disponíveis:
    
    ```
    ./meta-intel-edison/setup.sh --dl_dir=bitbake_download_dir  --sstate_dir=bitbake_sstate_dir 
    ```

6. Atualize a localização do repositório Paho git. Edite o arquivo **~/edison-src/out/linux64/poky/meta-intel-iot-middleware/recipes-connectivity/paho-mqtt/paho-mqtt\_3.1.bb** e substitua a URL `git://git.eclipse.org/gitroot/paho/org.eclipse.paho.mqtt.c.git/` por `git://github.com/eclipse/paho.mqtt.c.git`.

7. Inicialize o ambiente de compilação com os seguintes comandos:
    
    ```
    cd out/linux64/
    source poky/oe-init-build-env
    ```

8. Use o seguinte comando para criar sua imagem personalizada. Na primeira vez em que você executar esse comando, poderá levar até 6 horas para ser executado em uma máquina com 4 núcleos na CPU. As recompilações subsequentes depois de adicionar suas próprias personalizações serão muito mais rápidas:
    
    ```
    bitbake edison-image
    ```

9. Finalize a compilação executando os seguintes comandos:
    
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Os arquivos necessários para exibir a placa Edison agora estão na pasta **~/edison-src/out/linux64/build/toFlash/**.

### Adicionar o SDK do Gateway à sua imagem personalizada

As etapas nesta seção o guiarão no processo de adicionar o SDK do Gateway à imagem personalizada para que a placa Edison atue como um gateway IoT durante a inicialização. Para este tutorial, o gateway inclui um módulo que simula dois dispositivos Bluetooth de baixo consumo de energia (BLE) que geram a telemetria da temperatura do gateway para encaminhar para o hub IoT.

Conclua as etapas a seguir na mesma máquina Ubuntu 14.04 usada para criar uma imagem Edison na seção anterior.

1. Clone o SDK do Gateway na pasta base:
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-gateway-sdk.git --recursive
    ```

2. Configure o gateway para conectar seu Hub IoT e simular dois dispositivos. Edite o arquivo **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_intel\_edison.json** e substitua os espaços reservados **IoTHubName**, **IoTHubSuffix**, **deviceID** e **deviceKey** pelos valores anotados quando você configurou seu Hub IoT. Use os dispositivos **GW-ble1-demo** e **GW-ble2-demo** criados anteriormente.

3. Crie uma pasta para conter a nova receita:
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk
    ```

4. Copie o arquivo de receita denominado **azure-iot-field-gateway-sdk.bb** da pasta **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/** para a pasta **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk/** recém-criada. Edite o arquivo para substituir as duas ocorrências de `<<userName>>` por seu nome de usuário atual.

5. Edite **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** para adicionar uma entrada para sua nova receita. Adicione a seguinte linha ao final do arquivo:
    
    ```
    IMAGE_INSTALL += "azure-iot-field-gateway-sdk"
    ```

6. Agora, você pode testar as alterações executando o comando **bitbake** para criar a nova receita:
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake azure-iot-field-gateway-sdk
    ```

### Adicionar o cliente de gerenciamento de dispositivos do Hub IoT do Azure à sua imagem personalizada

As etapas nesta seção o orientam durante o processo de acréscimo do cliente de gerenciamento de dispositivos do Hub IoT à sua imagem personalizada para que você possa gerenciar o dispositivo do gateway Edison a partir de seu Hub IoT. Para este tutorial, o cliente de gerenciamento de dispositivos inclui o código de exemplo para habilitar uma atualização do firmware no dispositivo do gateway Edison.

Conclua as etapas a seguir na mesma máquina Ubuntu 14.04 usada na seção anterior para adicionar o gateway à sua imagem Edison.

1. Clone a ramificação **dmpreview** do repositório de SDKs do Hub IoT na pasta base:
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-sdks -b dmpreview --recursive
    ```

2. Crie as seguintes pastas para conter sua nova receita:
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files
    ```

3. Copie o arquivo **iotdm-edison-sample.bb** da pasta **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** para a pasta **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample**.

4. Edite o arquivo **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/iotdm-edison-sample.bb** e substitua `-Duse_http:BOOL=OFF` por `-Duse_http:BOOL=ON`.

5. Copie o arquivo **iotdm\_edison\_sample.service** da pasta **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** para a pasta **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files**.

6. Edite o arquivo **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** para adicionar uma entrada para sua nova receita. Adicione a seguinte linha ao final do arquivo:
    
    ```
    IMAGE_INSTALL += "iotdm-edison-sample"
    ```

7. Como o SDK do Gateway e o cliente de gerenciamento de dispositivos compartilham algumas bibliotecas, você precisa editar o arquivo **~/edison-src/out/linux64/poky/meta/classes/sstate.bbclass**. Adicione as seguintes linhas ao final do arquivo. Substitua `<your user>` pelo seu nome de usuário atual:
    
    ```
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/lib/libaziotsharedutil.a"
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/include/azureiot"
    ```

8. Configure o WiFi para iniciar automaticamente na placa Edison editando o arquivo **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-connectivity/wpa\_supplicant/wpa-supplicant/wpa\_supplicant.conf-sane** e adicionando as seguintes linhas ao final do arquivo. Substitua `<your wifi ssid>` e `<your wifi password>` pelos valores corretos de sua rede Wi-Fi:
    
    ```
    network={
      ssid="<your wifi ssid>"
      key_mgmt=WPA-PSK
      pairwise=CCMP TKIP
      group=CCMP TKIP WEP104 WEP40
      eap=TTLS PEAP TLS
      psk="<your wifi password>"
    }
    ```

9. Agora, você pode criar a imagem de sua placa Edison que contém o SDK do Gateway e o cliente de gerenciamento de dispositivos. O comando **bitbake** será executado muito mais rapidamente que antes porque ele só precisa criar a nova receita e adicioná-la à imagem:
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake edison-image
    ```

10. Finalize a compilação executando os seguintes comandos:
  
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Os arquivos necessários para exibir a placa Edison agora estão na pasta **~/edison-src/out/linux64/build/toFlash/**.

### Exibir sua Edison Intel com a imagem personalizada

Agora, é possível exibir sua placa Edison com a imagem personalizada que contém o cliente de gerenciamento de dispositivos do Hub IoT e o software do gateway.

Copie os arquivos da pasta **toFlash** na máquina Ubuntu usada para criar a imagem personalizada para a máquina que conecta sua placa Edison com um cabo USB.

Se você estiver usando um computador Windows para conectar a placa Edison com um cabo USB, deverá executar o script **flashall.bat** para exibir a placa. Se estiver usando um computador Linux para conectar a placa Edison com um cabo USB, deverá executar o script **flashall.bat** para exibir a placa.

Quando o processo de exibição for concluído, conecte a placa Edison usando uma [conexão serial][lnk-serial-connection] em sua máquina host e entre como **raiz**. Você deve verificar se sua placa Edison agora está conectada à rede Wi-Fi.

### Execute o exemplo

Você deve configurar o cliente de gerenciamento de dispositivos na placa Edison para se conectar como o dispositivo **GW-device** ao hub IoT. Use um editor de texto (como **vi** ou **nano**) para criar um arquivo denominado **.cs** na pasta /home/root na placa Edison. Esse arquivo deve conter apenas a cadeia de conexão do **GW-device**. Se você não anotou essa cadeia de conexão anteriormente, poderá usar as ferramentas [Gerenciador de Dispositivos ou iothub-explorer][lnk-explorer-tools] para recuperar a cadeia de conexão a partir do registro do dispositivo do Hub IoT.

Quando você tiver criado o arquivo **.cs**, reinicialize a placa Edison usando o seguinte comando:

```
reboot -h now
```

Quando a placa Edison reinicializar, verifique se os serviços de gerenciamento de dispositivos e de gateway inicializam com um status **OK**:

```
[  OK  ] Started Daemon to receive the wpa_supplicant event.
         Starting PulseAudio Sound System...
[  OK  ] Started WPA supplicant service.
[  OK  ] Started Azure IoT DM as a service..
[  OK  ] Started Azure Iot Gateway as a service..
         Stopping Daemon to receive the wpa_supplicant event...
[  OK  ] Stopped Daemon to receive the wpa_supplicant event.

```

Para solucionar problemas com o serviço de gateway IoT, examine as entradas no arquivo **/deviceCloudUploadGatewaylog.log** na placa Edison. Você também pode usar o seguinte comando para exibir qualquer saída do serviço:

```
systemctl status simulated_device_cloud_upload.service
```

Para solucionar problemas com o serviço de gerenciamento de dispositivos IoT, use o seguinte comando para exibir qualquer saída:

```
systemctl status iotdm_edison_sample.service
```

### Iniciar o trabalho de atualização do firmware

Uma atualização do firmware na placa Edison solicitada pelo serviço de gerenciamento de dispositivos IoT normalmente baixa um arquivo zip que contém o firmware de uma URL. Para simplificar este tutorial, você copiará manualmente o arquivo zip para a placa Edison, em seguida, usará uma URL **file://**, em vez de uma URL **http://** quando solicitar a atualização.

Novamente, para simplificar o tutorial, a atualização do firmware reaplica a mesma imagem do firmware, em vez de usar uma nova imagem. Você poderá ver essa imagem sendo aplicada à placa Edison.

Crie um arquivo zip denominado **edison.zip** que contém todos os arquivos e subpastas da pasta **toFlash** no computador Ubuntu usado para criar a imagem personalizada. Verifique se os arquivos da pasta **toFlash** estão na raiz do arquivo zip. Use uma ferramenta, como o SCP (ou PSCP se você estiver usando Putty) para copiar o arquivo **edison.zip** para a pasta /home/root na placa Edison.

Para enviar o trabalho de atualização do firmware e monitorar o andamento, use a [IU de exemplo do gerenciamento de dispositivos][lnk-dm-sample-ui] do Node.js. É possível executar este exemplo no Windows ou no Linux e ele requer o [Node.js][lnk-nodejs] 6.1.0 ou superior. Para recuperar, compilar e executar a IU de exemplo do gerenciamento de dispositivos em seu computador, siga as etapas abaixo:

1. Abra um **Prompt de comando**.

2. Confirme se você instalou o Node.js 6.1.0 ou superior, digitando `node --version`.

3. Clone o repositório do GitHub da interface do usuário do gerenciamento de dispositivos IoT do Azure ao executar o seguinte comando:

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Na pasta raiz de sua cópia clonada do repositório da interface do usuário do gerenciamento de dispositivos IoT do Azure, execute o comando a seguir para recuperar os pacotes dependentes:

    ```
    npm install
    ```

5. Quando o comando npm install for concluído, execute o seguinte comando para compilar o código:

    ```
    npm run build
    ```

6. Use um editor de texto para abrir o arquivo user-config.json na raiz da pasta clonada. Substitua o texto "&lt;YOUR CONNECTION STRING HERE&gt;" por sua cadeia de conexão do Hub IoT. Você pode encontrar essa cadeia de conexão no [Portal][lnk-azure-portal] do Azure.

7. No prompt de comando, execute o comando a seguir para iniciar o aplicativo de experiência do gerenciamento de dispositivos:

    ```
    npm run start
    ```

8. Quando o prompt de comando informar "Os serviços foram iniciados", abra um navegador da Web e navegue até o aplicativo de gerenciamento de dispositivos na URL a seguir para exibir seus dispositivos: <http://127.0.0.1:3003>.

    ![IU do gerenciamento de dispositivos][img-dm-ui]

9. Selecione o dispositivo **GW-device**, na lista suspensa **Trabalhos do Dispositivo**, selecione **Atualização de Firmware**, em seguida, clique em **Iniciar**.

10. No campo **URI do Pacote**, digite **file:///home/root/edison.zip** para usar o arquivo de imagem que você copiou anteriormente para a placa Edison. Clique em **Enviar**, **Sim**, em seguida, clique no link **Histórico de Trabalhos** para ver os novos trabalhos pai e filho em execução:

    ![Link do histórico de trabalhos][img-history-link]

11. Após alguns minutos, no terminal serial conectado à placa Edison, você verá a placa Edison reinicializar e aplicar as alterações do firmware:

    ```
    reading ota_update.scr
    14747 bytes read in 17 ms (846.7 KiB/s)
    ## Executing script at 00100000
    === OTA update script ===
    Validating Ota package
    part find mmc 0 label:update u_part_num;
    ota drive is mmc 0:9

    Validating edison_ifwi-dbg-00-dfu.bin hash for boot0 and boot1 partitions

    fatload mmc 0:9 0x6400000 edison_ifwi-dbg-00-dfu.bin;
    reading edison_ifwi-dbg-00-dfu.bin
    ...
    ```

12. Quando a placa Edison concluir a reinicialização, atualize a página na IU de exemplo do gerenciamento de dispositivos para ver se o status do trabalho agora está **concluído** para os dois trabalhos de atualização do firmware:

    ![Status do trabalho concluído][img-job-status]

Agora, você concluiu o tutorial que mostra como usar o software do gateway do Hub IoT e o cliente de gerenciamento de dispositivos em uma placa Intel Edison. Como parte deste tutorial, você:

- Criou uma imagem personalizada do Intel Edison que inclui o cliente de gerenciamento de dispositivos do Hub IoT e o software do gateway configurados para iniciar sempre que a placa Edison inicializa.
- Configurou a placa Edison e o cliente de gerenciamento de dispositivos para conectar o seu hub IoT.
- Iniciou um trabalho de gerenciamento de dispositivos a partir da IU de exemplo, que faz com que a placa Edison reinicialize e aplique uma nova imagem do firmware.

## Próximas etapas

Para saber mais sobre o gerenciamento de dispositivos com o Hub IoT e a IU de exemplo, consulte o artigo [Visão geral do gerenciamento de dispositivos do Hub IoT do Azure][lnk-device-management].

Se você quiser obter uma compreensão mais avançada do SDK do Gateway e experimentar alguns exemplos de código, visite [SDK do Gateway do Azure IoT][lnk-gateway-sdk].

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Guia do desenvolvedor][lnk-devguide]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]



<!-- Links and images -->

[img-dm-ui]: media/iot-hub-gateway-sdk-device-management/image1.png
[img-history-link]: media/iot-hub-gateway-sdk-device-management/image2.png
[img-job-status]: media/iot-hub-gateway-sdk-device-management/image3.png
[img-architecture]: media/iot-hub-gateway-sdk-device-management/architecture.png

[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-device-management]: iot-hub-device-management-overview.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-serial-connection]: https://software.intel.com/setting-up-serial-terminal-intel-edison-board
[lnk-inteledison-bsp]: http://www.intel.com/content/dam/support/us/en/documents/edison/sb/edisonbsp_ug_331188007.pdf
[lnk-hackgnar]: http://www.hackgnar.com/2016/01/manually-building-yocto-images-for.html
[lnk-shawnhymel]: http://shawnhymel.com/724/creating-a-custom-linux-kernel-for-the-edison-yocto-2-1/
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-nodejs]: https://nodejs.org/
[lnk-azure-portal]: https://portal.azure.com
[lnk-dm-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-physical]: iot-hub-gateway-sdk-physical-device.md
[lnk-gateway-scenario]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->