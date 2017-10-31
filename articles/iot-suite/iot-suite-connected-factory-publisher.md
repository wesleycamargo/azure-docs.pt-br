---
title: "Usar o editor OPC da fábrica conectada do Azure IoT Suite | Microsoft Docs"
description: "Como criar e implantar a implementação de referência do editor OPC da fábrica conectada."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: dobett
ms.openlocfilehash: 86ffacae9265b68e8adfeb8f7d8c72626f872dba
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>Editor OPC para Azure IoT Edge

Este artigo descreve como usar a implementação de referência do editor OPC. A implementação de referência demonstra como usar o Azure IoT Edge para:

- Conectar-se a servidores UA OPC existentes.
- Publicar dados de telemetria codificados em JSON desses servidores em formato *Pub/Sub* UA OPC, usando uma carga JSON, para o Hub IoT do Azure. Você pode usar qualquer um dos protocolos de transporte com suporte pelo Azure IoT Edge.

Este aplicativo de referência inclui:

- Um *cliente* UA OPC para se conectar a servidores UA OPC existentes em sua rede.
- Um *servidor* UA OPC escutando na porta 62222 que você pode usar para gerenciar o que é publicado.

O aplicativo é implementado usando o .NET Core e pode ser executado em plataformas com suporte pelo .NET Core.

O editor implementa a lógica de repetição quando estabelece conexões com pontos de extremidade. O editor espera que os pontos de extremidade respondam dentro de um número especificado de solicitações de keep alive. Essa lógica de repetição permite que o editor detecte condições, como queda de energia, para um servidor UA OPC.

Para cada intervalo de publicação distinto de um servidor UA OPC, ele cria uma assinatura separada na qual todos os nós no intervalo de publicação são atualizados.

Para reduzir a carga de rede, o editor dá suporte a envio de dados enviados para o Hub IoT em lote. Um lote é enviado para o Hub IoT somente quando alcança o tamanho do pacote configurado.

Esse aplicativo usa a pilha de referência de UA OPC da OPC Foundation e, portanto, aplicam-se as restrições de licença. Visite http://opcfoundation.github.io/UA-.NETStandardLibrary/ para obter a documentação de UA OPC e os termos de licenciamento.

