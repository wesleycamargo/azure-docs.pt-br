---
title: "Composição de módulo do Azure IoT Edge | Microsoft Docs"
description: "Saiba o que há nos módulos do Azure IoT Edge e como eles podem ser reutilizados"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5de67b6f1ce79934a3a6aab623d2e77a56a8ce76
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Noções básicas sobre como os módulos do IoT Edge podem ser usados, configurados e reutilizados - versão prévia

O Azure IoT Edge permite compor vários módulos do IoT Edge antes de implantá-los nos dispositivos IoT Edge. Este artigo explica os conceitos mais importantes para compor vários módulos do IoT Edge antes da implantação. 

## <a name="the-deployment-manifest"></a>O manifesto de implantação
O *manifesto de implantação* é um documento JSON que descreve:

* Quais módulos do IoT Edge precisam ser implantados, juntamente com suas opções de gerenciamento e criação;
* A configuração do hub do Edge, que descreve como as mensagens devem fluir entre módulos e entre os módulos e o Hub IoT;
* Opcionalmente, os valores a serem definidos nas propriedades desejadas dos gêmeos dos módulos para configurar os aplicativos de módulo individuais.

Nos tutoriais do Azure IoT Edge, você pode criar um manifesto de implantação por meio de um assistente no portal do Azure IoT Edge. Você também pode aplicar um manifesto de implantação por meio de programação usando REST ou o SDK do serviço Hub IoT. Confira [Implantar e monitorar][lnk-deploy] para saber mais sobre implantações do IoT Edge.

De forma geral, o manifesto de implantação configura as propriedades desejadas de um módulo gêmeo para módulos do IoT Edge implantados em um dispositivo IoT Edge. Dois desses módulos estão sempre presentes: o agente do Edge e o hub do Edge.

O manifesto segue esta estrutura:

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
                }
            },
            "{module1}": {  // optional
                "properties.desired": {
                    // desired properties of module with id {module1}
                }
            },
            "{module2}": {  // optional
                ...
            },
            ...
        }
    }

Um exemplo de manifesto de implantação é indicado no final desta seção.

> [!IMPORTANT]
> Todos os dispositivos IoT Edge precisam ser configurados com um manifesto de implantação. Um tempo de execução do IoT Edge recém-instalado relatará um código de erro até ser configurado com um manifesto válido. 

### <a name="specify-the-modules"></a>Especificar os módulos
As propriedades desejadas do gêmeo do módulo do agende do Edge contêm: os módulos desejados, as opções de configuração e gerenciamento, juntamente com os parâmetros de configuração para o agente do Edge.

De maneira geral, esta seção do manifesto contém referências a imagens de módulo e opções de gerenciamento para os módulos do tempo de execução do IoT Edge (agente do Edge e hub do Edge) e os módulos especificados pelo usuário.

Confira o [propriedades do agente do Edge desejadas][lnk-edgeagent-desired] para obter uma descrição detalhada da seção de manifesto.

> [!NOTE]
> Um manifesto de implantação que contém o tempo de execução do IoT Edge (hub e agente) é válido.


### <a name="specify-the-routes"></a>Especificar as rotas
O hub do Edge fornece uma maneira para rotear de modo declarativo as mensagens entre módulos e entre os módulos e o Hub IoT.

As rotas têm a seguinte sintaxe:

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

A *fonte* pode ser qualquer uma dos seguintes:

