---
title: "Variáveis de ambiente do Azure Service Fabric | Microsoft Docs"
description: "Documentação de referência para as variáveis de ambiente do Service Fabric"
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: a4bf082f5bd5a57bb5eb7641a25176e3a06503a3
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2018
---
# <a name="service-fabric-environment-variables"></a>Variáveis de ambiente do Service Fabric

O Service Fabric tem variáveis de ambiente internas definidas para cada instância de serviço. A lista completa de variáveis de ambiente está abaixo:

| Variável de ambiente                         | DESCRIÇÃO                                                            | Exemplo                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | O nome do uri da malha do aplicativo                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | O nome do pacote de código ao qual pertence o processo              | Código                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | O endereço ip ou FQDN do ponto de extremidade                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Número da porta do ponto de extremidade                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Pasta de logs                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Pasta Temp                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Pasta de trabalho                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | A pasta base de aplicativos                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Um booliano que especifica se o processo é um contêiner                   | falso                                                                |
| Fabric_NodeId                                | A ID do nó que executa o processo                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | O IP ou FQDN do nó, conforme for especificado no arquivo de manifesto do cluster. | localhost ou 10.0.0.1                                                |
| Fabric_NodeName                              | O nome do nó que executa o processo                          | _Node_0                                                              |
| Fabric_ServiceName                           | O nome do serviço, se o serviço estiver hospedado no modo ExclusiveProcess  | MyService                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | UMA GUID                                                               |
| Fabric_ServicePackageName                    | Nome do pacote de serviço do qual o processo faz parte                     | Web1Pkg                                                              |

Variáveis de ambiente interno usadas pelo tempo de execução do Service Fabric:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName