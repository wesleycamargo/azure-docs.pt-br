<properties
   pageTitle="Conectando seu dispositivo à solução de monitoramento remoto Azure IoT Suite | Microsoft Azure"
   description="Descreve como conectar seu dispositivo à solução de monitoramento remoto Azure IoT Suite pré-configurada com um exemplo envolvendo temperatura e umidade."
   services="iot-hub"
   documentationCenter="na"
   authors="hegate"
   manager="jamesosb"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="hegate"/>


# Conectando o dispositivo à solução de monitoramento remoto Azure IoT Suite


## Visão geral do cenário

Neste exemplo, teremos três fontes de dados simulados: temperatura externa, interna temperatura e umidade. Não estamos usando sensores reais para fins de simplicidade (os dados são gerados automaticamente no código do cliente), mas é recomendável como uma próxima etapa conectar seu sensor favorito e enviar dados reais. Visite os recursos vinculados na seção Estendendo sua solução para saber mais.

## Pré-requisitos

### Provisionar seu IoT Suite

Se você ainda não provisionou sua solução pré-configurada de monitoramento remoto, faça-o [aqui](http://www.microsoft.com/pt-BR/server-cloud/internet-of-things/getting-started.aspx).


### Provisionar o dispositivo na solução de monitoramento remoto
```
Note: if you have already provisioned a device on your solution, you can skip this step.
```
Para conectar o dispositivo à solução pré-configurada, você deve obter as credenciais de dispositivo do painel de controle. Isso será usado em seu aplicativo cliente para que o dispositivo possa ser identificado. Siga esta etapas

1.  No canto inferior esquerdo do painel, clique em "Adicionar um dispositivo". ![][1]
2.  Em Dispositivo Personalizado, clique no botão "Adicionar novo".

    ![][2]
3.  Escolha a sua própria identificação de dispositivo digitando um nome como realdevice1 e clique em Verificar ID para conferir se esse nome ainda não foi usado. ![][3]

5. Copie suas credenciais fornecidas (ID do dispositivo, nome de host do Hub IoT e chave do dispositivo). Você precisará delas posteriormente no aplicativo cliente para conectar o dispositivo à solução. ![][4]
6. Verifique se o dispositivo está exibido corretamente na seção de dispositivos. O status será "Pendente". Assim deve ser até que a conexão entre o dispositivo e a nuvem seja estabelecida.

    ![][5]

[1]: ./media/iot-suite-connecting-devices/suite0.png
[2]: ./media/iot-suite-connecting-devices/suite1.png
[3]: ./media/iot-suite-connecting-devices/suite2.png
[4]: ./media/iot-suite-connecting-devices/suite3.png
[5]: ./media/iot-suite-connecting-devices/suite5new.png

Agora, escolha qual idioma você gostaria de usar para continuar o seu exemplo. Neste tutorial, criamos códigos de exemplo para C e node.js, mas você também pode implementá-la em C# e Java.

## Enviando dados do dispositivo para a solução de monitoramento remoto usando C


### Executando seu dispositivo em Linux

1. Configure seu ambiente: se você nunca usou nosso SDK de Dispositivo antes, saiba como configurar o ambiente em Linux [aqui](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux).

1. Abra o arquivo **c/serializer/samples/serializer/remote\_monitoring.c** em um editor de texto.

2. Localize as seguintes linhas no arquivo: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Substitua "[Id do dispositivo]" e "[chave do dispositivo]” com os dados do dispositivo.

4. Use os dados do dispositivo Nomedohost do Hub IoT para preencher o nome e o sufixo IoTHub. Para fazer isso, você precisa dividir em IoTHub + IoTHubSuffix. Por exemplo, se seu nome de host do Hub IoT for Contoso.azure-devices.net, Contoso será seu nome IoTHub e o resto será o Sufixo. O resultado deve ser assim:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

5. Salve suas alterações e compile os exemplos. Para criar o exemplo, você pode executar o script build.sh no diretório **build\_all/c/linux**.

6. Execute o aplicativo de exemplo **c/serializador/samples/remote\_monitoring/linux/remote\_monitoring**.

Visualize seu dispositivo registrado e os dados

7. Volte para o painel de solução de monitoramento remoto. O dispositivo deve estar com o status alterado para Em execução na lista de dispositivos. ![][18]

8. Clique no painel para ver os dados de entrada. O exemplo é configurado para enviar 50 unidades de temperatura interna, 55 unidades de temperatura externa e 50 de umidade. Observe que o painel mostra somente temperatura e umidade por padrão.

8. Agora, vá para a [seção](#command) Comando e controle para saber como alterar a temperatura no seu dispositivo da solução de monitoramento remoto.


### Executando seu dispositivo no Windows


1. Configure seu ambiente: se você nunca usou nosso SDK de Dispositivo antes, saiba como configurar o ambiente em Windows [aqui](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows).

1. Inicie uma nova instância do Visual Studio 2015. Abra a solução **remote\_monitoring.sln** na pasta **c\\serializer\\build\\windows** da sua cópia local do repositório.

2. No Visual Studio, em **Solution Explorer**, navegue até a pasta de exemplos. No projeto **remote\_monitoring**, abra o arquivo **remote\_monitoring.c**.

2. Localize as seguintes linhas no arquivo: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Substitua "[Id do dispositivo]" e "[chave do dispositivo]” com os dados do dispositivo.

4. Use os dados do dispositivo Nomedohost do Hub IoT para preencher o nome e o sufixo IoTHub. Para fazer isso, você precisa dividi-los da seguinte forma:

    Se seu nome de host do Hub IoT for Contoso.azure-devices.net, Contoso será seu nome IoTHub e o resto depois dele será o sufixo. O resultado deve ser assim:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Em **Solution Explorer**, clique com o botão direito do mouse no projeto **remote\_monitoring**, clique em **Depurar** e em **Iniciar nova instância** para compilar e executar o exemplo. O console exibe mensagens à medida que o aplicativo envia mensagens do dispositivo para a nuvem pelo Hub IoT.

Visualize seu dispositivo registrado e os dados

7. Volte para o painel de solução de monitoramento remoto. O dispositivo deve estar com o status alterado para Em execução na lista de dispositivos. ![][18]

8. Clique no painel para ver os dados de entrada. O exemplo é configurado para enviar 50 unidades de temperatura interna, 55 unidades de temperatura externa e 50 de umidade. Observe que o painel mostra somente temperatura e umidade por padrão.

8. Agora, vá para a [seção](#command) Comando e controle para saber como alterar a temperatura no seu dispositivo da solução de monitoramento remoto.


8. Agora, vá para a seção Comando e controle para saber como alterar a temperatura no seu dispositivo da solução de monitoramento remoto.

### Executando seu dispositivo em mbed

As instruções a seguir descrevem as etapas para conectar um dispositivo [Freescale FRDM-K64F habilitado para mbed](https://developer.mbed.org/platforms/FRDM-K64F/) ao Hub IoT do Azure.


Requisitos

- Hardware exigido: [Freescale K64F habilitado para mbed](https://developer.mbed.org/platforms/FRDM-K64F/) ou semelhante.

Conecte o dispositivo

- Conecte a placa à sua rede usando um cabo Ethernet. Essa etapa é necessária, já que o exemplo depende de acesso à internet.

- Conecte o dispositivo ao computador usando um cabo microUSB. Não deixe de conectar o cabo à porta USB correta no dispositivo, como mostra [esta](https://developer.mbed.org/platforms/frdm-k64f/) Figura, na seção "Introdução".

- Execute as [instruções sobre o manual do mbed](https://developer.mbed.org/handbook/SerialPC) para configurar a conexão serial com o dispositivo do computador de desenvolvimento. Se você estiver no Windows, instale os drivers de porta serial do Windows localizados [aqui](http://developer.mbed.org/handbook/Windows-serial-configuration#1-download-the-mbed-windows-serial-port).

Crie o projeto mbed e importe o código de exemplo

- No navegador da Web, vá até o [site de desenvolvedor](https://developer.mbed.org/) mbed.org. Se você ainda não se inscreveu, verá uma opção para criar uma nova conta (ele é gratuito). Caso contrário, faça logon com suas credenciais de conta. Em seguida, clique em **Compilador** no canto superior direito da página. Isso deve levá-lo até a interface de gerenciamento do espaço de trabalho.

- Verifique se a plataforma de hardware que você está usando é exibida no canto superior direito da janela ou clique no ícone no canto superior direito para selecionar a plataforma de hardware.

- Clique em **Importar** no menu principal. Clique em **Clique aqui** para importar do link de URL ao lado do logotipo de globo mbed.

	![][6]

- Na janela pop-up, insira o link para o código de exemplo https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/

	![][7]

- Você pode ver no compilador mbed que a importação desse projeto importou várias bibliotecas. Algumas são fornecidas e mantidas pela equipe do Azure IoT ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)), enquanto outras são bibliotecas de terceiros disponíveis no catálogo de bibliotecas mbed.

	![][8]

- Abra remote\_monitoring\\remote\_monitoring.c e localize o seguinte código no arquivo: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Substitua [Id do dispositivo], [chave do dispositivo] com os dados do dispositivo.

4. Use os dados do dispositivo Nomedohost do Hub IoT para preencher o nome e o sufixo IoTHub. Para fazer isso, você precisa dividi-los da seguinte forma:

    Se seu nome de host do Hub IoT for Contoso.azure-devices.net, Contoso será seu nome IoTHub e o resto depois dele será o sufixo. O resultado deve ser assim:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```
    ![][9]

Compile e execute o programa

- Clique em **Compilar** para criar o programa. Você pode ignorar os avisos com segurança, mas se a compilação gerar erros, corrija-os antes de continuar.

- Se a compilação for bem-sucedida, um arquivo .bin será gerado com o nome do seu projeto. Copie o arquivo .bin para o dispositivo. Salvar o arquivo .bin no dispositivo faz com que a sessão de terminal atual para o dispositivo seja redefinida. Ao se reconectar, redefina o terminal novamente de forma manual ou inicie um novo terminal. Isso permite que o dispositivo mbed seja redefinido e inicie a execução do programa.

- Conecte-se ao dispositivo usando um aplicativo de cliente SSH, como o PuTTY. Você pode determinar qual porta serial o dispositivo usa verificando o Gerenciador de Dispositivos do Windows:


- Em PuTTY, clique no tipo de conexão **Serial**. O dispositivo provavelmente se conectará em 115200; portanto, insira esse valor na caixa **Velocidade**. Em seguida, clique em **Abrir**:

	![][11]

O programa inicia a execução. Talvez seja necessário redefinir a placa (pressione CTRL+Break ou pressione o botão de redefinição da placa) se o programa não iniciar automaticamente quando você se conectar.

Visualize seu dispositivo registrado e os dados 7. Volte para o painel de solução de monitoramento remoto. O dispositivo deve estar com o status alterado para Em execução na lista de dispositivos. ![][18]

8. Clique no painel para ver os dados de entrada. O exemplo é configurado para enviar 50 unidades de temperatura interna, 55 unidades de temperatura externa e 50 de umidade. Observe que o painel mostra somente temperatura e umidade por padrão.

8. Agora, vá para a [seção](#command) Comando e controle para saber como alterar a temperatura no seu dispositivo da solução de monitoramento remoto.



[6]: ./media/iot-suite-connecting-devices/mbed1.png
[7]: ./media/iot-suite-connecting-devices/mbed2a.png
[8]: ./media/iot-suite-connecting-devices/mbed3a.png
[9]: ./media/iot-suite-connecting-devices/suite6.png
[10]: ./media/iot-suite-connecting-devices/mbed5a.png
[11]: ./media/iot-suite-connecting-devices/mbed6.png
[12]: ./media/iot-suite-connecting-devices/mbed7.png

Para aprender sobre comando e controle, vá para tal seção mais adiante neste tutorial.

## Enviando dados do dispositivo para a solução de monitoramento remoto usando node.js



-   Em nosso repositório azure-iot-sdks, localize os seguintes arquivos: packages.json (em /node/common) e remote\_monitoring.js (em node/device/samples/). Copie-os para seu dispositivo e coloque-los na mesma pasta.

- Abra o arquivo remote-monitoring.js e procure as seguintes variáveis:


   ```
   static const char* deviceId = "[Device Id]";
   static const char* deviceKey = "[Device Key]";
   static const char* hubName = "[IoTHub Name]";
   static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
   ```

-  Substitua "[Id do dispositivo]" e "[chave do dispositivo]” com os dados do dispositivo.

-  Use os dados do dispositivo Nomedohost do Hub IoT para preencher o nome e o sufixo IoTHub. Para fazer isso, você precisa dividi-los da seguinte forma:

   Se seu nome de host do Hub IoT for Contoso.azure-devices.net, Contoso será seu nome IoTHub e o resto depois dele será o sufixo. O resultado deve ser assim:


   ```
     static const char* deviceId = "mydevice";
   static const char* deviceKey = "mykey";
   static const char* hubName = "Contoso";
   static const char* hubSuffix = "azure-devices.net";
   ```


- Salve o arquivo. Execute o seguinte comando na pasta de destino:

```
npm install
node .
```

3.  Substitua cada uma das variáveis com as informações coletadas na etapa anterior. Salve as alterações.


4. Abra um shell (Linux) ou prompt de comando Node.js (Windows) e navegue até a pasta **node\\samples**. Em seguida, execute o aplicativo de exemplo usando o seguinte comando:

    ```
    node simple_sample_remotemonitoring.js
    ```

Visualizar seu dispositivo e os dados de entrada

6. No portal de solução pré-configurada, clique na seção de dispositivos para garantir que o status do dispositivo foi alterado para "Em execução" e que você pode ver todos os dados do fabricante.

7. Clique no painel e selecione seu dispositivo em "Dispositivos a Exibir". Agora você deve ver os dados de telemetria que estão sendo monitorados na solução de monitoramento remoto.


## <a name="command"></a>Comandar e controlar seu dispositivo no painel de controle

Agora que o dispositivo está conectado e enviando dados de temperatura gerados automaticamente do dispositivo, você pode comandar e controlar seu dispositivo remotamente do Hub IoT. Você pode implementar vários tipos de comandos que se adaptam a seu aplicativo de negócios. Nesse caso, implementamos uma alteração de temperatura, como se houvesse a necessidade de controlá-la da solução. Para enviar o comando, você deve:

-  Clicar no seu ID de dispositivo na lista de dispositivos (você pode encontrar a seção de dispositivo no menu do lado esquerdo).

	![][13]

- No menu à direita em que são mostrados os detalhes do dispositivo, clique em "Enviar comando"


- Selecionar o comando que você deseja executar: nesse caso, escolhemos "Definir temperatura", pois queremos alterar a temperatura para a qual o dispositivo está definido. Selecione o comando e escolha o valor de temperatura. Clique em Enviar Comando e a temperatura nova será enviada ao dispositivo. Observação: você verá que no histórico de comandos o resultado do comando é "Pendente". Isso ocorre porque, para fins de simplicidade, esses exemplos ainda não implementaram lógica no dispositivo para responder ao Hub IoT. Você pode fazer isso estendendo a solução.

	![][14]
- Volte para o painel e verifique se os dados atualizados estão vindo. Você deve ver estatísticas atualizadas sobre a temperatura e os novos dados que estão sendo exibidos no histórico da telemetria.




[13]: ./media/iot-suite-connecting-devices/suite4.png
[14]: ./media/iot-suite-connecting-devices/suite7-1.png
[15]: ./media/iot-suite-connecting-devices/suite8a.png
[16]: ./media/iot-suite-connecting-devices/mbed4a.png
[17]: ./media/iot-suite-connecting-devices/suite9.png
[18]: ./media/iot-suite-connecting-devices/suite10.png


## Próximas etapas

Há várias maneiras de estender a funcionalidade deste exemplo: conectar o sensor real ao seu dispositivo para enviar a data real; implementar a funcionalidade de comando e controle, etc. Use nosso [guia](articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md) sobre como estender a solução de monitoramento remoto para saber mais sobre isso.

<!---HONumber=Oct15_HO2-->