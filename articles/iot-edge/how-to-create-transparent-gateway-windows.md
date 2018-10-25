---
title: Criar um gateway transparente com o Azure IoT Edge - Windows | Microsoft Docs
description: Usar o Azure IoT Edge para criar um gateway transparente que pode processar informações de vários dispositivos
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f584e2cdcb038c6f8e9fcdbeecc22fb957bd7f8d
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394794"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Criar um dispositivo do IoT Edge Windows que atua como um gateway transparente

Este artigo fornece instruções detalhadas sobre o uso de um dispositivo do IoT Edge como um gateway transparente. Para o restante deste artigo, o termo *gateway do IoT Edge* se refere a um dispositivo de IoT Edge usado como um gateway transparente. Para obter informações mais detalhadas, consulte [Como um dispositivo IoT Edge pode ser usado como um gateway](./iot-edge-as-gateway.md), o que fornece uma visão geral conceitual.

>[!NOTE]
>No momento:
> * Se o gateway está desconectado do Hub IoT, os dispositivos downstream não podem se autenticar no gateway.
> * Os dispositivos habilitados para o Edge não podem se conectar com gateways do IoT Edge. 
> * Dispositivos downstream não podem usar o upload de arquivo.

A parte difícil de criar um gateway transparente é conectar de forma segura o gateway aos dispositivos downstream. Azure IoT Edge permite que você use a infraestrutura de PKI para configurar conexões seguras de TLS entre esses dispositivos. Neste caso, nós estamos permitindo um dispositivo downstream conectar-se a um dispositivo IoT Edge atuando como um gateway transparente.  Para manter a segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo Edge como você deseja apenas os dispositivos que se conectar a seus gateways e não um gateway potencialmente mal-intencionados.

Você pode criar qualquer infraestrutura de certificado que permite a relação de confiança necessária para sua topologia de dispositivo/gateway. Neste artigo, supomos a mesma configuração de certificado que você usaria para habilitar a [segurança X.509 da CA](../iot-hub/iot-hub-x509ca-overview.md) no Hub IoT, que envolve um certificado CA X.509 associado a um hub IoT específico (o proprietário do hub IoT CA) e uma série de certificados assinados com essa CA e uma CA para o dispositivo Edge.

