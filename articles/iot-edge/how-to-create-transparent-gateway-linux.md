---
title: Criar um gateway transparente com o Azure IoT Edge - Linux | Microsoft Docs
description: Usar o Azure IoT Edge para criar um gateway transparente que pode processar informações de vários dispositivos
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5a78d6fb8ee52f0daba80a77cc8a5e75c2e5248d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034252"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Criar um dispositivo do IoT Edge Linux que atua como um gateway transparente

Este artigo fornece instruções detalhadas sobre o uso de um dispositivo do IoT Edge como um gateway transparente. Para o restante deste artigo, o termo *gateway do IoT Edge* se refere a um dispositivo de IoT Edge usado como um gateway transparente. Para saber mais, confira [Como um dispositivo do IoT Edge pode ser usado como gateway][lnk-edge-as-gateway], que oferece uma visão geral conceitual.

>[!NOTE]
>No momento:
> * Se o gateway está desconectado do Hub IoT, os dispositivos downstream não podem se autenticar no gateway.
> * Os dispositivos do IoT Edge não podem se conectar com gateways do IoT Edge.
> * Dispositivos downstream não podem usar o upload de arquivo.

A parte difícil de criar um gateway transparente é conectar de forma segura o gateway aos dispositivos downstream. Azure IoT Edge permite que você use a infraestrutura de PKI para configurar conexões seguras de TLS entre esses dispositivos. Neste caso, nós estamos permitindo um dispositivo downstream conectar-se a um dispositivo IoT Edge atuando como um gateway transparente.  Para manter a segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo Edge como você deseja apenas os dispositivos que se conectar a seus gateways e não um gateway potencialmente mal-intencionados.

Você pode criar qualquer infraestrutura de certificado que permite a relação de confiança necessária para sua topologia de dispositivo/gateway. Neste artigo, pressupomos a mesma configuração de certificado que você usará para habilitar a [segurança de AC X.509][lnk-iothub-x509] no Hub IoT, que envolve um certificado de Autoridade de Certificação X.509 associado a um hub IoT específico (a AC proprietária do hub IoT) e uma série de certificados, assinados com essa AC, e um AC para os dispositivos do IoT Edge.

![Instalação do gateway][1]

O gateway apresenta o seu certificado AC de dispositivo Edge para o dispositivo downstream durante a iniciação da conexão. O dispositivo downstream verifica se que o certificado de AC de dispositivo Edge é assinado pelo certificado de autoridade de certificação de proprietário. Esse processo permite que o dispositivo downstream confirmar que o gateway vem de uma fonte confiável.

As etapas a seguir o orientará no processo de criação de certificados e instalá-los nos lugares certos.

## <a name="prerequisites"></a>pré-requisitos
1.  Instale o tempo de execução do Azure IoT Edge em um dispositivo de Linux que você deseja usar como o gateway transparente.
   * [Linux x64][lnk-install-linux-x64]
   * [Linux ARM32][lnk-install-linux-arm]

2.  Obter os scripts para gerar os certificados necessários de não produção com o comando a seguir. Esses scripts ajudam você a criar os certificados necessários para configurar um gateway transparente. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Esses scripts usam OpenSSL para gerar os certificados necessários e OpenSSL requer algumas configurações.
   
   1. Navegue até o diretório no qual você deseja trabalhar. Daqui em diante será chamamos isso $WRKDIR.  Todos os arquivos serão criados neste diretório.

      cd $WRKDIR
   
   1. Copie os arquivos de script e de configuração em seu diretório de trabalho.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Criação de certificado
1.  Crie o certificado de AC do proprietário e um certificado intermediário. Estes estão todos colocados em `$WRKDIR`.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   As saídas da execução do script são os seguintes certificados e chaves:
   * Certificados
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * simétricas
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  Crie uma chave privada e o certificado de AC de dispositivo Edge com o comando a seguir.

   >[!NOTE]
   > **NÃO** usar um nome que seja o mesmo nome de host DNS do gateway. Isso fará com que a certificação do cliente em relação a esses certificados falhe.

      ```cmd
      ./certGen.sh create_edge_device_certificate "<gateway device name>"
      ```

   As saídas da execução do script são os seguintes certificados e chave:
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Criação de cadeia de certificados
Crie uma cadeia de certificados de AC de certificação do proprietário, o certificado intermediário e certificado de AC de dispositivo Edge com o comando a seguir. Colocá-lo em um arquivo de cadeia permite facilmente instalá-lo em seu dispositivo Edge atuando como um gateway transparente.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Instalação no gateway
1.  Copie os seguintes arquivos de $WRKDIR em qualquer lugar no seu dispositivo Edge, serão considerados que $CERTDIR. Se você gerou os certificados no seu dispositivo Edge, ignore esta etapa.

   * Certificado de AC de dispositivo - `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Chave privada de AC do dispositivo - `$WRKDIR/private/new-edge-device.key.pem`
   * AC do proprietário - `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`

2.  Definir as propriedades `certificate` no Daemon Security yaml arquivo config para o caminho onde você colocou os arquivos de certificado e chave.

```yaml
certificates:
  device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Implantar EdgeHub para o gateway
