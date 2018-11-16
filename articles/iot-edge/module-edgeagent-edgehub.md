---
title: Referência do Azure IoT EdgeAgent e EdgeHub | Microsoft Docs
description: Revise as propriedades específicas e os respectivos valores para os módulos gêmeos edgeAgent e edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 01e1942b12de126aa34130f5a4b77dd0fb958aa6
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568913"
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Propriedades dos módulos gêmeos agente do Edge e hub do Edge

O agente do Edge e hub do Edge são dois módulos que compõem o tempo de execução do IoT Edge. Para obter mais informações sobre quais funções cada módulo executa, consulte [Entender o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md). 

Este artigo fornece as propriedades desejadas e as propriedades relatadas dos módulos gêmeos de tempo de execução. Para saber mais sobre como implantar módulos em dispositivos do IoT Edge, confira [Implantação e monitoramento](module-deployment-monitoring.md).

## <a name="edgeagent-desired-properties"></a>Propriedades desejadas do EdgeAgent

O gêmeo do módulo para o agente do Edge se chama `$edgeAgent` e coordena as comunicações entre o agente do Edge em execução em um dispositivo e o Hub IoT. As propriedades desejadas são definidas durante a aplicação de um manifesto de implantação em um dispositivo específico como parte de uma implantação de dispositivo único ou em escala. 

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| schemaVersion | Tem que ser "1.0" | SIM |
| runtime.type | Tem que ser "docker" | SIM |
| runtime.settings.minDockerVersion | Definido para a versão mínima do Docker necessária para o manifesto de implantação | SIM |
| runtime.settings.loggingOptions | Um JSON em cadeias de caracteres que contém as opções de registro para o contêiner do agente do Edge. [Opções de registro em log do Docker](https://docs.docker.com/engine/admin/logging/overview/) | Não  |
| runtime.settings.registryCredentials<br>.{registryId}.username | O nome de usuário do registro de contêiner. Para registro de contêiner do Azure, o nome de usuário geralmente é o nome do registro.<br><br> Credenciais de registro são necessárias para as imagens de módulo que não são públicas. | Não  |
| runtime.settings.registryCredentials<br>.{registryId}.password | A senha para o registro de contêiner. | Não  |
| runtime.settings.registryCredentials<br>.{registryId}.address | O endereço do registro de contêineres. Para registro de contêiner do Azure, o endereço é geralmente *{registryname}.azurecr.io*. | Não  |  
| systemModules.edgeAgent.type | Tem que ser "docker" | SIM |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente do Edge. Atualmente, o agente do Edge não é capaz de se atualizar. | SIM |
| systemModules.edgeAgent.settings<br>.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do agente do Edge. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não  |
| systemModules.edgeAgent.configuration.id | A ID da implantação que implantou este módulo. | Esta propriedade é definida pelo Hub IoT quando este manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| systemModules.edgeHub.type | Tem que ser "docker" | SIM |
| systemModules.edgeHub.status | Deve estar "em execução" | SIM |
| systemModules.edgeHub.restartPolicy | Deve estar "sempre" | SIM |
| systemModules.edgeHub.settings.image | O URI da imagem do hub do Edge. | SIM |
| systemModules.edgeHub.settings<br>.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do hub do Edge. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não  |
| systemModules.edgeHub.configuration.id | A ID da implantação que implantou este módulo. | Esta propriedade é definida pelo Hub IoT quando este manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| modules.{moduleId}.version | Uma cadeia definida pelo usuário que representa a versão desse módulo. | SIM |
| modules.{moduleId}.type | Tem que ser "docker" | SIM |
| modules.{moduleId}.status | {"running" \| "stopped"} | SIM |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | SIM |
| modules.{moduleId}.settings.image | O URI para a imagem do módulo. | SIM |
| modules.{moduleId}.settings.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do módulo. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não  |
| modules.{moduleId}.configuration.id | A ID da implantação que implantou este módulo. | Esta propriedade é definida pelo Hub IoT quando este manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |

## <a name="edgeagent-reported-properties"></a>Propriedades relatadas do EdgeAgent

As propriedades relatadas pelo agente do Edge incluem três partes de informação principais:

1. O status do aplicativo das propriedades desejadas vistas pela última vez;
2. O status dos módulos em execução no momento no dispositivo, conforme relatado pelo agente de borda; e
3. Uma cópia das propriedades desejadas atualmente em execução no dispositivo.

Esta última informação é útil no caso de as propriedades desejadas mais recentes não serem aplicadas com êxito pelo tempo de execução, juntamente com o dispositivo ainda estar a executar um manifesto de implantação anterior.

> [!NOTE]
> As propriedades relatadas do agente do Edge são úteis porque podem ser consultadas com a [linguagem de consulta do Hub IoT](../iot-hub/iot-hub-devguide-query-language.md) para investigar o status das implantações em grande escala. Para saber mais sobre como usar as propriedades de agente Edge para status, confira [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

A tabela a seguir não inclui as informações que são copiadas das propriedades desejadas.

| Propriedade | DESCRIÇÃO |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo agente do Edge. |
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

## <a name="edgehub-desired-properties"></a>Propriedades desejadas do EdgeHub

O gêmeo do módulo para o hub do Edge se chama `$edgeHub` e coordena as comunicações entre o hub do Edge em execução em um dispositivo e o Hub IoT. As propriedades desejadas são definidas durante a aplicação de um manifesto de implantação em um dispositivo específico como parte de uma implantação de dispositivo único ou em escala. 

| Propriedade | DESCRIÇÃO | Necessárias no manifesto de implantação |
| -------- | ----------- | -------- |
| schemaVersion | Tem que ser "1.0" | SIM |
| routes.{routeName} | Uma cadeia de caracteres que representa uma rota do hub do Edge. | O elemento `routes` pode estar presente mas vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que o hub do Edge mantém mensagens no caso de pontos de extremidade de roteamento desconectados, por exemplo, desconectados do Hub IoT ou módulo local | SIM |

## <a name="edgehub-reported-properties"></a>Propriedades relatadas do EdgeHub

| Propriedade | DESCRIÇÃO |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo hub do Edge. |
| lastDesiredStatus.code | Este é o código de status que se refere às últimas propriedades desejadas vistas pelo hub do Edge. Valores permitidos: `200` Êxito, `400` Configuração inválida, `500` Falha |
| lastDesiredStatus.description | A descrição do status |
| clients.{device or moduleId}.status | O status de conectividade do dispositivo ou do módulo. Valores possíveis {"connected" \| "disconnected"}. Somente as identidades de módulo podem estar em estado desconectado. Os dispositivos downstream conectados ao hub do Edge aparecem somente quando conectados. |
| clients.{device or moduleId}.lastConnectTime | Última hora em que o dispositivo ou o módulo estiveram conectados |
| clients.{device or moduleId}.lastDisconnectTime | Última vez em que o dispositivo ou módulo esteve desconectado |

## <a name="next-steps"></a>Próximas etapas

Para saber como utilizar essas propriedades para compilar manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
