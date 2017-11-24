---
title: Criar um dispositivo de gateway com o Azure IoT Edge | Microsoft Docs
description: "Usar o Azure IoT Edge para criar um dispositivo de gateway que pode processar informações de vários dispositivos"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e1337ddf5ed84a06a62e2faa198f3e8fb49bc3bd
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-iot-edge-gateway-device-to-process-data-from-other-iot-devices---preview"></a>Criar um dispositivo de gateway do IoT Edge para processar dados de outros dispositivos IoT – versão prévia

Há duas maneiras de usar dispositivos do IoT Edge como gateways:

* Conectar dispositivos downstream que usam o [SDK do dispositivo IoT do Azure][lnk-devicesdk];
* Conectar os dispositivos que não usam o protocolo do Hub IoT.

Quando você usa um dispositivo do IoT Edge como um gateway, obtém as seguintes vantagens:

* **Multiplexação de conexão**. Todos os dispositivos que conectam ao Hub IoT por meio de um dispositivo do IoT Edge usarão a mesma conexão subjacente.
* **Suavização de tráfego**. O dispositivo do IoT Edge implementa automaticamente a retirada exponencial em caso de limitação do Hub IoT, persistindo as mensagens localmente. Isso tornará sua solução resiliente a picos no tráfego.
* **Suporte offline limitado**. O dispositivo de gateway armazenará localmente as mensagens que não podem ser entregues ao Hub IoT.

Neste artigo, chamaremos o *gateway do IoT Edge* de um dispositivo do IoT Edge usado como um gateway.

>[!NOTE]
>No momento:
> * Os dispositivos downstream não podem se autenticar no gateway se o gateway está desconectado do Hub IoT; e
> * Os dispositivos do IoT Edge não podem se conectar com gateways do IoT Edge.

## <a name="use-the-azure-iot-device-sdk"></a>Usar o SDK do dispositivo IoT do Azure

O hub do Edge que está instalado em todos os dispositivos do IoT Edge expõe os seguintes primitivos para os dispositivos downstream:

* mensagens do dispositivo para a nuvem e da nuvem para o dispositivo;
* métodos diretos; e
* operações de dispositivo gêmeo.

>[!NOTE]
>Atualmente, os dispositivos downstream não podem usar o upload de arquivo ao se conectarem por meio de um gateway do IoT Edge.

Ao conectar dispositivos ao gateway do IoT Edge usando o SDK do dispositivo IoT do Azure, você precisa:

* Configurar o dispositivo downstream com uma cadeia de conexão que referencia o nome do host do dispositivo de gateway; e
* Verifique se o dispositivo downstream confia no certificado usado para aceitar a conexão pelo dispositivo de gateway.

Quando você instala o tempo de execução do Azure IoT Edge usando o script de controle, um certificado é criado para o hub do Edge, assim como no tutorial Instalar o IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] e no [Linux][lnk-tutorial1-lin]. Esse certificado é usado pelo hub do Edge para aceitar conexões TLS de entrada e precisa ser confiável para o dispositivo downstream durante a conexão com o dispositivo de gateway.

Você pode criar qualquer infraestrutura de certificado que permite a relação de confiança necessária para sua topologia de dispositivo/gateway. Neste artigo, pressupomos a mesma configuração de certificado que você usará para habilitar a [segurança de AC X.509][lnk-iothub-x509] no Hub IoT, que envolve um certificado de Autoridade de Certificação X.509 associado a um hub IoT específico (a *AC proprietária do hub IoT*) e uma série de certificados, assinados com essa AC, instalados nos dispositivos do IoT Edge.

>[!IMPORTANT]
>Atualmente, os dispositivos do IoT Edge e os dispositivos downstream podem usar apenas [tokens SAS][lnk-iothub-tokens] para se autenticarem no Hub IoT. Os certificados serão usados apenas para validar a conexão TLS entre o dispositivo folha e de gateway.

Nossa configuração usa a **AC proprietária do hub IoT** como:
* um certificado de autenticação para a configuração do tempo de execução do IoT Edge em todos os dispositivos do IoT Edge; e como
* um certificado de chave pública instalado nos dispositivos downstream.

Isso resultará em uma solução que permite que todos os dispositivos usem qualquer dispositivo do IoT Edge como um gateway, desde que estejam conectados ao mesmo hub IoT.

### <a name="create-the-certificates-for-test-scenarios"></a>Criar os certificados para cenários de teste

Use os scripts de exemplo do PowerShell e Bash descritos em [Gerenciando uma amostra de certificado de Autoridade de Certificação][lnk-ca-scripts] para gerar uma **AC proprietária do hub IoT** autoassinada e certificados de dispositivo assinados com ela.

1. Siga a etapa 1 de [Gerenciando uma amostra de certificado de Autoridade de Certificação][lnk-ca-scripts] para instalar os scripts.
2. Siga a etapa 2 para gerar a **AC proprietária do hub IoT**; esse arquivo será usado pelos dispositivos downstream para validar a conexão.

Use as instruções a seguir para gerar um certificado para o dispositivo de gateway.

#### <a name="bash"></a>Bash

* Execute `./certGen.sh create_edge_device_certificate myGateway` para criar o novo certificado de dispositivo.  
  Isso criará os arquivos .\certs\new-edge-device.*, que contêm a chave pública e o PFX e .\private\new-edge-device.key.pem, que contém a chave privada do dispositivo.  
