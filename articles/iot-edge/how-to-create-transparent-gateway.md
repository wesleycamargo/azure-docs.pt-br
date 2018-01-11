---
title: Criar um dispositivo de gateway transparente com o Azure IoT Edge | Microsoft Docs
description: "Usar o Azure IoT Edge para criar um dispositivo de gateway transparente que pode processar informações de vários dispositivos"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 25f4cea1908a0f9bdf387ddfed5f29e6d19bdd20
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Criar um dispositivo do IoT Edge que atua como um gateway transparente - visualização

Este artigo fornece instruções detalhadas sobre o uso de um dispositivo do IoT Edge como um gateway transparente. Para o restante deste artigo, o termo *gateway do IoT Edge* se refere a um dispositivo de IoT Edge usado como um gateway transparente. Para saber mais, confira [Como um dispositivo do IoT Edge pode ser usado como gateway][lnk-edge-as-gateway], que oferece uma visão geral conceitual. 

>[!NOTE]
>No momento:
> * Se o gateway está desconectado do Hub IoT, os dispositivos downstream não podem se autenticar no gateway.
> * Os dispositivos do IoT Edge não podem se conectar com gateways do IoT Edge.

## <a name="understand-the-azure-iot-device-sdk"></a>Noções básicas do SDK do dispositivo IoT do Azure


O hub do Edge que está instalado em todos os dispositivos do IoT Edge expõe os seguintes primitivos para os dispositivos downstream:

* mensagens do dispositivo para a nuvem e da nuvem para o dispositivo;
* métodos diretos
* operações de dispositivo gêmeo

Atualmente, os dispositivos downstream não podem usar o upload de arquivo ao se conectarem por meio de um gateway do IoT Edge.

Ao conectar dispositivos ao gateway do IoT Edge usando o SDK do dispositivo IoT do Azure, você precisa:

* Configurar o dispositivo downstream com uma cadeia de conexão que referencia o nome do host do dispositivo de gateway; e
* Verifique se o dispositivo downstream confia no certificado usado para aceitar a conexão pelo dispositivo de gateway.

Quando você instala o tempo de execução do Azure IoT Edge usando o script de controle, um certificado é criado para o hub do Edge, assim como no tutorial Instalar o IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] e no [Linux][lnk-tutorial1-lin]. Esse certificado é usado pelo hub do Edge para aceitar conexões TLS de entrada e precisa ser confiável para o dispositivo downstream durante a conexão com o dispositivo de gateway.

Você pode criar qualquer infraestrutura de certificado que permite a relação de confiança necessária para sua topologia de dispositivo/gateway. Neste artigo, pressupomos a mesma configuração de certificado que você usará para habilitar a [segurança de AC X.509][lnk-iothub-x509] no Hub IoT, que envolve um certificado de Autoridade de Certificação X.509 associado a um hub IoT específico (a *AC proprietária do hub IoT*) e uma série de certificados, assinados com essa AC, instalados nos dispositivos do IoT Edge.

>[!IMPORTANT]
>Atualmente, os dispositivos do IoT Edge e os dispositivos downstream podem usar apenas [tokens SAS][lnk-iothub-tokens] para se autenticarem no Hub IoT. Os certificados serão usados apenas para validar a conexão TLS entre o dispositivo folha e de gateway.

Nossa configuração usa a **AC proprietária do hub IoT** destas duas formas:
* Um certificado de autenticação para a configuração do tempo de execução do IoT Edge em todos os dispositivos do IoT Edge; e
* Um certificado de chave pública instalado nos dispositivos downstream.

Isso resulta em uma solução que permite que todos os dispositivos usem qualquer dispositivo do IoT Edge como um gateway, desde que estejam conectados ao mesmo hub IoT.

## <a name="create-the-certificates-for-test-scenarios"></a>Criar os certificados para cenários de teste

Use os scripts de exemplo do PowerShell e Bash descritos em [Gerenciando uma amostra de certificado de Autoridade de Certificação][lnk-ca-scripts] para gerar uma **AC proprietária do hub IoT** autoassinada e certificados de dispositivo assinados com ela.

>[!IMPORTANT]
>Esta amostra destina-se apenas a fins de teste. Para cenários de produção, consulte [Proteger a implantação de IoT][lnk-iothub-secure-deployment] para obter diretrizes de IoT do Azure sobre como proteger sua solução IoT e provisionar seu certificado de acordo.


