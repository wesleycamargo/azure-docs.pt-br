---
title: Configurar dispositivos para proxies de rede - Azure IoT Edge | Microsoft Docs
description: Como configurar o tempo de execução do Azure IoT Edge e quaisquer módulos de IoT Edge voltados para a Internet para comunicação por meio de um servidor proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 33f5cd6e1d2989a9ca5c26bbcf947bd6eade3831
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774193"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy

Os dispositivos IoT Edge enviam solicitações HTTPS para se comunicarem com o Hub IoT. Se o seu dispositivo estiver conectado a uma rede que usa um servidor proxy, você precisará configurar o tempo de execução do IoT Edge para se comunicar através do servidor. Os servidores proxy também podem afetar os módulos individuais do IoT Edge se eles fizerem solicitações HTTP ou HTTPS que não são roteadas pelo hub do IoT Edge. 

Configurar um dispositivo IoT Edge para funcionar com um servidor proxy segue estas etapas básicas: 

1. Instale o tempo de execução do IoT Edge no dispositivo. 
2. Configure o daemon do Docker e o daemon do IoT Edge no seu dispositivo para usar o servidor proxy.
3. Configure as propriedades edgeAgent no arquivo config.yaml no seu dispositivo.
4. Definir variáveis de ambiente para o tempo de execução do IoT Edge e outros módulos do IoT Edge no manifesto de implantação.

## <a name="know-your-proxy-url"></a>Saber a sua URL do proxy

Para configurar o daemon do Docker e o IoT Edge no seu dispositivo, você precisa conhecer sua URL de proxy.

Os URLs proxy usam o seguinte formato: **protocolo**: // **proxy_host**: **proxy_port**.

* O **protocolo** é HTTP ou HTTPS. O daemon do Docker pode ser usado com qualquer protocolo, dependendo das configurações do registro do contêiner, mas o daemon do IoT Edge e os contêineres de tempo de execução devem sempre usar HTTPS.

* O **proxy_host** é um endereço para o servidor proxy. Se o seu servidor proxy requerer autenticação, você pode fornecer suas credenciais como parte do proxy_host no formato do **usuário**: **senha**\@**proxy_host**.

* O **proxy_port** é a porta de rede em que o proxy responde ao tráfego de rede.

## <a name="install-the-runtime"></a>Instalar o tempo de execução