Um dos principais recursos do Azure IoT Edge é a possibilidade de implantar módulos em seus dispositivos IoT Edge na nuvem. Esta seção apresenta a você criar uma implantação aparentemente vazia; No entanto o Hub Edge é automaticamente adicionada a todas as implantações, mesmo se não houver nenhum módulo presente. Hub Edge é o módulo somente que necessário em um dispositivo Edge para que ele atue como um gateway transparente, para que criar uma implantação vazia é suficiente. 
1. No portal do Azure, navegue para o hub IoT.
2. Vá para **IoT Edge** e selecione o dispositivo Edge IoT que você deseja usar como um gateway.
3. Selecione **Definir Módulos**.
4. Selecione **Avançar**.
5. Na etapa **Especificar rotas**, você deve ter uma rota padrão que envie todas as mensagens de todos os módulos para o Hub IoT. Caso contrário, adicione o código a seguir e selecione **Avançar**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. Na etapa Revisar Modelo, selecione **Enviar**.

## <a name="installation-on-the-downstream-device"></a>Instalação do dispositivo downstream
Um dispositivo downstream pode ser qualquer aplicativo que usa o [SDK do dispositivo IoT do Azure][lnk-devicesdk], como o aplicativo simples descrito em [Conectar seu dispositivo ao hub IoT usando o .NET][lnk-iothub-getstarted]. Um aplicativo de dispositivo downstream precisa confiar no certificado de **Autoridade de Certificação proprietária** para validar as conexões TLS com os dispositivos de gateway. Essa etapa pode ser executada normalmente de duas maneiras: no nível do sistema operacional ou (para algumas linguagens) no nível do aplicativo.

### <a name="os-level"></a>Nível OS
Instalar este certificado no repositório de certificados do sistema operacional permitirá que todos os aplicativos usem o proprietário do certificado de autoridade de certificação como um certificado confiável.

* Ubuntu - aqui está um exemplo de como instalar um certificado de autoridade de certificação em um host do Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Você deve ver uma mensagem dizendo, "Atualizando certificados no /etc/ssl/certs... 1 adicionado, removido 0; feito."

* Windows - [Este](https://msdn.microsoft.com/en-us/library/cc750534.aspx) artigo fornece detalhes sobre como fazer isso em um dispositivo Windows usando o Assistente de importação de certificado. 

### <a name="application-level"></a>Nível de aplicativo
Para aplicativos .NET, você pode adicionar o trecho a seguir para confiar em um certificado no formato PEM. Inicializar a variável `certPath` com `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Conecte o dispositivo downstream para o gateway
Você deve inicializar o SDK de dispositivo do Hub IoT com uma cadeia de conexão que referencia o nome do host do dispositivo de gateway. Isso é feito acrescentando a propriedade `GatewayHostName` à cadeia de conexão do dispositivo. Por exemplo, esta é uma cadeia de conexão do dispositivo de exemplo para um dispositivo, à qual acrescentamos a propriedade `GatewayHostName`:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Este é um comando de exemplo que testes que tudo o que foi configurado corretamente. Você sohuld uma mensagem informando que "verificada Okey".
   >
   >openssl s_client -connect mygateway.contoso.com:8883 -CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem -showcerts

## <a name="routing-messages-from-downstream-devices"></a>Roteamento de mensagens de dispositivos downstream
O tempo de execução de IoT Edge pode rotear as mensagens enviadas dos dispositivos downstream assim como as mensagens enviadas pelos módulos. Isso permite que você execute uma análise em um módulo em execução no gateway antes de enviar dados para a nuvem. A rota abaixo seria usada para enviar mensagens de um dispositivo de downstream chamado `sensor` para um nome de módulo `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Consulte o [artigo de composição do módulo] [lnk-módulo-composição] para obter mais detalhes sobre o roteamento de mensagens.

## <a name="next-steps"></a>Próximas etapas
[Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