1. Clone os [SDKs do Microsoft Azure IoT e bibliotecas para C] do GitHub:

   ```
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. Para instalar os scripts de certificado, siga as instruções em **Etapa 1: configuração inicial** de [Exemplo de gerenciamento de certificado de AC][lnk-ca-scripts]. 
3. Para gerar a **AC do proprietário de hub IoT**, siga as instruções na **Etapa 2: criar a cadeia de certificados**. Esse arquivo é usado pelos dispositivos downstream para validar a conexão.
4. Para gerar um certificado para seu dispositivo de gateway, use as instruções do PowerShell ou do Bash:

### <a name="bash"></a>Bash

Crie o novo certificado de dispositivo:

   ```
   ./certGen.sh create_edge_device_certificate myGateway
   ```

Novos arquivos são criados: .\certs\new-edge-device.* , que contêm a chave pública e o PFX, e .\private\new-edge-device.key.pem, que contém a chave privada do dispositivo.
 
No diretório `certs`, execute o seguinte comando para obter a cadeia completa da chave pública de dispositivo:

   ```
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>Powershell

Crie o novo certificado de dispositivo: 
   ```
   New-CACertsEdgeDevice myGateway
   ```

Novos arquivos myEdgeDevice* são criados, que contêm a chave pública, a chave privada e o PFX desse certificado. 

Quando solicitado a inserir uma senha durante o processo de autenticação, insira “1234”.

## <a name="configure-a-gateway-device"></a>Configurar um dispositivo de gateway

Para configurar seu dispositivo do IoT Edge como um gateway, você precisa apenas configurá-lo para usar o certificado do dispositivo criado na seção anterior.

Vamos pressupor os seguintes nomes de arquivo dos scripts de exemplo acima:

| Saída | Script Bash | PowerShell |
| ------ | ----------- | ---------- |
| Certificado do dispositivo | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Chave privada do dispositivo | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Cadeia de certificados do dispositivo | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| AC proprietária do hub IoT | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

Forneça as informações do dispositivo e o certificado ao tempo de execução do IoT Edge. 
 
No Linux, usando a saída do Bash:

   ```
   sudo iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

No Windows, usando a saída do PowerShell:

   ```
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

Por padrão, os scripts de exemplo não definem uma senha para a chave privada do dispositivo. Se você definir uma senha, adicione o seguinte parâmetro:

   ```
   --device-ca-passphrase {passphrase}
   ```

O script solicita a definição de uma senha para o certificado do Agente do Edge. Reinicie o tempo de execução do IoT Edge após esse comando:

   ```
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Configurar um dispositivo downstream

Um dispositivo downstream pode ser qualquer aplicativo que usa o [SDK do dispositivo IoT do Azure][lnk-devicesdk], como o aplicativo simples descrito em [Conectar seu dispositivo ao hub IoT usando o .NET][lnk-iothub-getstarted].

Primeiro, um aplicativo de dispositivo downstream precisa confiar no certificado de **Autoridade de Certificação proprietária do hub IoT** para validar as conexões TLS com os dispositivos de gateway. Essa etapa pode ser executada normalmente de duas maneiras: no nível do sistema operacional ou (para algumas linguagens) no nível do aplicativo.

Por exemplo, para aplicativos .NET, você pode adicionar o trecho a seguir para confiar em um certificado no formato PEM armazenado no caminho `certPath`. Dependendo da versão do script usada, o caminho faz referência a um `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) ou a um `RootCA.pem` (Powershell).

   ```
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

A execução dessa etapa no nível do sistema operacional é diferente entre o Windows e distribuições Linux.

A segunda etapa é inicializar o SDK de dispositivo do Hub IoT com uma cadeia de conexão que referencia o nome do host do dispositivo de gateway.
Isso é feito acrescentando a propriedade `GatewayHostName` à cadeia de conexão do dispositivo. Por exemplo, esta é uma cadeia de conexão do dispositivo de exemplo para um dispositivo, à qual acrescentamos a propriedade `GatewayHostName`:

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

Essas duas etapas permitem que o aplicativo de dispositivo se conecte com o dispositivo de gateway.

## <a name="next-steps"></a>Próximas etapas
[Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge][lnk-module-dev].

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
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus