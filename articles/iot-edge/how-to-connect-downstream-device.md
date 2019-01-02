---
title: Conectar dispositivos downstream – Azure IoT Edge | Microsoft Docs
description: Como configurar dispositivos downstream ou folha para se conectar por meio de dispositivos de gateway do Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0797952641efb9fe876d72f2dce0418ff5771d40
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083325"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Conecte um dispositivo downstream a um gateway do Azure IoT Edge

O Azure IoT Edge permite cenários de gateway transparentes, nos quais um ou mais dispositivos podem passar suas mensagens por meio de um único dispositivo de gateway que mantém a conexão ao Hub IoT. Depois de configurar o dispositivo de gateway, você precisa saber como conectar com segurança os dispositivos de recebimento de dados. 

Este artigo identifica problemas comuns com conexões de dispositivos downstream e orienta você na configuração de seus dispositivos de recebimento de dados por meio de: 

* Explicando a segurança da camada de transporte (TLS) e os fundamentos do certificado. 
* Explicando como as bibliotecas TLS funcionam em diferentes sistemas operacionais e como cada sistema operacional lida com certificados.
* Andando por exemplos de IoT do Azure em vários idiomas para ajudar você a começar. 

Neste artigo, os termos *gateway* e *gateway IoT Edge* se referem a um dispositivo IoT Edge configurado como um gateway transparente. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as etapas deste artigo, você deve ter dois dispositivos prontos para uso:

1. Um dispositivo IoT Edge configurado como um gateway transparente. 
    [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)

    Depois de configurar seu dispositivo de gateway, copie o certificado **azure-iot-test-only.root.ca.cert.pem** do gateway e disponibilize-o em qualquer lugar em seu dispositivo downstream. 

2. Um dispositivo downstream que possui uma identidade de dispositivo do Hub IoT. 
    Você não pode usar um dispositivo IoT Edge como o dispositivo downstream. Em vez disso, use um dispositivo registrado como um dispositivo IoT regular no Hub IoT. No portal, você pode registrar um novo dispositivo na seção **Dispositivos IoT**. Ou você pode usar a CLI do Azure para [registrar um dispositivo](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). Copie a cadeia de conexão e disponibilize-a para uso nas seções posteriores. 

    Atualmente, somente dispositivos downstream com autenticação de chave simétrica podem se conectar através de gateways do IoT Edge. As autoridades de certificação X.509 e os certificados autoassinados X.509 não são atualmente suportados. 

## <a name="prepare-a-downstream-device"></a>Preparar um dispositivo downstream

Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço em nuvem do [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub). Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Para todos os fins práticos, um dispositivo downstream pode até ser um aplicativo em execução no próprio dispositivo de gateway IoT Edge. 

Para conectar um dispositivo downstream a um gateway IoT Edge, você precisa de duas coisas:

1. Um dispositivo ou aplicativo configurado com uma cadeia de conexão de conexão de dispositivo Hub IoT anexada com informações para conectá-lo ao gateway. 

    A cadeia de caracteres de conexão é formatada como: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Anexe a propriedade **GatewayHostName** com o nome do host do dispositivo de gateway ao final da cadeia de conexão. O valor de **GatewayHostName** deve corresponder ao valor de **hostname** no arquivo config.yaml do dispositivo de gateway. 

    A cadeia de caracteres final se parece com: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. O dispositivo ou aplicativo precisa confiar no certificado **raiz CA** ou **CA do proprietário** para validar as conexões TLS com o dispositivo de gateway. 

    Essa etapa mais complicada é explicada em detalhes no restante deste artigo. Essa etapa pode ser executada de duas maneiras: instalando o certificado de autoridade de certificação no armazenamento de certificados do sistema operacional ou (para determinados idiomas) fazendo referência ao certificado nos aplicativos que usam os SDKs do IoT do Azure.

## <a name="tls-and-certificate-fundamentals"></a>Conceitos básicos TLS e o certificado

