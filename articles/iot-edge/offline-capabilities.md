---
title: Operar dispositivos offline – Azure IoT Edge | Microsoft Docs
description: Saiba como módulos e dispositivos IoT Edge podem operar sem conexão à Internet por longos períodos e como o IoT Edge pode permitir que dispositivos IoT regulares operem offline também.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 74d2601c2319ccad9cc980b83894a3242705aa46
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148112"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Entender os recursos estendidos de offline para dispositivos do IoT Edge, módulos e dispositivos filho

O Azure IoT Edge é compatível com operações offline estendidas em seus dispositivos do IoT Edge e também permite operações offline em dispositivos filho que não são do Edge. Contanto que um dispositivo do IoT Edge tenha tido uma oportunidade de se conectar ao Hub IoT, ele e todos os dispositivos filho podem continuar a funcionar com uma conexão com a Internet intermitente ou sem nenhuma conexão. 


## <a name="how-it-works"></a>Como ele funciona

Quando um dispositivo do IoT Edge entra em modo offline, o hub do IoT Edge assume três funções. Primeiro, ele armazena todas as mensagens que estão no sentido upstream e as salva até que o dispositivo se reconecte. Em segundo lugar, ele atua em nome do Hub IoT para autenticar dispositivos filho e módulos para que eles possam continuar a operar. Em terceiro lugar, ele permite a comunicação entre os dispositivos filho que normalmente passariam pelo Hub IoT. 

O exemplo a seguir mostra como um cenário de IoT Edge opera no modo offline:

1. **Configurar um dispositivo IoT Edge.**

   Dispositivos do IoT Edge automaticamente têm recursos offline habilitados. Para estender essa funcionalidade para outros dispositivos de IoT, você precisa declarar uma relação de pai-filho entre os dispositivos no Hub IoT. 

2. **Sincronizar com o Hub IoT.**

   Pelo menos uma vez após a instalação do tempo de execução do IoT Edge, o dispositivo do IoT Edge precisa estar online para sincronizar com o Hub IoT. Nessa sincronização, o dispositivo do IoT Edge obtém detalhes sobre todos os dispositivos filho atribuídos a ele. O dispositivo do IoT Edge também atualiza seu cache local com segurança para permitir operações offline e recupera as configurações para o armazenamento local de mensagens de telemetria. 

3. **Ficar offline.**

   Enquanto estiver desconectado do Hub IoT, o dispositivo do IoT Edge, seus módulos implantados e quaisquer dispositivos de IoT filho poderão operar indefinidamente. Os módulos e dispositivos filho podem ser iniciados e reiniciados ao autenticarem com o hub do IoT Edge enquanto estiverem offline. A telemetria upstream associada para o Hub IoT é armazenada localmente. A comunicação entre os módulos ou entre dispositivos de IoT filho é mantida por meio de métodos diretos ou mensagens. 

4. **Reconectar e sincronizar novamente com o Hub IoT.**

   Quando a conexão com o Hub IoT for restaurada, o dispositivo do IoT Edge será sincronizado novamente. As mensagens armazenadas localmente são entregues na mesma ordem em que foram armazenadas. Todas as diferenças entre as propriedades desejadas e relatadas dos módulos e dispositivos são reconciliadas. O dispositivo do IoT Edge atualiza qualquer alteração de seu conjunto de dispositivos de IoT filho atribuídos.

## <a name="restrictions-and-limits"></a>Restrições e limites

