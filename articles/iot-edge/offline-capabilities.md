---
title: Recursos offline do Azure IoT Edge | Microsoft Docs
description: Entenda como módulos e dispositivos do IoT Edge podem operar offline por longos períodos e como o IoT Edge pode permitir que dispositivos IoT regulares operem offline também.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ddc07db4e101bb16321478d17d84ffe0d30f0afd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946214"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Entender os recursos offline estendidos para dispositivos, módulos e dispositivos filho do IoT Edge (versão prévia)

O Azure IoT Edge é compatível com operações offline estendidas em seus dispositivos do IoT Edge e também permite operações offline em dispositivos filho que não são do Edge. Contanto que um dispositivo do IoT Edge tenha tido uma oportunidade de se conectar ao Hub IoT, ele e todos os dispositivos filho podem continuar a funcionar com uma conexão com a Internet intermitente ou sem nenhuma conexão. 

>[!NOTE]
>O suporte offline para o IoT Edge está em [versão prévia pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Como ele funciona

Quando um dispositivo do IoT Edge entra em modo offline, o hub do Edge assume três funções. Primeiro, ele armazena todas as mensagens que estão no sentido upstream e as salva até que o dispositivo se reconecte. Em segundo lugar, ele atua em nome do Hub IoT para autenticar dispositivos filho e módulos para que eles possam continuar a operar. Em terceiro lugar, ele permite a comunicação entre os dispositivos filho que normalmente passariam pelo Hub IoT. 

O exemplo a seguir mostra como um cenário de IoT Edge opera no modo offline:

1. Configure um dispositivo do IoT Edge. 

   Dispositivos do IoT Edge automaticamente têm recursos offline habilitados. Para estender essa funcionalidade para outros dispositivos de IoT, você precisa declarar uma relação de pai-filho entre os dispositivos no Hub IoT. 

2. Sincronize com o Hub IoT.

   Pelo menos uma vez após a instalação do tempo de execução do IoT Edge, o dispositivo do IoT Edge precisa estar online para sincronizar com o Hub IoT. Nessa sincronização, o dispositivo do IoT Edge obtém detalhes sobre todos os dispositivos filho atribuídos a ele. O dispositivo do IoT Edge também atualiza seu cache local com segurança para permitir operações offline e recupera as configurações para o armazenamento local de mensagens de telemetria. 

3. Fique offline. 

   Enquanto estiver desconectado do Hub IoT, o dispositivo do IoT Edge, seus módulos implantados e quaisquer dispositivos de IoT filho poderão operar indefinidamente. Os módulos e dispositivos filho podem ser iniciados e reiniciados ao autenticar com o hub do Edge enquanto estiverem offline. A telemetria upstream associada para o Hub IoT é armazenada localmente. A comunicação entre os módulos ou entre dispositivos de IoT filho é mantida por meio de métodos diretos ou mensagens. 

4. Reconecte e sincronize novamente com o Hub IoT.

   Quando a conexão com o Hub IoT for restaurada, o dispositivo do IoT Edge será sincronizado novamente. As mensagens armazenadas localmente são entregues na mesma ordem em que foram armazenadas. Todas as diferenças entre as propriedades desejadas e relatadas dos módulos e dispositivos são reconciliadas. O dispositivo do IoT Edge atualiza qualquer alteração de seu conjunto de dispositivos de IoT filho atribuídos.

## <a name="restrictions-and-limits"></a>Restrições e limites

Os recursos offline estendidos descritos neste artigo estão disponíveis no [IoT Edge versão 1.0.2 ou superior](https://github.com/Azure/azure-iotedge/releases). As versões anteriores têm um subconjunto de recursos offline. Os dispositivos do IoT Edge existentes que não têm recursos offline estendidos não podem ser atualizados alterando a versão de tempo de execução, mas devem ser reconfigurados com uma nova identidade do dispositivo do IoT Edge para obter esses recursos. 

O suporte estendido offline está disponível em todas as regiões em que o Hub IoT está disponível, exceto no Leste dos EUA e na Europa Ocidental. 

Somente dispositivos que não são do IoT Edge podem ser adicionados como dispositivos filho. 

Dispositivos do IoT Edge e seus dispositivos filho atribuídos podem funcionar indefinidamente offline após a sincronização inicial única. No entanto, o armazenamento de mensagens depende da configuração de TTL (vida útil) e do espaço em disco disponível para armazenar as mensagens. 

## <a name="set-up-an-edge-device"></a>Configurar um dispositivo do Edge

Para qualquer dispositivo do IoT Edge que você deseja executar durante longos períodos offline, configure o tempo de execução do IoT Edge para se comunicar por MQTT. 

Para um dispositivo do IoT Edge estender suas funcionalidades offline estendidas para dispositivos de IoT filho, você precisa declarar as relações de pai-filho no portal do Azure.

### <a name="set-the-upstream-protocol-to-mqtt"></a>Definir o protocolo de upstream para MQTT

Configure o hub do Edge e o agente do Edge para se comunicarem com o MQTT como o protocolo de upstream. Esse protocolo é declarado usando variáveis de ambiente no manifesto de implantação. 

No portal do Azure, você pode acessar as definições do módulo de agente do Edge e hub do Edge selecionando o botão **Definir configurações de tempo de execução do Edge avançadas** ao configurar os módulos para uma implantação. Para ambos os módulos, crie uma variável de ambiente chamada **UpstreamProtocol** e defina seu valor como **MQTT**. 

No modelo de implantação JSON, as variáveis de ambiente são declaradas como mostrado no exemplo a seguir: 

```json
"edgeAgent": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
        "createOptions": ""
    },
    "env": {
        "UpstreamProtocol": {
            "value": "MQTT"
        }
    },
}
```

### <a name="assign-child-devices"></a>Atribuir dispositivos filho

Dispositivos filho podem ser qualquer dispositivo que não é do Edge registrado no mesmo Hub IoT. Você pode gerenciar a relação pai-filho na criação de um novo dispositivo ou da página de detalhes do dispositivo do IoT Edge pai ou do filho. 

   ![Gerenciar dispositivos filho da página de detalhes do dispositivo do IoT Edge](./media/offline-capabilities/manage-child-devices.png)

Dispositivos pai podem ter vários dispositivos de filho, mas um dispositivo filho pode ter apenas um pai.

## <a name="optional-offline-settings"></a>Configurações offline opcionais

Se você espera que seus dispositivos passem longos períodos offline, após os quais quer coletar todas as mensagens que foram geradas, configure o hub do Edge para que ele possa armazenar todas as mensagens. Há duas alterações que você pode fazer ao hub do Edge para habilitar o armazenamento de mensagens de longo prazo. Primeiro aumente a configuração de tempo de vida útil e adicione espaço em disco adicional para o armazenamento de mensagens. 

### <a name="time-to-live"></a>Vida útil

A configuração de vida útil é a quantidade de tempo (em segundos) que uma mensagem pode esperar para ser entregue antes de expirar. O padrão é 7200 segundos (duas horas). 

Essa configuração é uma propriedade desejada do hub do Edge, que é armazenada no gêmeo do módulo. Você pode configurá-la no portal do Azure, na seção **Definir configurações avançadas do tempo de execução do Edge** ou diretamente no manifesto de implantação. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>Armazenamento offline adicional

Por padrão, as mensagens são armazenadas no sistema de arquivos de contêiner do hub do Edge. Se essa quantidade de armazenamento não for suficiente para suas necessidades offline, você poderá dedicar o armazenamento local no dispositivo do IoT Edge. Você precisará criar uma variável de ambiente para o hub do Edge que aponte para uma pasta de armazenamento no contêiner. Em seguida, use as opções de criação para associar essa pasta de armazenamento a uma pasta no computador host. 

Você pode configurar variáveis de ambiente e as opções de criação para o módulo de hub do Edge no portal do Azure na seção **Definir configurações avançadas do tempo de execução do Edge**. Ou, você pode configurá-las diretamente no manifesto de implantação. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"C:\\\\HostStoragePath:C:\\\\ModuleStoragePath\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "storageFolder": {
            "value": "C:\\\\ModuleStoragePath"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="next-steps"></a>Próximas etapas

Habilite as operações offline estendidas em seus cenários de gateway transparente para dispositivos [Linux](how-to-create-transparent-gateway-linux.md) ou [Windows](how-to-create-transparent-gateway-windows.md). 