O desafio de conectar com segurança dispositivos downstream ao IoT Edge é como qualquer outra comunicação cliente / servidor segura que ocorre na Internet. Um cliente e um servidor se comunicam com segurança pela Internet usando [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). O TLS é construído usando as construções [padrão de infra-estrutura de chave pública (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) chamadas de certificados. O TLS é uma especificação bastante envolvida e aborda uma ampla variedade de tópicos relacionados à proteção de dois pontos de extremidade, mas a seção a seguir descreve resumidamente o que é necessário para conectar dispositivos com segurança a um gateway do IoT Edge.

Quando um cliente se conecta a um servidor, o servidor apresenta uma cadeia de certificados, chamada de *cadeia de certificados do servidor*. Uma cadeia de certificados normalmente inclui um certificado de autoridade de certificação raiz (CA), um ou mais certificados CA intermediários e, finalmente, o próprio certificado do servidor. Um cliente estabelece confiança com um servidor, verificando criptograficamente toda a cadeia de certificados do servidor. Essa validação de cliente da cadeia de certificados de servidor é chamada *autenticação de servidor*. Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado de autoridade de certificação raiz que foi usado para criar o certificado do servidor (ou emitir uma). Normalmente ao se conectar a sites, um navegador vem pré-configurada com certificados de autoridade de certificação comumente usados para que o cliente tem um processo contínuo. 

Quando um dispositivo se conecta ao Hub IoT do Azure, o dispositivo é o cliente e o serviço de nuvem do Hub IoT é o servidor. O serviço de nuvem Hub IoT é respaldado por um certificado de CA raiz chamado **Baltimore CyberTrust Root**, que está publicamente disponível e é amplamente usado. Como o certificado CA do Hub IoT já está instalado na maioria dos dispositivos, muitas implementações TLS (OpenSSL, Schannel, LibreSSL) o usam automaticamente durante a validação do certificado do servidor. Um dispositivo que pode se conectar com êxito ao Hub IoT pode ter problemas ao tentar se conectar a um gateway IoT Edge.

Quando um dispositivo se conecta a um gateway IoT Edge, o dispositivo downstream é o cliente e o dispositivo de gateway é o servidor. O Azure IoT Edge permite que os operadores (ou usuários) criem cadeias de certificados de gateway da maneira que acharem melhor. O operador pode optar por usar um certificado de autoridade de certificação público, como o Baltimore, ou usar um certificado de autoridade de certificação raiz autoassinado (ou interno). Os certificados públicos de CA costumam ter um custo associado a eles, então são normalmente usados em cenários de produção. Os certificados de CA autoassinados são preferidos para desenvolvimento e teste. Os artigos de configuração de gateway transparente listados na seção de pré-requisitos usam certificados de CA raiz autoassinados. 

Quando você usa um certificado de autoridade de certificação raiz auto-assinado para um gateway IoT Edge, ele precisa ser instalado ou fornecido a todos os dispositivos de recebimento de dados que tentam se conectar ao gateway. 

Para saber mais sobre os certificados do IoT Edge e algumas implicações de produção, consulte [Detalhes do uso do certificado do IoT Edge](iot-edge-certs.md).

## <a name="install-certificates-using-the-os"></a>Instalar os certificados usando o sistema operacional

Este artigo usa o *proprietário CA* para fazer referência ao certificado de CA raiz, já que esse é o termo usado pelos scripts no artigo do gateway de pré-requisito. 

A instalação do certificado da autoridade de certificação do proprietário no armazenamento de certificados do sistema operacional geralmente permite que a maioria dos aplicativos use o certificado da autoridade de certificação do proprietário. Existem algumas exceções, como o aplicativo NodeJS, que não usa o armazenamento de certificados do sistema operacional, mas usa o armazenamento de certificados interno do tempo de execução do nó. Se você não conseguir instalar o certificado no nível do sistema operacional, consulte os exemplos específicos de idioma mais adiante neste artigo para usar o certificado com o SDK do IoT do Azure em aplicativos. 

### <a name="ubuntu"></a>Ubuntu

Os seguintes comandos são um exemplo de como instalar um certificado de CA em um host Ubuntu. Esta amostra pressupõe que você esteja usando o certificado **azure-iot-test-only.root.ca.cert.pem** dos artigos de pré-requisitos e que você tenha copiado o certificado em um local no downstream dispositivo.  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Você deve ver uma mensagem que diz, "Atualizando certificados no /etc/ssl/certs... 1 adicionado, removido 0; feito."

### <a name="windows"></a> Windows

As etapas a seguir são um exemplo de como instalar um certificado de autoridade de certificação em um host do Windows. Esta amostra pressupõe que você esteja usando o certificado **azure-iot-test-only.root.ca.cert.pem** dos artigos de pré-requisitos e que você tenha copiado o certificado em um local no downstream dispositivo.  

1. No menu Iniciar, pesquise e selecione **Gerenciar certificados de computador**. Um utilitário chamado **certlm** é aberta.
2. Navegue para **Certificados - Computador Local** > **Autoridades de Certificação Raiz Confiáveis**.
3. Clique com o botão direito em **Certificates** e selecione **All Tasks** > **Import**. Deve iniciar o Assistente para importação de certificados. 
4. Siga as etapas conforme direcionado e importe o arquivo de certificado `<path>/azure-iot-test-only.root.ca.cert.pem`. Quando concluído, você verá uma mensagem "Importada com êxito". 

Você também pode instalar certificados programaticamente usando APIs .NET, conforme mostrado na amostra .NET mais adiante neste artigo. 

Geralmente, os aplicativos usam a pilha TLS fornecida pelo Windows chamada [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) para se conectar com segurança através de TLS. O Schannel *requer* que quaisquer certificados sejam instalados no armazenamento de certificados do Windows antes de tentar estabelecer uma conexão TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usar certificados com SDKs de IoT do Azure

Este artigo se refere ao certificado da CA raiz como o *proprietário CA*, já que esse é o termo usado pelos scripts que geram o certificado autoassinado nos artigos de pré-requisitos. 

Esta seção descreve como os SDKs do IoT do Azure se conectam a um dispositivo IoT Edge usando aplicativos de amostra simples. O objetivo de todas as amostras é conectar o cliente do dispositivo e enviar mensagens de telemetria ao gateway, em seguida, fechar a conexão e sair. 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>Conceitos comuns entre todos os SDKs de IoT do Azure

Ter duas coisas prontas antes de usar os exemplos de nível de aplicativo:

1. Cadeia de conexão de Hub IoT do dispositivo downstream modificada para apontar para o dispositivo de gateway.

    A cadeia de caracteres de conexão é formatada como: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Anexe a propriedade **GatewayHostName** com o nome do host do dispositivo de gateway ao final da cadeia de conexão. O valor de **GatewayHostName** deve corresponder ao valor de **hostname** no arquivo config.yaml do dispositivo de gateway. 

    A cadeia de caracteres final se parece com: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. O caminho completo para o certificado de CA raiz que você copiou e salvou em algum lugar em seu dispositivo downstream.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Esta seção fornece um aplicativo de amostra para conectar um cliente de dispositivo do Azure IoT NodeJS a um gateway do IoT Edge. Para hosts Linux e Windows, você deve instalar o certificado de CA raiz no nível do aplicativo, conforme mostrado aqui, pois os aplicativos NodeJS não usam o armazenamento de certificados do sistema. 

1. Obter a amostra para **edge_downstream_device.js** da [repositório de exemplos do SDK do dispositivo IoT do Azure para Node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Certifique-se de ter todos os pré-requisitos para executar a amostra revisando o arquivo **readme.md**. 
3. No arquivo edge_downstream_device.js, atualize as variáveis **connectionString** e **edge_ca_cert_path**. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

Para reconhecimento da amostra que você está executando, o snippet de código a seguir é como o SDK do cliente lê o arquivo de certificado e o utiliza para estabelecer uma conexão TLS segura: 

```nodejs
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Esta seção apresenta um aplicativo de exemplo para se conectar a um cliente do dispositivo IoT do Azure .NET para um gateway IoT Edge. No entanto, os aplicativos .NET são capazes de usar automaticamente quaisquer certificados instalados no armazenamento de certificados do sistema em hosts Linux e Windows.

1. Obtenha o exemplo para **EdgeDownstreamDevice** da [pasta de exemplos .NET do IoT Edge](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Certifique-se de ter todos os pré-requisitos para executar a amostra revisando o arquivo **readme.md**. 
3. No arquivo **Properties / launchSettings.json**, atualize as variáveis **DEVICE_CONNECTION_STRING** e **CA_CERTIFICATE_PATH**. Se você quiser usar o certificado instalado no armazenamento de certificados confiáveis no sistema host, deixe essa variável em branco. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

Para instalar programaticamente um certificado confiável no armazenamento de certificados por meio de um aplicativo .NET, consulte a função **InstallCACert ()** no arquivo **EdgeDownstreamDevice / Program.cs**. Essa operação é idempotente, portanto, pode ser executada várias vezes com os mesmos valores sem efeito adicional. 

### <a name="c"></a>C

Esta seção apresenta um aplicativo de amostra para conectar um cliente de dispositivo do Azure IoT C a um gateway do IoT Edge. O C SDK pode operar com muitas bibliotecas TLS, incluindo OpenSSL, WolfSSL e Schannel. Para obter mais informações, consulte o [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). 

1. Obtenha o aplicativo **iotedge_downstream_device_sample** do [SDK do dispositivo IoT do Azure para amostras C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Certifique-se de ter todos os pré-requisitos para executar a amostra revisando o arquivo **readme.md**. 
3. No arquivo iotedge_downstream_device_sample.c, atualize as variáveis **connectionString** e **edge_ca_cert_path**. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 

O SDK do dispositivo IoT do Azure para C fornece uma opção para registrar um certificado de CA ao configurar o cliente. Essa operação não instala o certificado em nenhum lugar, mas usa um formato de cadeia de caracteres do certificado na memória. O certificado salvo é fornecido para a pilha TLS subjacente ao estabelecer uma conexão. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Nos hosts do Windows, se você não estiver usando o OpenSSL ou outra biblioteca TLS, o padrão do SDK será usar o Schannel. Para que o Schannel funcione, o certificado da CA raiz da IoT Edge deve ser instalado no armazenamento de certificados do Windows, não definido usando a operação `IoTHubDeviceClient_SetOption`. 

### <a name="java"></a>Java

Esta seção apresenta um aplicativo de amostra para conectar um cliente de dispositivo Java IoT do Azure a um gateway IoT Edge. 

1. Obter a amostra para **evento de envio** da [SDK do dispositivo IoT do Azure para exemplos de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Certifique-se de ter todos os pré-requisitos para executar a amostra revisando o arquivo **readme.md**. 
3. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

### <a name="python"></a>Python

Esta seção apresenta um aplicativo de exemplo para conectar um cliente de dispositivo Python do IoT do Azure a um gateway do IoT Edge. 

1. Obtenha o exemplo para **edge_downstream_client** do [SDK do dispositivo IoT do Azure para amostras do Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Certifique-se de ter todos os pré-requisitos para executar a amostra revisando o arquivo **readme.md**. 
3. No arquivo edge_downstream_client.py, atualize as variáveis **CONNECTION_STRING** e **TRUSTED_ROOT_CA_CERTIFICATE_PATH**. 
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo. 


## <a name="test-the-gateway-connection"></a>Testar a conexão de gateway

Este é um comando de exemplo que testes que tudo o que foi configurado corretamente. Você deve ver uma mensagem informando que "verificada Okey".

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="next-steps"></a>Próximas etapas

Saiba como o IoT Edge pode estender [recursos off-line](offline-capabilities.md) para dispositivos downstream. 