Você pode encontrar o código-fonte do editor OPC no repositório GitHub [Editor OPC para Azure IoT Edge](https://github.com/Azure/iot-edge-opc-publisher).

## <a name="prerequisites"></a>Pré-requisitos

Para criar o aplicativo, você precisa ter o [SDK do .NET Core 1.1.](https://docs.microsoft.com/dotnet/core/sdk) para o seu sistema operacional.

## <a name="build-the-application"></a>Compilar o aplicativo

### <a name="as-native-windows-application"></a>Como aplicativo nativo do Windows

Abra o projeto `OpcPublisher.sln` com o Visual Studio 2017 e compile a solução pressionando F7.

### <a name="as-docker-container"></a>Como contêiner do Docker

Para criar o aplicativo como um contêiner do Docker do Windows, use o arquivo de configuração `Dockerfile.Windows`.

Para criar o aplicativo como um contêiner do Docker do Linux, use o arquivo de configuração `Dockerfile`.

Na raiz do repositório no computador de desenvolvimento, digite o seguinte comando em uma janela de console:

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

A opção `-f` para `docker build` é opcional e o padrão é usar o arquivo de configuração `Dockerfile`.

O Docker também permite que você crie diretamente de um repositório git. Você pode criar um contêiner do Docker do Linux com o seguinte comando:

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>Configurar os nós de UA OPC para publicar

Para configurar os nós de UA OPC que devem ter seus valores publicados no Hub IoT do Azure, crie um arquivo de configuração formatado em JSON. O nome padrão para esse arquivo de configuração é `publishednodes.json`. O aplicativo atualiza e salva esse arquivo de configuração quando ele usa os métodos de servidor UA OPC **PublishNode** ou **UnpublishNode**.

A sintaxe do arquivo de configuração é a seguinte:

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Executar o aplicativo

### <a name="command-line-options"></a>Opções de linha de comando

Para ver o uso completo do aplicativo, use a opção de linha de comando `--help`. O exemplo abaixo mostra a estrutura de um comando:

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` é o nome do aplicativo UA OPC a ser usado. Esse parâmetro é necessário. O nome do aplicativo também é usado para registrar o editor no registro de dispositivo do Hub IoT.

`IoT Hubconnectionstring` é a cadeia de conexão do proprietário do Hub IoT. Esse parâmetro é opcional.

Há suporte para as seguintes opções:

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

Você pode usar as seguintes variáveis de ambiente para controlar o aplicativo:
- `_HUB_CS`: define a cadeia de conexão do proprietário do Hub IoT
- `_GW_LOGP`: define o nome do arquivo de log a ser usado
- `_TPC_SP`: define o caminho para armazenar certificados de estações confiáveis
- `_GW_PNFP`: define o nome do arquivo de configuração de publicação

Os argumentos de linha de comando substituem as configurações de variável de ambiente.

Normalmente, você deve especificar a cadeia de conexão do proprietário do Hub IoT apenas na primeira inicialização do aplicativo. A cadeia de conexão é criptografada e armazenada no repositório de certificados da plataforma.

Em chamadas subsequentes, a cadeia de conexão é lida do repositório de certificados da plataforma e reutilizada. Se você especificar a cadeia de conexão em cada inicialização, o dispositivo no registro de dispositivo do Hub IoT será sempre removido e recriado.

### <a name="native-on-windows"></a>Nativo no Windows

Para executar o aplicativo de modo nativo no Windows, abra o projeto `OpcPublisher.sln` com o Visual Studio 2017, compile a solução e publique-a. Você pode iniciar o aplicativo no **Diretório de destino** em que foi publicado com:

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Usar um contêiner criado automaticamente

Para executar o aplicativo em um contêiner criado automaticamente, crie e inicie seu próprio contêiner:

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>Usar um contêiner de hub.docker.com

Um contêiner pré-compilado está disponível no DockerHub. Para iniciá-lo, execute o seguinte comando:

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Importante ao usar um contêiner

#### <a name="access-to-the-publisher-opc-ua-server"></a>Acesso ao servidor do servidor UA OPC do editor

Por padrão, o servidor UA OPC do editor escuta na porta 62222. Para expor essa porta de entrada em um contêiner, você precisa usar a opção `docker run` `-p`:

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Habilitar a resolução de nome entre contêineres

Para habilitar a resolução de nome de dentro do contêiner para outros contêineres, você deverá:

- Criar uma rede de pontes de docker definida pelo usuário.
- Conectar o contêiner à rede usando a opção `--network`.
- Atribuir o contêiner usando a opção `--name`.

O exemplo abaixo mostra algumas das opções de configuração:

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

O contêiner pode ser acessado por outros contêineres pela rede usando o nome `publisher`.

#### <a name="assign-a-hostname"></a>Atribuir um nome de host

O editor usa o nome do host da máquina em que ele está em execução para gerar o certificado e o ponto de extremidade. O Docker escolhe um nome de host aleatório, a menos que você o defina com a opção `-h`. Veja um exemplo para definir o nome do host interno do contêiner para `publisher`:

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>Usando montagens associadas (sistema de arquivos compartilhado)

Em alguns cenários, você deseja ler informações de configuração em locais no host, ou gravar arquivos de log neles, em vez de usar o sistema de arquivos do contêiner. Para configurar esse comportamento, use a opção `-v` de `docker run` no modo de montagem associada.

#### <a name="store-for-x509-certificates"></a>Armazenamento para certificados X509

O armazenamento de certificados X509 não funciona com montagens associadas porque as permissões do caminho para o armazenamento precisam ser `rw` para o proprietário. Em vez disso, você precisa usar a opção `-v` de `docker run` no modo de volume.

## <a name="debug-the-application"></a>Depurar o aplicativo

### <a name="native-on-windows"></a>Nativo no Windows

Abra o projeto `OpcPublisher.sln` com o Visual Studio 2017 e inicie a depuração do aplicativo pressionando F5.

### <a name="in-a-docker-container"></a>Em um contêiner do Docker

O Visual Studio 2017 dá suporte à depuração de aplicativos em um contêiner do Docker usando `docker-compose`. No entanto, esse método não permite passar parâmetros de linha de comando.

Uma opção de depuração alternativa à qual o VS2017 dá suporte é a depuração por `ssh`. Você pode usar o arquivo de configuração da build do docker `Dockerfile.ssh` na raiz do repositório para criar um contêiner habilitado para SSH:

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

Agora você pode iniciar o contêiner para depurar o editor:

```cmd/sh
docker run -it publisherssh
```

No contêiner, você deve iniciar o daemon **ssh** manualmente:

```cmd/sh
service ssh start
```

Neste ponto, você pode criar uma sessão de **ssh** como o usuário `root` com senha `Passw0rd`.

Para se preparar para depurar o aplicativo no contêiner, conclua as seguintes etapas adicionais:

1. No lado do host, crie um arquivo `launch.json`:

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. Compile um projeto e publique-o em um diretório de sua escolha.

1. Use uma ferramenta como `WinSCP` para copiar os arquivos publicados no diretório `/root/publisher` no contêiner. Se você optar por usar um diretório diferente, atualize a propriedade `cdw` no arquivo `launch.json`.

Agora você pode iniciar a depuração usando o seguinte comando na janela Comando do Visual Studio:

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>Próximas etapas

Uma próxima etapa sugerida é aprender a [Implantar um gateway no Windows ou no Linux para a solução pré-configurada de fábrica conectada](iot-suite-connected-factory-gateway-deployment.md).