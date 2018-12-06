---
title: Prepare-se para uma solução de produção do Azure IoT Edge | Microsoft Docs
description: Saiba como levar sua solução do Azure IoT Edge do desenvolvimento à produção, incluindo a configuração de seus dispositivos com os certificados apropriados e a elaboração de um plano de implantação para futuras atualizações de código.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48fd774d6d887a12ed1c1ad9689ca34f5d866367
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585369"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Prepare-se para implantar sua solução IoT Edge em produção

Quando você estiver pronto para levar sua solução IoT Edge do desenvolvimento para a produção, verifique se ela está configurada para desempenho contínuo.

As informações fornecidas neste artigo não são todas iguais. Para ajudá-lo a priorizar, cada seção começa com listas que dividem o trabalho em duas seções: **importante** para ser concluído antes da produção ou **útil** para você saber.

## <a name="device-configuration"></a>Configuração do dispositivo

Os dispositivos IoT Edge podem ser de um Raspberry Pi a um laptop para uma máquina virtual em execução em um servidor. Você pode ter acesso ao dispositivo fisicamente ou por meio de uma conexão virtual ou pode ficar isolado por longos períodos de tempo. De qualquer forma, você quer ter certeza de que está configurado para funcionar adequadamente. 

* **Importante**
    * Instalar certificados de produção
    * Tenha um plano de gerenciamento de dispositivos
    * Use o Moby como o mecanismo de contêiner

* **Utilidade**
    * Escolha o protocolo upstream

### <a name="install-production-certificates"></a>Instalar certificados de produção

Cada dispositivo IoT Edge em produção precisa de um certificado de autoridade de certificação (CA) do dispositivo instalado. Esse certificado de CA é então declarado para o tempo de execução do IoT Edge no arquivo config.yaml. Para facilitar o desenvolvimento e o teste, o tempo de execução do IoT Edge cria certificados temporários se nenhum certificado for declarado no arquivo config.yaml. No entanto, esses certificados temporários expiram após três meses e não são seguros para cenários de produção. 

Para reconhecer a função do certificado de CA do dispositivo, consulte [Como o IoT Edge do Azure usa certificados](iot-edge-certs.md).

Para obter mais informações sobre como instalar certificados em um dispositivo IoT Edge e referenciá-los no arquivo config.yaml, consulte [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md). As etapas para configurar os certificados são as mesmas, independentemente de o dispositivo ser usado como gateway ou não. Esse artigo fornece scripts para gerar certificados de amostra somente para teste. Não use esses certificados de amostra em produção. 

### <a name="have-a-device-management-plan"></a>Tenha um plano de gerenciamento de dispositivos

Antes de colocar qualquer dispositivo em produção, você deve saber como gerenciar futuras atualizações. Para um dispositivo IoT Edge, a lista de componentes a atualizar pode incluir:

* Firmware do dispositivo
* Bibliotecas do sistema operacional
* Mecanismo de contêiner, como Moby
* Daemon do IoT Edge
* Certificados de AC

Para conhecer as etapas para atualizar o daemon do IoT Edge, consulte [Atualizar o tempo de execução do IoT Edge](how-to-update-iot-edge.md). Os métodos atuais para atualizar o daemon IoT Edge exigem acesso físico ou SSH ao dispositivo IoT Edge. Se você tiver muitos dispositivos para atualizar, considere adicionar as etapas de atualização a um script ou use uma ferramenta de automação como Ansible para executar atualizações em escala.

### <a name="use-moby-as-the-container-engine"></a>Use o Moby como o mecanismo de contêiner

Ter um mecanismo de contêiner em um dispositivo é um pré-requisito para qualquer dispositivo IoT Edge. Apenas o Moby-Engine é suportado na produção. Outros mecanismos de contêiner, como o Docker, funcionam com o IoT Edge e não há problema em usar esses mecanismos para desenvolvimento. O mecanismo de moby pode ser redistribuído quando usado com o Azure IoT Edge e a Microsoft fornece serviços para esse mecanismo. O uso de outros mecanismos de contêiner em um dispositivo IoT Edge não é suportado.