Se você estiver instalando o tempo de execução do IoT Edge em um dispositivo Linux, configure o gerenciador de pacotes para percorrer seu servidor proxy para acessar o pacote de instalação. Por exemplo, [Configurar o apt-get para usar um proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Depois que o gerenciador de pacotes estiver configurado, siga as instruções em [Instalar tempo de execução do Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) ou [Instalar o tempo de execução do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md) como de costume.

Se estiver instalando o tempo de execução do IoT Edge em um dispositivo Windows, será necessário passar pelo servidor proxy uma vez para fazer o download do arquivo de script do instalador e, em seguida, passar novamente durante a instalação para fazer o download dos componentes necessários. Você pode configurar informações de proxy nas configurações do Windows ou incluir suas informações de proxy diretamente no script de instalação. O seguinte script do Powershell é um exemplo de uma instalação do Windows usando o argumento `-proxy`:

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -proxy <proxy URL>
```

Se tiver credenciais complicadas para o servidor proxy que não podem ser incluídas na URL, use o `-ProxyCredential` parâmetro em `-InvokeWebRequestParameters`. Por exemplo,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Para obter mais informações sobre parâmetros de proxy, consulte [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Para obter mais informações sobre opções de instalação, consulte [Tempo de execução para a instalação do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

Depois que o tempo de execução do IoT Edge for instalado, use a seção a seguir para configurá-lo com suas informações de proxy. 

## <a name="configure-the-daemons"></a>Configurar os daemons

Os daemons Docker e IoT Edge em execução no dispositivo IoT Edge precisam ser configurados para usar o servidor proxy. O daemon do Docker faz solicitações da Web para extrair imagens de contêiner de registros de contêiner. O daemon do IoT Edge faz solicitações da Web para se comunicar com o Hub IoT.

### <a name="docker-daemon"></a>Daemon do docker

Consulte a documentação do Docker para configurar o daemon do Docker com variáveis de ambiente. A maioria dos registros de contêiner (incluindo DockerHub e Azure Container Registries) oferece suporte a solicitações HTTPS, portanto, o parâmetro que você deve definir é **HTTPS_PROXY**. Se você está obtendo imagens de um registro que não dão suporte ao protocolo TLS (TLS), você deve definir o parâmetro **HTTP_PROXY**. 

Escolha o artigo que se aplica à sua versão do Docker: 

* [Docker para Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Docker para Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)

### <a name="iot-edge-daemon"></a>Daemon do IoT Edge

O daemon do IoT Edge é configurado de maneira semelhante ao daemon do Docker. Todas as solicitações que o IoT Edge envia ao Hub IoT usam HTTPS. Use as etapas a seguir para definir uma variável de ambiente para o serviço, com base em seu sistema operacional. 

#### <a name="linux"></a>Linux

Abra um editor no terminal para configurar o daemon do IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Insira o texto a seguir, substituindo  **\<URL do proxy >** com seu endereço do servidor proxy e a porta. Em seguida, salve e saia. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Atualize o gerenciador de serviços para pegar a nova configuração do IoT Edge.

```bash
sudo systemctl daemon-reload
```

Reinicie o IoT Edge para que as alterações entrem em vigor.

```bash
sudo systemctl restart iotedge
```

Verifique se sua variável de ambiente foi criada e se a nova configuração foi carregada. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a> Windows

Abra uma janela do PowerShell como administrador e execute o seguinte comando para editar o registro com a nova variável de ambiente. Substitua **\<URL do proxy>** pelo endereço e porta do servidor proxy.
 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Reinicie o IoT Edge para que as alterações entrem em vigor.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configurar o dispositivo IoT Edge

O agente IoT Edge é o primeiro módulo a ser iniciado em qualquer dispositivo IoT Edge. É iniciado pela primeira vez com base nas informações do arquivo config.yaml do IoT Edge. Em seguida, o agente do IoT Edge se conecta ao Hub IoT para recuperar os manifestos de implantação, que declaram quais outros módulos devem ser implantados no dispositivo.

Abra o arquivo config.yaml no seu dispositivo IoT Edge. Nos sistemas Linux, esse arquivo está localizado em **/etc/iotedge/config.yaml**. Nos sistemas Windows, esse arquivo está localizado em **C: \ ProgramData \ iotedge \ config.yaml**. O arquivo de configuração é protegido, você precisa de privilégios administrativos para acessá-lo. Em sistemas Linux, isso significa usar o `sudo` comando antes de abrir o arquivo em seu editor de texto preferido. No Windows, isso significa abrir um editor de texto como o Bloco de Notas para ser executado como administrador e, em seguida, abrir o arquivo. 

No arquivo config.yaml, localize a seção **Especificação do módulo Edge Agent**. A definição do agente IoT Edge inclui um parâmetro **env**, no qual é possível incluir variáveis de ambiente. 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

Remova as chaves que são espaços reservados para o parâmetro env e inclua a nova variável em uma nova linha. Lembre-se de que os recuos em YAML são dois espaços. 

```yaml
https_proxy: "<proxy URL>"
```

O tempo de execução do IoT Edge usa o AMQP por padrão para falar com o IoT Hub. Alguns servidores proxy bloqueiam as portas AMQP. Se for esse o caso, você também precisará configurar o edgeAgent para usar o AMQP em WebSocket. Adicione uma segunda variável de ambiente.

```yaml
UpstreamProtocol: "AmqpWs"
```

![definição de edgeAgent com variáveis de ambiente](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Salve as alterações no config. YAML e feche o editor. Reinicie o IoT Edge para que as alterações entrem em vigor. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Configurar manifestos de implantação  

Depois que o dispositivo IoT Edge estiver configurado para funcionar com o servidor proxy, continue a declarar as variáveis de ambiente em todos os manifestos de implantação futuros. Sempre configure os dois módulos de tempo de execução: edgeAgent e edgeHub para se comunicar por meio do servidor proxy para que eles possam manter uma conexão com o Hub IoT. Outros módulos do IoT Edge que se conectam à internet devem ser configurados para o servidor proxy. No entanto, os módulos que roteiam as suas mensagens por meio do edgeHub ou que se comunicam somente com outros módulos no dispositivo não precisam dos detalhes do servidor proxy. 

Você pode criar manifestos de implantação usando o portal do Azure ou manualmente editando um arquivo JSON. 

### <a name="azure-portal"></a>Portal do Azure

Quando você usa o assistente **Configurar módulos** para criar implantações para dispositivos IoT Edge, cada módulo tem uma seção **Variáveis de Ambiente** que você pode usar para configurar conexões do servidor proxy. 

Para configurar o agente do IoT Edge e os módulos do hub do IoT Edge, selecione **Definir configurações avançadas do Edge Runtime** na primeira etapa do assistente. 

![Definir configurações avançadas de tempo de execução do Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Adicione a variável de ambiente **https_proxy** às definições do módulo de agente IoT Edge e do hub do IoT Edge. Se você incluiu a variável de ambiente **UpstreamProtocol** no arquivo config.yaml em seu dispositivo IoT Edge, inclua isso na definição do módulo do agente do IoT Edge também. 

![Defina a variável de ambiente https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Todos os outros módulos adicionados a um manifesto de implantação seguem o mesmo padrão. Na página em que você define o nome e a imagem do módulo, há uma seção de variáveis de ambiente.

### <a name="json-deployment-manifest-files"></a>Arquivos de manifesto de implantação de JSON

Se você criar implantações para dispositivos IoT Edge usando os modelos no Visual Studio Code ou criando manualmente arquivos JSON, poderá adicionar as variáveis de ambiente diretamente a cada definição de módulo. 

Use o seguinte formato JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Com as variáveis de ambiente incluídas, sua definição de módulo deve ser semelhante ao seguinte exemplo edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Se você incluiu a variável de ambiente **UpstreamProtocol** no arquivo confige.yaml em seu dispositivo IoT Edge, inclua isso na definição do módulo do agente do IoT Edge também. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as funções do tempo de execução do [IoT Edge](iot-edge-runtime.md).

Solucione problemas de erros de instalação e configuração com [problemas comuns e resoluções para o Azure IoT Edge](troubleshoot.md)

