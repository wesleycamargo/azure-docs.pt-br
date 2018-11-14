---
title: Composição de módulo do Azure IoT Edge | Microsoft Docs
description: Saiba como um manifesto de implantação declara quais módulos para implantar, como implantá-las e como criar rotas de mensagens entre eles.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 12c53b1fdad4ab8f55c000ca1cb4f08dab7c8a74
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230353"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Aprenda a implantar módulos e estabelecer rotas no IoT Edge

Cada dispositivo IoT Edge executa ao menos dois módulos: $edgeAgent e $edgeHub, que compõem o tempo de execução do IoT Edge. Além disso, qualquer dispositivo IoT Edge pode executar vários módulos para executar qualquer número de processos. Quando você implanta todos esses módulos em um dispositivo de uma vez, você precisa de uma maneira para declarar quais módulos são incluídos como eles interagem entre si. 

O *manifesto de implantação* é um documento JSON que descreve:

* A configuração do agente Edge, que inclui a imagem de contêiner para cada módulo, as credenciais para registros de contêiner privado de acesso e instruções sobre como cada módulo deve ser criado e gerenciado.
* A configuração do hub do Edge, que inclui como as mensagens fluem entre os módulos e, eventualmente, para o Hub IoT.
* Especifique, de forma opcional, as propriedades desejadas do gêmeo de módulo.

Todos os dispositivos IoT Edge precisam ser configurados com um manifesto de implantação. Um tempo de execução do IoT Edge recém-instalado relatará um código de erro até ser configurado com um manifesto válido. 