### <a name="choose-upstream-protocol"></a>Escolha o protocolo upstream

O protocolo (e, portanto, a porta usada) para comunicação upstream para o Hub IoT pode ser configurado para o agente Azure Data Box Edge o hub do Azure Data Box Edge. O protocolo padrão é AMQP, mas você pode querer mudar isso dependendo da sua configuração de rede. 

Os dois módulos de tempo de execução possuem uma variável de ambiente **UpstreamProtocol**. Os valores válidos para a variável são: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure a variável UpstreamProtocol para o agente Edge no arquivo config.yaml no próprio dispositivo. Por exemplo, se o seu dispositivo IoT Edge estiver atrás de um servidor proxy que bloqueia as portas AMQP, pode ser necessário configurar o agente de borda para usar AMQP sobre WebSocket (AMQPWS) para estabelecer a conexão inicial com o Hub IoT. 

Depois que o dispositivo IoT Edge se conectar, certifique-se de continuar configurando a variável UpstreamProtocol para os dois módulos de tempo de execução em implantações futuras. Um exemplo desse processo é fornecido em [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Implantação

* **Utilidade**
    * Seja consistente com o protocolo upstream
    * Reduza o espaço de memória usado pelo hub Edge
    * Não use versões de depuração de imagens de módulo

### <a name="be-consistent-with-upstream-protocol"></a>Seja consistente com o protocolo upstream

Se você configurou o agente de borda em seu dispositivo IoT Edge para usar um protocolo diferente do padrão AMQP, deverá declarar o mesmo protocolo em todas as implantações subsequentes. Por exemplo, se o seu dispositivo IoT Edge estiver atrás de um servidor proxy que bloqueia portas AMQP, você provavelmente configurou o dispositivo para conectar-se por AMQP sobre WebSocket (AMQPWS). Quando você implanta módulos no dispositivo, se não configurar o mesmo protocolo APQPWS para o agente do Azure Data Box Edge e o hub do Azure Data Box Edge, o AMQP padrão substituirá as configurações e impedirá a conexão novamente. 

Você só precisa configurar a variável de ambiente UpstreamProtocol para os módulos Edge Agent e Edge. Quaisquer módulos adicionais adotam qualquer protocolo que esteja configurado nos módulos de tempo de execução. 

Um exemplo desse processo é fornecido em [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Reduza o espaço de memória usado pelo hub Edge

Se você estiver implantando dispositivos restritos com memória limitada disponível, poderá configurar o hub do Azure Stream Analytics no IoT Edge para ser executado em uma capacidade mais simplificada e usar menos espaço em disco. No entanto, essas configurações limitam o desempenho do hub do Edge, portanto, encontre o equilíbrio correto que funcione para sua solução. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Não otimize o desempenho em dispositivos restritos

O hub Edge é otimizado para desempenho por padrão, por isso ele tenta alocar grandes blocos de memória. Essa configuração pode causar problemas de estabilidade em dispositivos menores, como o Raspberry Pi. Se você estiver implantando dispositivos com recursos restritos, poderá definir a variável de ambiente **OptimizeForPerformance** como **false** no hub Edge. 

Para obter mais informações, consulte [Problemas de estabilidade em dispositivos restritos por recursos](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Desabilitar protocolos não utilizados

Outra maneira de otimizar o desempenho do hub do Edge e reduzir seu uso de memória é desativar as cabeças de protocolo em busca de protocolos que você não esteja usando em sua solução. 

Cabeçalhos de protocolo são configurados definindo variáveis de ambiente booleano para o módulo de hub do Azure Stream Analytics no IoT Edge em seus manifestos de implantação. As três variáveis são:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Todas as três variáveis possuem *dois sublinhados* e podem ser configuradas como verdadeiras ou falsas. 

#### <a name="reduce-storage-time-for-messages"></a>Reduza o tempo de armazenamento de mensagens

O módulo hub do Azure Data Box Edge armazena as mensagens temporariamente se elas não puderem ser entregues ao Hub IoT por qualquer motivo. Você pode configurar por quanto tempo o hub Edge se mantém em mensagens não entregues antes de expirar. Se você tiver preocupações de memória em seu dispositivo, poderá diminuir o valor de **timeToLiveSecs** no gêmeo do módulo hub do Azure Data Box Edge. 

O valor padrão do parâmetro timeToLiveSecs é de 7200 segundos, ou seja, duas horas. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Não use versões de depuração de imagens de módulo

Ao passar de cenários de teste para cenários de produção, lembre-se de remover as configurações de depuração dos manifestos de implantação. Verifique se nenhuma das imagens do módulo nos manifestos de implantação possui o sufixo **\.debug**. Se você adicionou criar opções para expor portas nos módulos para depuração, remova essas opções de criação também. 

## <a name="container-management"></a>Gerenciamento de contêiner

* **Importante**
    * Gerenciar o acesso ao seu registro de contêiner
    * Use tags para gerenciar versões

### <a name="manage-access-to-your-container-registry"></a>Gerenciar o acesso ao seu registro de contêiner

Antes de implantar módulos nos dispositivos de produção do IoT Edge, assegure-se de controlar o acesso ao seu registro de contêiner para que pessoas de fora não possam acessar ou fazer alterações em suas imagens de contêiner. Use um registro de contêiner privado, não público, para gerenciar imagens de contêiner. 

Nos tutoriais e em outra documentação, instruímos você a usar as mesmas credenciais de registro de contêiner no dispositivo IoT Edge que você usa na sua máquina de desenvolvimento. Essas instruções destinam-se apenas a ajudá-lo a configurar ambientes de teste e desenvolvimento mais facilmente e não devem ser seguidas em um cenário de produção. O Registro de Contêiner do Azure [autenticação com entidades de serviço](../container-registry/container-registry-auth-service-principal.md) quando aplicativos ou serviços extraem imagens de contêiner de maneira automática ou não assistida, como fazem os dispositivos IoT Edge. Crie uma entidade de serviço com acesso somente leitura ao seu registro de contêiner e forneça esse nome de usuário e senha no manifesto de implantação.

### <a name="use-tags-to-manage-versions"></a>Use tags para gerenciar versões

Uma tag é um conceito do Docker que você pode usar para distinguir entre as versões dos contêineres do Docker. As tags são sufixos como **1.0**, que vão no final de um repositório contêiner. Por exemplo, **mcr.microsoft.com/azureiotedge-agent:1.0**. As tags são mutáveis e podem ser alteradas para apontar para outro contêiner a qualquer momento, portanto, sua equipe deve concordar com uma convenção a seguir à medida que você atualiza as imagens do seu módulo no futuro. 

As tags também ajudam a impor atualizações nos seus dispositivos IoT Edge. Quando você envia uma versão atualizada de um módulo para seu registro de contêiner, incremente a tag. Em seguida, envie uma nova implantação para seus dispositivos com a tag incrementada. O mecanismo do contêiner reconhecerá a tag incrementada como uma nova versão e baixará a versão mais recente do módulo para o seu dispositivo. 

Para obter um exemplo de uma convenção de tag, consulte [ Atualizar o tempo de execução do IoT Edge ](how-to-update-iot-edge.md#understand-iot-edge-tags) para saber como o IoT Edge usa tags de rolagem e tags específicas para rastrear versões. 

## <a name="networking"></a>Rede

* **Utilidade**
    * Revisar configuração de entrada/saída
    * Adicionar as conexões
    * Configurar a comunicação por meio de um proxy

### <a name="review-outboundinbound-configuration"></a>Revisar configuração de entrada/saída

Os canais de comunicação entre o Hub IoT e o IoT Edge são sempre configurados para serem enviados. Para a maioria dos cenários do IoT Edge, apenas três conexões são necessárias. O mecanismo de contêiner precisa se conectar ao registro do contêiner (ou registros) que contém as imagens do módulo. O tempo de execução do IoT Edge precisa se conectar ao Hub IoT para recuperar informações de configuração do dispositivo e enviar mensagens e telemetria. E, se você usar o provisionamento automático, o daemon do IoT Edge precisará se conectar ao Serviço de provisionamento de dispositivos. Para obter mais informações, consulte [Regras de configuração de firewall e porta](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Adicionar as conexões

Se a sua configuração de rede exigir a inclusão explícita de conexões feitas a partir de dispositivos IoT Edge, analise a seguinte lista de componentes do IoT Edge:

* **O agente do IoT Edge** abre uma conexão persistente AMQP / MQTT ao Hub IoT, possivelmente por meio de WebSockets. 
* **O hub IoT Edge** abre uma única conexão AMQP persistente ou várias conexões MQTT para o Hub IoT, possivelmente por meio de WebSockets. 
* **O daemon do IoT Edge** faz chamadas de intermitente HTTPS para o Hub IoT. 

Em todos os três casos, o nome DNS deve corresponder ao padrão \*Azure-Devices.NET. 

Além disso, o **mecanismo do Container** faz chamadas para registros de contêiner por HTTPS. Para recuperar as imagens do contêiner de tempo de execução IoT Edge, o nome DNS é mcr.microsoft.com. O mecanismo de contêiner se conecta a outros registros conforme configurado na implantação. 

### <a name="configure-communication-through-a-proxy"></a>Configurar a comunicação por meio de um proxy

Se os dispositivos forem implantados em uma rede que usa um servidor proxy, eles precisarão se comunicar por meio do proxy para acessar o Hub IoT e os registros de contêiner. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gerenciamento de soluções

* **Utilidade**
    * Configurar logs e diagnósticos
    * Considere testes e pipelines de CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Configurar logs e diagnósticos

No Linux, o daemon do IoT Edge usa o journald como o driver de registro padrão. Você pode usar a ferramenta de linha de comando `journalctl` para consultar os logs do daemon. No Windows, o daemon IoT Edge usa diagnósticos do PowerShell. Use `Get-WinEvent` para consultar logs do daemon. Os módulos da IoT Edge usam o driver JSON para criação de log, que é o padrão do Docker.  

Ao testar uma implantação do IoT Edge, você geralmente pode acessar seus dispositivos para recuperar logs e solucionar problemas. Em um cenário de implantação, talvez você não tenha essa opção. Pense em como você coletará informações sobre seus dispositivos em produção. Considere como você coletará informações sobre seus dispositivos em produção. Uma opção é usar um módulo de registro que coleta informações dos outros módulos e as envia para a nuvem. Um exemplo de um módulo de registro é [log-logan-analytics](https://github.com/veyalla/logspout-loganalytics), ou você pode criar o seu próprio. 

Se você estiver preocupado com o fato de os logs se tornarem muito grandes em um dispositivo com recursos restritos, você terá algumas opções para reduzir o uso da memória. 

* Você pode limitar especificamente o tamanho de todos os arquivos de log do docker no próprio daemon do Docker. Para Linux, configure o daemon em `/etc/docker/daemon.json`. Para Windows, `C:\ProgramData\docker\confige\daemon.json`. 
* Se você deseja ajustar o tamanho do arquivo de log para cada contêiner, você pode fazer isso no CreateOptions de cada módulo. 
* Configure o Docker para gerenciar automaticamente os logs definindo journald como o driver de log padrão do Docker. 
* Periodicamente, remova logs antigos do seu dispositivo instalando uma ferramenta de logrotate para o Docker. Use a seguinte especificação de arquivo: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Considere testes e pipelines de CI/CD

Para o cenário de implantação do IoT Edge mais eficiente, considere a integração de sua implantação de produção em seus pipelines de teste e CI/CD. O Azure IoT Edge suporta várias plataformas de CI/CD, incluindo o DevOps do Azure. Para obter mais informações, consulte [Integração contínua e implantação contínua no Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [implantação automática do IoT Edge](module-deployment-monitoring.md).
* Veja como o IoT Edge suporta [Integração contínua e implantação contínua](how-to-ci-cd.md).
