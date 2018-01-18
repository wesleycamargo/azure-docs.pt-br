---
title: "Implantar o gateway do alocador conectado – Azure | Microsoft Docs"
description: "Como implantar um gateway no Windows ou no Linux para permitir conectividade com a solução pré-configurada de fábrica conectada."
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
ms.date: 12/11/2017
ms.author: dobett
ms.openlocfilehash: c9854c68a95c2c1cc584503eb2f0b0dba6091016
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-preconfigured-solution-on-windows-or-linux"></a>Implantar um gateway edge para a solução pré-configurada da fábrica conectada no Windows ou Linux

Você precisa de dois componentes de software para implantar um gateway edge para a solução pré-configurada da *fábrica Conectada*:

- O *Proxy OPC* estabelece uma conexão com a fábrica Conectada. O Proxy OPC aguarda então as mensagens de comando e controle do navegador de OPC integrado que é executado no portal da solução de fábrica conectada.

- O *Publicador OPC* conecta-se a servidores OPC UA locais existentes e encaminha mensagens de telemetria deles para a fábrica Conectada. Você pode conectar um dispositivo clássico de OPC usando o [adaptador clássico de OPC para o agente do usuário de OPC](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Ambos os componentes são de código livre e estão disponíveis como origem no GitHub e nos contêineres do Docker no DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) | [Publicador OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [Proxy OPC](https://github.com/Azure/iot-edge-opc-proxy)         | [Proxy OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Não é necessário um endereço IP público ou portas de entrada abertas no firewall do gateway para nenhum componente. Os componentes do Proxy OPC e do Publicador OPC só usam a porta de saída 443.

As etapas neste artigo mostram como implantar um gateway edge usando o Docker no Windows ou Linux. O gateway permite conectividade com a solução pré-configurada de fábrica conectada. Você também pode usar os componentes sem a fábrica Conectada.

> [!NOTE]
> Ambos os componentes podem ser usados como módulos [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Escolher um dispositivo de gateway

Se você ainda não tem um dispositivo de gateway, a Microsoft te aconselha a comprar um gateway comercial de um de nossos parceiros. Para obter uma lista de dispositivos de gateway compatíveis com a solução de fábrica Conectada, visite o [catálogo de dispositivo IoT do Azure](https://catalog.azureiotsuite.com/?q=opc). Siga as instruções que acompanham o dispositivo para configurar o gateway.

Se preferir, use as instruções a seguir para configurar manualmente um dispositivo de gateway existente.

## <a name="install-and-configure-docker"></a>Instalar e configurar o Docker

Instale o [Docker para Windows](https://www.docker.com/docker-windows) em seu dispositivo de gateway baseado no Windows ou use um gerenciador de pacotes para instalar o docker no seu dispositivo de gateway baseado em Linux.

Durante a configuração do Docker para Windows, selecione uma unidade no seu computador host para compartilhar com o Docker. A captura de tela a seguir mostra o compartilhamento da unidade **D** em seu sistema do Windows para permitir o acesso à unidade de host de dentro de um contêiner do docker:

![Instalar o Docker para Windows](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Você também pode executar essa etapa após a instalação do docker usando o diálogo **Configurações**. Clique com botão direito no ícone do **Docker** na bandeja do sistema do Windows e escolha **Configurações**.

Se você estiver usando o Linux, nenhuma configuração adicional é necessária para habilitar o acesso ao sistema de arquivos.

No Windows, crie uma pasta na unidade compartilhada com o Docker; no Linux, crie uma pasta no sistema de arquivos raiz. Este passo a passo é referente a essa pasta como `<SharedFolder>`.

Quando você referencia o `<SharedFolder>` em um comando do Docker, certifique-se de usar a sintaxe correta para o seu sistema operacional. Aqui estão dois exemplos, um para Windows e outro para Linux:

- Se você estiver usando a pasta `D:\shared` no Windows como o seu `<SharedFolder>`, a sintaxe de comando do Docker é `//d/shared`.

- Se você estiver usando a pasta `/shared` no Linux como o seu `<SharedFolder>`, a sintaxe de comando do Docker é `/shared`.

Para obter mais informações, consulte a referência do mecanismo do docer [Usar volumes](https://docs.docker.com/engine/admin/volumes/volumes/).

## <a name="configure-the-opc-components"></a>Configurar os componentes OPC

Antes de instalar os componentes OPC, conclua as etapas a seguir para preparar o seu ambiente:

1. Para concluir a implantação do gateway, você precisa da cadeia de caracteres de conexão **iothubowner** do Hub IoT na sua implantação de fábrica Conectada. No [portal do Azure](http://portal.azure.com/), navegue até o Hub IoT no grupo de recursos criado quando você implantou a solução de fábrica Conectada. Clique em **Políticas de acesso compartilhado** para acessar a cadeia de conexão **iothubowner**:

    ![Localizar a cadeia de conexão Hub IoT](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    Copie o valor de **Cadeia de conexão – chave primária**.

1. Para permitir a comunicação entre contêineres do docker, você precisa de uma rede de ponte definida pelo usuário. Para criar uma rede de ponte para os seus contêineres, execute os seguintes comandos no prompt de comando:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Para verificar se a rede de ponte **iot_edge** foi criada, execute o comando a seguir:

    ```cmd/sh
    docker network ls
    ```

    A sua rede de ponte **iot_edge** está incluída na lista de redes.

Para executar o Publicador OPC, execute o comando a seguir no prompt de comando:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- O [GitHub de Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) e a [referência de execução do docker](https://docs.docker.com/engine/reference/run/) fornecem mais informações sobre:

  - As opções de linha de comando do docker especificadas antes do nome de contêiner (`microsoft/iot-edge-opc-publisher:2.1.3`).
  - O significado dos parâmetros de linha de comando de Publicador OPC especificado após o nome do contêiner (`microsoft/iot-edge-opc-publisher:2.1.3`).

- O `<IoTHubOwnerConnectionString>` é a cadeia de conexão da política de acesso compartilhado do **iot hubowner** do portal do Azure. Você copiou essa cadeia de conexão em uma etapa anterior. Você só precisa dessa cadeia de conexão para a primeira execução do Publicador OPC. Em execuções subsequentes, você deve omiti-la, porque ela representa um risco de segurança.

- O `<SharedFolder>` que você usa e sua sintaxe são descritos na seção [Instalar e configurar o Docker](#install-and-configure-docker). O Publicador OPC usa o `<SharedFolder>` para ler o arquivo de configuração do Publicador OPC, gravar o arquivo de log e tornar esses arquivos disponíveis fora do contêiner.

- O Publicador OPC lê sua configuração a partir do arquivo **publishednodes.json**, que você deve colocar na pasta `<SharedFolder>/docker`. Esse arquivo de configuração define quais dados do nó de agente do usuário de OPC em um determinado servidor de agente do usuário de OPC o Publicador OPC deve assinar.

- Sempre que o servidor de agente do usuário de OPC notificar o Publicador OPC quanto a uma alteração de dados, o novo valor é enviado ao Hub IoT. Dependendo das configurações de envio em lote, o Publicador OPC pode, primeiro, acumular os dados antes de enviá-los ao Hub IoT em um lote.

- A sintaxe completa do arquivo **publishednodes.json** é descrita na página do [Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) no GitHub.

    O trecho de código a seguir mostra um exemplo simples de um arquivo **publishednodes.json**. Este exemplo mostra como publicar o valor **CurrentTime** a partir de um servidor de agente do usuário de OPC com o nome do host **win10pc**:

    ```json
    [
      {
        "EndpointUrl": "opc.tcp://win10pc:48010",
        "OpcNodes": [
          {
            "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
          }
        ]
      }
    ]
    ```

    No arquivo **publishednodes.json**, o servidor de agente do usuário do OPC é especificado pela URL de ponto de extremidade. Se você especificar o nome do host usando um rótulo de nome do host (como **win10pc**) como no exemplo anterior, em vez de um endereço IP, a resolução de endereço de rede no contêiner deve ser capaz de resolver este rótulo de nome do host para um endereço IP.

- O Docker não dá suporte à resolução de nome NetBIOS, somente à resolução de nome DNS. Se você não tiver um servidor DNS na rede, você pode usar a solução alternativa mostrada no exemplo anterior de linha de comando. O exemplo anterior de linha de comando usa o parâmetro `--add-host` para adicionar uma entrada no arquivo de hosts de contêineres. Essa entrada permite que o nome de host pesquise por `<OpcServerHostname>`, resolvendo para o endereço IP fornecido `<IpAddressOfOpcServerHostname>`.

- O agente do usuário do OPC usa certificados X.509 para autenticação e criptografia. Você precisa colocar o certificado do Publicador OPC no servidor de agente do usuário de OPC no qual você está se conectando, para garantir que ele confie no Publicador OPC. O repositório de certificados do Publicador OPC está localizado na pasta `<SharedFolder>/CertificateStores`. Você pode encontrar o certificado do Publicador OPC na pasta `trusted/certs` na pasta `CertificateStores`.

  As etapas para configurar o servidor de agente do usuário de OPC dependem do dispositivo que você está usando. Normalmente, essas etapas ficam documentadas no manual do usuário do servidor de agente do usuário de OPC.

Para instalar o Proxy OPC, execute o comando a seguir no prompt de comando:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Você precisa executar a instalação de uma vez em um sistema.

Use o comando a seguir para executar o Proxy OPC:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

O Proxy OPC salva a cadeia de conexão durante a instalação. Em execuções subsequentes, você deve omiti-la, porque ela representa um risco de segurança.

## <a name="enable-your-gateway"></a>Habilitar o seu gateway

Conclua as etapas a seguir para habilitar o seu gateway na solução de pré-configurada da fábrica Conectada:

1. Quando ambos os componentes estiverem em execução, navegue até a página **Conectar seu próprio servidor de agente de usuário de OPC** no portal de solução da fábrica Conectada. Esta página só está disponível para os administradores na solução. Insira a URL de ponto de extremidade do publicador (opc.tcp://publisher:62222) e clique em **Conectar**.

1. Estabeleça uma relação de confiança entre o portal da fábrica Conectada e o Publicador OPC. Quando você encontrar um aviso de certificado, clique em **Continuar**. Em seguida, você verá um erro informando que o publicador OPC não confia no cliente Web do agente do usuário. Para resolver esse erro, copie o certificado do **cliente Web do agente do usuário** da pasta `<SharedFolder>/CertificateStores/rejected/certs` para a pasta `<SharedFolder>/CertificateStores/trusted/certs` no gateway. Não é preciso reiniciar o gateway.

Agora, é possível se conectar ao gateway da nuvem e você está pronto para adicionar servidores OPC UA à solução.

## <a name="add-your-own-opc-ua-servers"></a>Adicionar seus próprios servidores OPC de agente do usuário

Para adicionar seus próprios servidores OPC de agente do usuário à solução pré-configurada de fábrica Conectada:

1. Navegue até a página **Conectar seu próprio Servidor OPC de agente do usuário** no portal de solução de fábrica Conectada. Siga as mesmas etapas da seção anterior para estabelecer uma relação de confiança entre o portal da fábrica Conectada e o servidor do agente do usuário OPC.

    ![Portal de solução](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. Procure a árvore de nós de agente do usuário do OPC do seu servidor de agente do usuário do OPC, clique com o botão direito do mouse nos nós OPC que você deseja enviar para a fábrica Conectada e selecione **publicar**.

1. Agora, a telemetria flui do dispositivo de gateway. Você pode ver a telemetria na exibição **Locais de Fábrica** do portal de fábrica Conectada em **Nova Fábrica**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a arquitetura da solução pré-configurada de fábrica Conectada, confira [Passo a passo da solução pré-configurada de fábrica Conectada](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough).

Saiba mais sobre a [Implementação de referência do Editor de OPC](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).