Nos tutoriais do Azure IoT Edge, você pode criar um manifesto de implantação por meio de um assistente no portal do Azure IoT Edge. Você também pode aplicar um manifesto de implantação por meio de programação usando REST ou o SDK do serviço Hub IoT. Para saber mais, veja [Noções básicas sobre implantações do IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implantação

De forma geral, o manifesto de implantação configura as propriedades desejadas de um módulo gêmeo para módulos do IoT Edge implantados em um dispositivo IoT Edge. Dois desses módulos sempre estão presentes: `$edgeAgent`, e `$edgeHub`.

Um manifesto de implantação que contém o tempo de execução do IoT Edge (hub e agente) é válido.

O manifesto segue esta estrutura:

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
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
```

## <a name="configure-modules"></a>Configurar módulos

Você precisa dizer para o tempo de execução IoT Edge como instalar os módulos na sua implantação. As informações de configuração e gerenciamento para todos os módulos fica dentro das propriedades desejadas do **$edgeAgent**. Essas informações incluem os parâmetros de configuração para o próprio agente Edge. 

Para obter uma lista completa das propriedades que podem ou devem ser incluídos, consulte [Propriedades do agente Edge e hub do Edge](module-edgeagent-edgehub.md).

As propriedades de $edgeAgent seguem esta estrutura:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "{module1}": { // optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Declarar rotas

O hub do Edge fornece uma maneira para rotear de modo declarativo as mensagens entre módulos e entre os módulos e o Hub IoT. O hub do Edge gerencia todas as comunicações, para que as informações de rota fica dentro das propriedades desejadas do **$edgeHub**. Você pode ter várias rotas dentro da mesma implantação.

As rotas são declaradas nas propriedades desejadas do **$edgeHub** com a seguinte sintaxe:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Cada rota precisa de uma origem e um coletor, mas a condição é uma parte opcional que você pode usar para filtrar mensagens. 


### <a name="source"></a>Fonte
A origem especifica de onde as mensagens são provenientes. Pode ser qualquer um dos seguintes valores:

| Fonte | DESCRIÇÃO |
| ------ | ----------- |
| `/*` | Todas as mensagens de dispositivo para nuvem de qualquer dispositivo ou módulo |
| `/messages/*` | Qualquer mensagem de dispositivo para nuvem enviada por um dispositivo ou por um módulo por meio de algumas ou nenhuma saída |
| `/messages/modules/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo por meio de algumas ou nenhuma saída |
| `/messages/modules/{moduleId}/*` | Qualquer mensagem de dispositivo para nuvem enviada por {Idmódulo} sem saída |
| `/messages/modules/{moduleId}/outputs/*` | Qualquer mensagem de dispositivo para nuvem enviada por {IDmódulo} com algumas saídas |
| `/messages/modules/{moduleId}/outputs/{output}` | Qualquer mensagem de dispositivo para nuvem enviada por {IDmódulo} usando {saída} |

### <a name="condition"></a>Condição
A condição é opcional em uma declaração de rota. Se você deseja passar todas as mensagens do coletor para a fonte, deixe-o inteiramente fora da cláusula **WHERE**. Ou você pode usar a [linguagem de consulta de Hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar determinadas mensagens ou tipos de mensagem que satisfazem a condição.

As mensagens que passam entre módulos no IoT Edge são formatadas da mesma forma que as mensagens que passam entre seus dispositivos e o Hub IoT do Azure. Todas as mensagens são formatadas como JSON e têm os parâmetros **systemProperties**, **appProperties** e **body**. 

Você pode criar consultas baseadas nos três parâmetros com a seguinte sintaxe: 

* Propriedades do sistema: `$<propertyName>` ou `{$<propertyName>}`
* Propriedades do aplicativo: `<propertyName>`
* Propriedades do corpo: `$body.<propertyName>` 

Para obter exemplos sobre como criar consultas para propriedades de mensagem, consulte [Expressões de consulta de rotas de mensagem do dispositivo para a nuvem](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Um exemplo específico do IoT Edge é quando você deseja filtrar as mensagens que chegaram a um dispositivo de gateway vindo de um dispositivo de folha. Mensagens que chegam de módulos contêm uma propriedade do sistema chamada **connectionModuleId**. Então se você quiser rotear mensagens de dispositivos de folha diretamente ao Hub IoT, use a rota a seguir para excluir mensagens de módulo:

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Coletor
O coletor define onde as mensagens são enviadas. Pode ser qualquer um dos seguintes valores:

| Coletor | DESCRIÇÃO |
| ---- | ----------- |
| `$upstream` | Enviar a mensagem para o Hub IoT |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Enviar a mensagem para a entrada `{input}` do módulo`{moduleId}` |

IoT Edge fornece garantias at-least-once. O hub Edge armazena mensagens localmente no caso de uma rota não é possível entregar a mensagem para o coletor. Por exemplo, se o hub Edge não pode se conectar ao IoT Hub ou o módulo de destino não está conectado.

O hub do Edge armazena as mensagens até à hora especificada na propriedade `storeAndForwardConfiguration.timeToLiveSecs` das [propriedades desejadas do hub do Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definir ou atualizar as propriedades desejadas 

O manifesto de implantação pode especificar propriedades desejadas para o gêmeo do módulo de cada módulo implantado no dispositivo IoT Edge. Quando as propriedades desejadas são especificadas no manifesto de implantação, substituem qualquer propriedade desejada atualmente no gêmeo do módulo.

Se você não especificar as propriedades desejadas de um gêmeo de módulo no manifesto de implantação, o Hub IoT não modifica o gêmeo de módulo de qualquer forma, e você poderá definir as propriedades desejadas programaticamente.

Os mesmos mecanismos que permitem que você modifique dispositivos gêmeos são usados para modificar módulos gêmeos. Para obter mais informações, consulte o [guia do desenvolvedor do módulo gêmeo](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Exemplo de manifesto de implantação

Este é um exemplo de um documento JSON do manifesto de implantação.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "{password}",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
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
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
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
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa das propriedades que podem ou devem ser incluídos em $edgeAgent e $edgeHub, consulte [Propriedades do agente Edge e hub do Edge](module-edgeagent-edgehub.md).

* Agora que sabe como os módulos do IoT Edge são usados, [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge](module-development.md).