Os recursos off-line estendidos descritos neste artigo estão disponíveis no [IoT Edge versão 1.0.4 ou superior](https://github.com/Azure/azure-iotedge/releases). As versões anteriores têm um subconjunto de recursos offline. Os dispositivos do IoT Edge existentes que não têm recursos offline estendidos não podem ser atualizados alterando a versão de tempo de execução, mas devem ser reconfigurados com uma nova identidade do dispositivo do IoT Edge para obter esses recursos. 

O suporte offline estendido está disponível em todas as regiões onde o Hub IoT está disponível, **exceto** leste dos EUA.

Somente dispositivos que não são do IoT Edge podem ser adicionados como dispositivos filho. 

Dispositivos do IoT Edge e seus dispositivos filho atribuídos podem funcionar indefinidamente offline após a sincronização inicial única. No entanto, o armazenamento de mensagens depende da configuração de TTL (vida útil) e do espaço em disco disponível para armazenar as mensagens. 

## <a name="set-up-an-iot-edge-device"></a>Configurar um dispositivo IoT Edge

Para um dispositivo do IoT Edge estender suas funcionalidades offline estendidas para dispositivos de IoT filho, você precisa declarar as relações de pai-filho no portal do Azure.

### <a name="assign-child-devices"></a>Atribuir dispositivos filho

Dispositivos filho podem ser qualquer dispositivo que não é do Edge registrado no mesmo Hub IoT. Dispositivos pai podem ter vários dispositivos de filho, mas um dispositivo filho pode ter apenas um pai. Há três opções para definir os dispositivos de filho para um dispositivo de borda:

#### <a name="option-1-iot-hub-portal"></a>Opção 1: Portal do Hub IoT

 Você pode gerenciar a relação pai-filho na criação de um novo dispositivo ou da página de detalhes do dispositivo do IoT Edge pai ou do filho. 

   ![Gerenciar dispositivos filho da página de detalhes do dispositivo do IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opção 2: Use o `az` ferramenta de linha de comando

Usando o [interface de linha de comando do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) com [extensão de IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 ou mais recente), você pode gerenciar relações pai-filho com o [identidade do dispositivo](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) subcomandos. No exemplo a seguir, podemos executar uma consulta para atribuir dispositivos no hub do IoT Edge em todos os não como dispositivos de filho de um dispositivo IoT Edge. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

Você pode modificar os [consulta](../iot-hub/iot-hub-devguide-query-language.md) para selecionar um subconjunto diferente de dispositivos. O comando pode levar vários segundos se você especificar um grande conjunto de dispositivos.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opção 3: Usar o SDK do serviço de Hub IoT 

Por fim, você pode gerenciar relações pai-filho por meio de programação usando o C#, Java ou do SDK do serviço de Hub IoT do Node. js. Aqui está uma [exemplo de atribuição de um dispositivo filho](https://aka.ms/set-child-iot-device-c-sharp) usando o C# SDK.

### <a name="specifying-dns-servers"></a>Especificar servidores DNS 

Para aumentar a robustez, é altamente recomendável que você especificar os endereços de servidor DNS usados em seu ambiente. Consulte a [duas opções para fazer isso no artigo de solução de problemas](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Configurações offline opcionais

Se você pretende coletar todas as mensagens que seus dispositivos geram durante longos períodos de tempo offline, configure o hub do IoT Edge para que ele possa armazenar todas as mensagens. Há duas alterações que você pode fazer no hub do IoT Edge para habilitar o armazenamento de mensagens de longo prazo. Primeiro, aumente a configuração de vida útil. Em seguida, adicione mais espaço em disco para o armazenamento de mensagens. 

### <a name="time-to-live"></a>Vida útil

A configuração de vida útil é a quantidade de tempo (em segundos) que uma mensagem pode esperar para ser entregue antes de expirar. O padrão é 7200 segundos (duas horas). O valor máximo é limitado somente pelo valor máximo de uma variável de inteiro, que é aproximadamente 2 bilhões. 

Essa configuração é uma propriedade desejada do hub do IoT Edge, que é armazenada no gêmeo do módulo. Você pode configurá-la no portal do Azure, na seção **Definir configurações avançadas do tempo de execução do Edge** ou diretamente no manifesto de implantação. 

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

As mensagens são armazenadas por padrão no sistema de arquivos de contêiner do hub do IoT Edge. Se essa quantidade de armazenamento não for suficiente para suas necessidades offline, você poderá dedicar o armazenamento local no dispositivo do IoT Edge. Crie uma variável de ambiente para o hub do IoT Edge que aponte para uma pasta de armazenamento no contêiner. Em seguida, use as opções de criação para associar essa pasta de armazenamento a uma pasta no computador host. 

Você pode configurar variáveis de ambiente e as opções de criação para o módulo do hub do IoT Edge no portal do Azure na seção **Definir configurações avançadas do tempo de execução do Edge**. Ou, você pode configurá-las diretamente no manifesto de implantação. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Substitua `<HostStoragePath>` e `<ModuleStoragePath>` pelo seu caminho de armazenamento de host e módulo; o caminho de armazenamento do host e do módulo deve ser um caminho absoluto. Nas opções de criação, associe os caminhos de armazenamento do host e do módulo. Em seguida, crie uma variável de ambiente que aponta para o caminho de armazenamento do módulo.  

Por exemplo, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` significa que o diretório **/etc/iotedge/storage** no host do sistema é mapeado para o diretório **/iotedge/storage/** no contêiner. Ou outro exemplo de sistemas Windows: `"Binds":["C:\\temp:C:\\contemp"]` significa que o diretório **C:\\temp** no sistema do host é mapeado para o diretório **C:\\contemp** no contêiner. 

Você também pode encontrar mais detalhes sobre opções de criação nos [documentos do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Próximas etapas

Ative operações offline estendidas em seus [cenários de gateway transparente](how-to-create-transparent-gateway.md).