| Fonte | DESCRIÇÃO |
| ------ | ----------- |
| `/*` | Todas as mensagens de dispositivo para nuvem de qualquer dispositivo ou módulo |
| `/messages/*` | Qualquer mensagem de dispositivo para nuvem enviada por um dispositivo ou por um módulo por meio de algumas ou nenhuma saída |
| `/messages/modules/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo por meio de algumas ou nenhuma saída |
| `/messages/modules/{moduleId}/*` | Qualquer mensagem de dispositivo para nuvem enviada por {Idmódulo} sem saída |
| `/messages/modules/{moduleId}/outputs/*` | Qualquer mensagem de dispositivo para nuvem enviada por {IDmódulo} com algumas saídas |
| `/messages/modules/{moduleId}/outputs/{output}` | Qualquer mensagem de dispositivo para nuvem enviada por {IDmódulo} usando {saída} |

A condição pode ser qualquer condição que tenha suporte pela [linguagem de consulta do Hub IoT][lnk-iothub-query] para regras de roteamento do Hub IoT.

O coletor pode ser um dos seguintes:

| Coletor | DESCRIÇÃO |
| ---- | ----------- |
| `$upstream` | Enviar a mensagem para o Hub IoT |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Enviar a mensagem para a entrada `{input}` do módulo`{moduleId}` |

É importante observar que o hub do Edge oferece garantias de pelo menos uma vez, o que significa que as mensagens serão armazenadas localmente caso uma rota não possa entregar a mensagem para o coletor, por exemplo, o hub do Edge não pode se conectar ao Hub IoT ou o módulo de destino não está conectado.

O hub do Edge armazena as mensagens até à hora especificada na propriedade `storeAndForwardConfiguration.timeToLiveSecs` das propriedades desejadas do hub do Edge.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>Especificar as propriedades desejadas do gêmeo de módulo

O manifesto de implantação pode especificar as propriedades desejadas do gêmeo de módulo de cada um dos módulos de usuário especificados na seção de agente do Edge.

Quando as propriedades desejadas são especificadas no manifesto de implantação, substituem qualquer propriedade desejada atualmente no gêmeo do módulo.

Se você não especificar as propriedades desejadas de um gêmeo de módulo no manifesto de implantação, o Hub IoT não modifica o gêmeo de módulo de qualquer forma, e você poderá definir as propriedades desejadas programaticamente.

Os mesmos mecanismos que permitem que você modifique dispositivos gêmeos são usados para modificar módulos gêmeos. Consulte o [guia do desenvolvedor do dispositivo gêmeo](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins) para obter mais informações.   

### <a name="deployment-manifest-example"></a>Exemplo de manifesto de implantação

Este é um exemplo de um documento JSON do manifesto de implantação.

    {
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "runtime": {
                    "type": "docker",
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "loggingOptions": ""
                    }
                },
                "systemModules": {
                    "edgeAgent": {
                        "type": "docker",
                        "settings": {
                        "image": "microsoft/azureiotedge-agent:1.0-preview",
                        "createOptions": ""
                        }
                    },
                    "edgeHub": {
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-hub:1.0-preview",
                        "createOptions": ""
                        }
                    }
                },
                "modules": {
                    "tempSensor": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                        "createOptions": "{}"
                        }
                    },
                    "filtermodule": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "myacr.azurecr.io/filtermodule:latest",
                        "createOptions": "{}"
                        }
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "routes": {
                    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
                },
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 10
                }
            }
        }
    }
    }

## <a name="reference-edge-agent-module-twin"></a>Referência: gêmeo de módulo do agente do Edge

O gêmeo do módulo para o agente do Edge se chama `$edgeAgent` e coordena as comunicações entre o agente do Edge em execução em um dispositivo e o Hub IoT.
As propriedades desejadas são definidas durante a aplicação de um manifesto de implantação em um dispositivo específico como parte de uma implantação de dispositivo único ou em escala. Confira [Implantação e Monitoramento][lnk-deploy] para obter mais informações sobre como implantar módulos em dispositivos do IoT Edge.

### <a name="edge-agent-twin-desired-properties"></a>Propriedades desejadas do gêmeo do agente do Edge

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| schemaVersion | Tem que ser "1.0" | sim |
| runtime.type | Tem que ser "docker" | sim |
| runtime.settings.minDockerVersion | Definido para a versão mínima do Docker necessária para o manifesto de implantação | sim |
| runtime.settings.loggingOptions | Um JSON em cadeias de caracteres que contém as opções de registro para o contêiner do agente do Edge. [Opções de registro em log do Docker][lnk-docker-logging-options] | Não  |
| systemModules.edgeAgent.type | Tem que ser "docker" | sim |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente do Edge. Atualmente, o agente do Edge não é capaz de se atualizar. | sim |
| systemModules.edgeAgent.settings.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do agente do Edge. [Opções de criação de docker][lnk-docker-create-options] | Não  |
| systemModules.edgeAgent.configuration.id | A ID da implantação que implantou este módulo. | Isso é definido pelo Hub IoT quando esse manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| systemModules.edgeHub.type | Tem que ser "docker" | sim |
| systemModules.edgeHub.status | Deve estar "em execução" | sim |
| systemModules.edgeHub.restartPolicy | Deve estar "sempre" | sim |
| systemModules.edgeHub.settings.image | O URI da imagem do hub do Edge. | sim |
| systemModules.edgeHub.settings.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do hub do Edge. [Opções de criação de docker][lnk-docker-create-options] | Não  |
| systemModules.edgeHub.configuration.id | A ID da implantação que implantou este módulo. | Isso é definido pelo Hub IoT quando esse manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| modules.{moduleId}.version | Uma cadeia definida pelo usuário que representa a versão desse módulo. | sim |
| modules.{moduleId}.type | Tem que ser "docker" | sim |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | sim |
| modules.{moduleId}.settings.image | O URI para a imagem do módulo. | sim |
| modules.{moduleId}.settings.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do módulo. [Opções de criação de docker][lnk-docker-create-options] | Não  |
| modules.{moduleId}.configuration.id | A ID da implantação que implantou este módulo. | Isso é definido pelo Hub IoT quando esse manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |

### <a name="edge-agent-twin-reported-properties"></a>Propriedades relatadas do gêmeo do agente do Edge

As propriedades relatadas pelo agente do Edge incluem três partes de informação principais:

1. O status do aplicativo das propriedades desejadas vistas pela última vez;
2. O status dos módulos em execução no momento no dispositivo, conforme relatado pelo agente de borda; e
3. Uma cópia das propriedades desejadas atualmente em execução no dispositivo.

Esta última informação é útil no caso de as propriedades desejadas mais recentes não serem aplicadas com êxito pelo tempo de execução, juntamente com o dispositivo ainda estar a executar um manifesto de implantação anterior.

> [!NOTE]
> As propriedades relatadas do agente do Edge são úteis porque podem ser consultadas com a [linguagem de consulta do Hub IoT][lnk-iothub-query] para investigar o status das implantações em grande escala. Confira [Implantações][lnk-deploy] para obter mais informações sobre como usar esse recurso.

A tabela a seguir não inclui as informações que são copiadas das propriedades desejadas.

| Propriedade | DESCRIÇÃO |
| -------- | ----------- |
| lastDesiredVersion | Este int refere-se à última versão das propriedades desejadas processadas pelo agente do Edge. |
| lastDesiredStatus.code | Este é o código de status que se refere às últimas propriedades desejadas vistas pelo agente do Edge. Valores permitidos: `200` Êxito, `400` Configuração inválida, `412` Versão do esquema inválido, `417` As propriedades desejadas estão vazias, `500` Falha |
| lastDesiredStatus.description | A descrição do status |
| deviceHealth | `healthy` se o status do tempo de execução de todos os módulos for `running` ou `stopped`, caso contrário, `unhealthy` |
| configurationHealth.{deploymentId}.health | `healthy` se o status do tempo de execução de todos os módulos definido pela implantação {deploymentId} for `running` ou `stopped`, caso contrário, `unhealthy` |
| runtime.platform.OS | Relatório do sistema operacional em execução no dispositivo |
| runtime.platform.architecture | Relatório da arquitetura do CPU do dispositivo |
| systemModules.edgeAgent.runtimeStatus | O status relatado do agente do Edge: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Descrição em texto do status relatado do agente do Edge. |
| systemModules.edgeHub.runtimeStatus | Status atual do hub do Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Texto de descrição do status atual do hub do Edge, se não estiver íntegro. |
| systemModules.edgeHub.exitCode | Se tiver encerrado, o código de saída relatado pelo contêiner de hub do Edge |
| systemModules.edgeHub.startTimeUtc | Hora em que o hub do Edge foi iniciado pela última vez |
| systemModules.edgeHub.lastExitTimeUtc | Hora em que o hub do Edge foi encerrado pela última vez |
| systemModules.edgeHub.lastRestartTimeUtc | Hora em que o hub do Edge foi reiniciado pela última vez |
| systemModules.edgeHub.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinicialização. |
| modules.{moduleId}.runtimeStatus | Status atual do módulo: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | Texto de descrição do status atual do módulo, se não estiver íntegro. |
| modules.{moduleId}.exitCode | Se tiver encerrado, o código de saída relatado pelo contêiner do módulo |
| modules.{moduleId}.startTimeUtc | Hora em que o módulo foi iniciado pela última vez |
| modules.{moduleId}.lastExitTimeUtc | Hora em que o módulo foi encerrado pela última vez |
| modules.{moduleId}.lastRestartTimeUtc | Hora em que o módulo foi reiniciado pela última vez |
| modules.{moduleId}.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinicialização. |

## <a name="reference-edge-hub-module-twin"></a>Referência: gêmeo de módulo de hub do Edge

O gêmeo do módulo para o hub do Edge se chama `$edgeHub` e coordena as comunicações entre o hub do Edge em execução em um dispositivo e o Hub IoT.
As propriedades desejadas são definidas durante a aplicação de um manifesto de implantação em um dispositivo específico como parte de uma implantação de dispositivo único ou em escala. Confira [Implantação][lnk-deploy] para obter mais informações sobre como implantar módulos em dispositivos do IoT Edge.

### <a name="edge-hub-twin-desired-properties"></a>Propriedades desejadas do gêmeo do hub do Edge

| Propriedade | DESCRIÇÃO | Necessárias no manifesto de implantação |
| -------- | ----------- | -------- |
| schemaVersion | Tem que ser "1.0" | sim |
| routes.{routeName} | Uma cadeia de caracteres que representa uma rota do hub do Edge. | O elemento `routes` pode estar presente mas vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que o hub do Edge mantém as mensagens no caso de pontos de extremidade de roteamentos se desconectarem, por exemplo: desconectado do Hub IoT ou do módulo local | sim |

### <a name="edge-hub-twin-reported-properties"></a>Propriedades relatadas do gêmeo do hub do Edge

| Propriedade | DESCRIÇÃO |
| -------- | ----------- |
| lastDesiredVersion | Este int refere-se à última versão das propriedades desejadas processadas pelo hub do Edge. |
| lastDesiredStatus.code | Este é o código de status que se refere às últimas propriedades desejadas vistas pelo hub do Edge. Valores permitidos: `200` Êxito, `400` Configuração inválida, `500` Falha |
| lastDesiredStatus.description | A descrição do status |
| clients.{identidade do dispositivo ou módulo}.status | O status de conectividade do dispositivo ou do módulo. Valores possíveis {"connected" \| "disconnected"}. Somente as identidades de módulo podem estar em estado desconectado. Os dispositivos downstream conectados ao hub do Edge aparecem somente quando conectados. |
| clients.{identidade do dispositivo ou módulo}.lastConnectTime | Última hora em que o dispositivo ou o módulo estiveram conectados |
| clients.{device or module identity}.lastDisconnectTime | Última vez em que o dispositivo ou módulo esteve desconectado |

## <a name="next-steps"></a>Próximas etapas

Agora que sabe como os módulos do IoT Edge são usados, [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