![Instalação do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

O gateway apresenta o seu certificado AC de dispositivo Edge para o dispositivo downstream durante a iniciação da conexão. O dispositivo downstream verifica se que o certificado de AC de dispositivo Edge é assinado pelo certificado de autoridade de certificação de proprietário. Esse processo permite que o dispositivo downstream confirmar que o gateway vem de uma fonte confiável.

As etapas a seguir o orientará no processo de criação de certificados e instalá-los nos lugares certos.

## <a name="prerequisites"></a>Pré-requisitos
1. [Instalar o tempo de execução do Azure IoT Edge](./how-to-install-iot-edge-windows-with-windows.md) em um dispositivo do Windows que você deseja usar como o gateway transparente.

1. Obter OpenSSL para Windows. Há muitas maneiras de instalar o OpenSSL:

   >[!NOTE]
   >Se já tiver o OpenSSL instalado em seu dispositivo Windows, você pode ignorar esta etapa, mas verifique se `openssl.exe` está disponível na variável de ambiente `%PATH%`.

   * Faça o download e instale todos os [binários do OpenSSL de terceiros](https://wiki.openssl.org/index.php/Binaries), por exemplo, [este projeto no SourceForge](https://sourceforge.net/projects/openssl/).
   
   * Faça o download do código-fonte do OpenSSL e construa os binários em sua máquina sozinho ou por meio do [vcpkg](https://github.com/Microsoft/vcpkg). As instruções a seguir usam vcpkg para baixar o código-fonte, compilação e instalar o OpenSSL em seu computador Windows com etapas simples.

      1. Navegue para um diretório onde você deseja instalar vcpkg. Daqui em diante chamaremos isso de $VCPKGDIR. Siga as instruções para fazer o download e instalar o [vcpkg](https://github.com/Microsoft/vcpkg).
   
      1. Quando o vcpkg estiver instalado, em um prompt do powershell, execute o seguinte comando para instalar o pacote OpenSSL para Windows x64. Normalmente, a instalação leva cerca de 5 minutos para concluir.

         ```PowerShell
         .\vcpkg install openssl:x64-windows
         ```
      1. Adicione `$VCPKGDIR\installed\x64-windows\tools\openssl` à variável de ambiente `PATH` para que o arquivo `openssl.exe` fique disponível para invocação.

1. Navegue até o diretório no qual você deseja trabalhar. Daqui em diante será chamamos isso $WRKDIR.  Todos os arquivos serão criados neste diretório.
   
   cd $WRKDIR

1.  Obter os scripts para gerar os certificados necessários de não produção com o comando a seguir. Esses scripts ajudam você a criar os certificados necessários para configurar um gateway transparente.

      ```PowerShell
      git clone https://github.com/Azure/azure-iot-sdk-c.git
      ```

1. Copie os arquivos de configuração e script em seu diretório de trabalho. Além disso, configure a variável de env OPENSSL_CONF para usar o arquivo de configuração openssl_root_ca.cnf.

   ```PowerShell
   copy azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
   ```

1. Habilitar o PowerShell para executar os scripts, executando o seguinte comando

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Colocar as funções, usadas por scripts, no espaço para nome global do PowerShell por dot sourcing com o seguinte comando
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

1. Verificar se OpenSSL foi instalado corretamente e certifique-se de não haver colisões de nome com certificados existentes, executando o comando a seguir. Se houver problemas, o script deve descrever como corrigi-los em seu sistema.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Criação de certificado
1. Crie o certificado de AC do proprietário e um certificado intermediário. Os certificados são colocados em `$WRKDIR`.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

1. Crie uma chave privada e o certificado de AC de dispositivo Edge com o comando a seguir.

   >[!NOTE]
   > **NÃO** usar um nome que seja o mesmo nome de host DNS do gateway. Isso fará com que a certificação do cliente em relação a esses certificados falhe.

   ```PowerShell
   New-CACertsEdgeDevice "<gateway device name>"
   ```

## <a name="certificate-chain-creation"></a>Criação de cadeia de certificados
Crie uma cadeia de certificados de AC de certificação do proprietário, o certificado intermediário e certificado de AC de dispositivo Edge com o comando a seguir. Colocá-lo em um arquivo de cadeia permite facilmente instalá-lo em seu dispositivo Edge atuando como um gateway transparente.

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway device name>"
   ```

   O script cria os seguintes certificados e chaves:
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`
   * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="installation-on-the-gateway"></a>Instalação no gateway
1. Copie os seguintes arquivos de $WRKDIR em qualquer lugar no seu dispositivo Edge, serão considerados que $CERTDIR. Se você gerou os certificados no seu dispositivo de borda, ignore esta etapa.

   * Certificado de AC de dispositivo - `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Chave privada de AC do dispositivo - `$WRKDIR\private\new-edge-device.key.pem`
   * AC do proprietário - `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2. Definir as propriedades `certificate` no Daemon Security yaml arquivo config para o caminho onde você colocou os arquivos de certificado e chave.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\\certs\\azure-iot-test-only.root.ca.cert.pem"
```
## <a name="deploy-edgehub-to-the-gateway"></a>Implantar EdgeHub para o gateway
Um dos principais recursos do Azure IoT Edge é a possibilidade de implantar módulos em seus dispositivos IoT Edge na nuvem. Nesta seção você cria uma implantação aparentemente vazia; no entanto, o hub do Edge é automaticamente adicionado a todas as implantações, mesmo se não há nenhum outro módulo presente. Hub Edge é o módulo somente que necessário em um dispositivo Edge para que ele atue como um gateway transparente, para que criar uma implantação vazia é suficiente. 
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
Um dispositivo downstream pode ser qualquer aplicativo usando o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md), como o simples descrito em [Conecte seu dispositivo ao hub IoT usando o .NET](../iot-hub/quickstart-send-telemetry-dotnet.md). Um aplicativo de dispositivo downstream precisa confiar no certificado de **Autoridade de Certificação proprietária** para validar as conexões TLS com os dispositivos de gateway. Essa etapa pode ser executada normalmente de duas maneiras: no nível do sistema operacional ou (para algumas linguagens) no nível do aplicativo.

### <a name="os-level"></a>Nível OS
Instalar este certificado no repositório de certificados do sistema operacional permitirá que todos os aplicativos usem o proprietário do certificado de autoridade de certificação como um certificado confiável.

* Ubuntu - aqui está um exemplo de como instalar um certificado de autoridade de certificação em um host do Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Você deve ver uma mensagem dizendo, "Atualizando certificados no /etc/ssl/certs... 1 adicionado, removido 0; feito."

* Windows - aqui está um exemplo de como instalar um certificado de autoridade de certificação em um host do Windows.
  1. No menu Iniciar, digite “Gerenciar certificados de computador”. Isso deve abrir um utilitário chamado `certlm`.
  2. Navegue para **Certificados de Computador Local** > **Certificados de Raiz** > **Certificados**> Clique com o botão direito> **Todas as Tarefas** > **Importar** para iniciar o assistente de importação de certificados.
  3. Siga as etapas conforme indicado e importe o arquivo de certificado $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem.
  4. Quando concluído, você verá uma mensagem "Importada com êxito".

### <a name="application-level"></a>Nível de aplicativo
Para aplicativos .NET, você pode adicionar o snippet a seguir para confiar em um certificado no formato PEM. Inicializar a variável `certPath` com `$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Conecte o dispositivo downstream para o gateway
Inicialize o sdk do dispositivo IoT Hub com uma cadeia de conexão referente ao nome do host do dispositivo de gateway. Isso é feito acrescentando a propriedade `GatewayHostName` à cadeia de conexão do dispositivo. Por exemplo, esta é uma cadeia de conexão do dispositivo de exemplo para um dispositivo, à qual acrescentamos a propriedade `GatewayHostName`:

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

Para obter mais informações sobre o roteamento de mensagens, consulte [composição do módulo](./module-composition.md).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Próximas etapas
[Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge](module-development.md).