* `cat new-edge-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-edge-device-full-chain.cert.pem` para obter a chave pública.
* `./private/new-edge-device.cert.pem` contém a chave privada do dispositivo.

#### <a name="powershell"></a>Powershell

* Execute `New-CACertsEdgeDevice myGateway` para criar o novo certificado de dispositivo.
  Isso criará arquivos myEdgeDevice*, que contêm a chave pública, a chave privada e o PFX desse certificado.  Quando solicitado a inserir uma senha durante o processo de autenticação, insira “1234”.


>[!IMPORTANT]
>Esta amostra destina-se apenas a fins de teste. Para cenários de produção, consulte [Proteger a implantação de IoT][lnk-iothub-secure-deployment] para obter diretrizes de IoT do Azure sobre como proteger sua solução IoT e provisionar seu certificado de acordo.

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>Configurar um dispositivo do IoT Edge como um gateway

Para configurar seu dispositivo do IoT Edge como um gateway, você precisa apenas configurá-lo para usar o certificado do dispositivo criado na seção anterior.

Vamos pressupor os seguintes nomes de arquivo dos scripts de exemplo acima:

| Saída | Script Bash | Powershell |
| ------ | ----------- | ---------- |
| Certificado do dispositivo | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Chave privada do dispositivo | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Cadeia de certificados do dispositivo | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| AC proprietária do hub IoT | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 De maneira semelhante à instalação descrita em Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] ou no [Linux][lnk-tutorial1-lin], você precisa fornecer as informações acima para o tempo de execução do IoT Edge. 
 
 No Linux:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

No Windows:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

Por padrão, os scripts de exemplo não definem uma senha para a chave privada do dispositivo. Se você definir uma senha, adicione o seguinte parâmetro:

        --device-ca-passphrase {passphrase}

O script solicitará a definição de uma senha para o certificado do Agente do Edge.
Você precisa reiniciar o tempo de execução do IoT Edge após esse comando.

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>Configurar um aplicativo de dispositivo do Hub IoT como um dispositivo downstream

Um dispositivo downstream pode ser qualquer aplicativo que usa o [SDK do dispositivo IoT do Azure][lnk-devicesdk], como o aplicativo simples descrito em [Conectar seu dispositivo ao hub IoT usando o .NET][lnk-iothub-getstarted].

Primeiro, um aplicativo de dispositivo downstream precisa confiar no certificado de **Autoridade de Certificação proprietária do hub IoT** para validar as conexões TLS com os dispositivos de gateway. Essa etapa pode ser executada normalmente de duas maneiras: no nível do sistema operacional ou (para algumas linguagens) no nível do aplicativo.

Por exemplo, para aplicativos .NET, você pode adicionar o trecho a seguir para confiar em um certificado no formato PEM armazenado no caminho `certPath`.

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Observe que os scripts de exemplo mencionados acima geram a chave pública no arquivo `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) ou `RootCA.pem` (PowerShell).

A execução dessa etapa no nível do sistema operacional é diferente entre o Windows e distribuições Linux.

A segunda etapa é inicializar o SDK de dispositivo do Hub IoT com uma cadeia de conexão que referencia o nome do host do dispositivo de gateway.
Isso é feito acrescentando a propriedade `GatewayHostName` à cadeia de conexão do dispositivo. Por exemplo, esta é uma cadeia de conexão do dispositivo de exemplo para um dispositivo, à qual acrescentamos a propriedade `GatewayHostName`:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Essas duas etapas permitirão que o aplicativo de dispositivo se conecte com o dispositivo de gateway.

## <a name="use-other-protocols"></a>Usar outros protocolos

Uma das principais funções de um dispositivo de gateway é interpretar protocolos de dispositivos downstream. Para conectar dispositivos que não usam o protocolo do Hub IoT, desenvolva um módulo do IoT Edge que faz essa conversão e conecta-se em nome do dispositivo downstream ao Hub IoT.

Você pode optar por criar um gateway *transparente* ou *opaco*:

| &nbsp; | Gateway transparente | Gateway opaco|
|--------|-------------|--------|
| Identidades que são armazenadas no registro de identidade do Hub IoT | Identidades de todos os dispositivos conectados | Somente a identidade do dispositivo de gateway |
| Dispositivo gêmeo | Cada dispositivo conectado tem seu próprio dispositivo gêmeo | Somente o gateway tem um dispositivo e módulo gêmeos |
| Métodos diretos e mensagens da nuvem para o dispositivo | A nuvem pode lidar com cada dispositivo conectado individualmente | A nuvem pode lidar somente com o dispositivo de gateway |
| [Cotas e limitações do Hub IoT][lnk-iothub-throttles-quotas] | Aplica-se a cada dispositivo | Aplica-se ao dispositivo de gateway |

Ao usar um padrão de gateway opaco, todos os dispositivos que se conectam por meio do gateway compartilham a mesma fila da nuvem para o dispositivo, que pode conter no máximo 50 mensagens. Devido a isso, o padrão de gateway opaco deve ser usado somente quando muito poucos dispositivos estão se conectando por meio de cada gateway de campo e seu tráfego da nuvem para o dispositivo é baixo.

Ao implementar um gateway opaco, o módulo de conversão de protocolo usa a cadeia de conexão do módulo.

Ao implementar um gateway transparente, o módulo cria várias instâncias do cliente de dispositivo do Hub IoT, usando as cadeias de conexão para os dispositivos downstream.

## <a name="next-steps"></a>Próximas etapas

- [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/CACertToolEdge/tools/CACertificates/CACertificateOverview.md