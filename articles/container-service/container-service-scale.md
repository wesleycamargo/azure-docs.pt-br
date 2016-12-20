---
title: Dimensione seu cluster ACS com a CLI do Azure | Microsoft Docs
description: "Como dimensionar o cluster do Serviço de Contêiner do Azure usando a CLI do Azure."
services: container-service
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure"
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>Dimensionar um Serviço de Contêiner do Azure
Você pode escalar horizontalmente o número de nós que seu Serviço de Contêiner do Azure (ACS) tem, usando a ferramenta de CLI do Azure. Quando você usar a CLI do Azure para dimensionar, a ferramenta retorna um novo arquivo de configuração que representa a alteração aplicada ao contêiner.

## <a name="about-the-command"></a>Sobre o comando
A CLI do Azure deve estar no modo Azure Resource Manager para interagir com os contêineres do Azure. Você pode mudar para o modo do Gerenciador de Recursos chamando `azure config mode arm`. O comando `acs` tem um comando filho chamado `scale`, que realiza todas as operações de escala para um serviço de contêiner. Você pode obter ajuda sobre os vários parâmetros usados no comando escala executando `azure acs scale --help`, que gera algo semelhante a isto:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Use o comando para dimensionar
Para dimensionar um serviço de contêiner, primeiro você precisa saber o **grupo de recursos** e o **nome do Serviço de Contêiner do Azure (ACS)**, e também especificar a nova contagem de agentes. Usando um valor menor ou maior, você pode, respectivamente, reduzir verticalmente ou escalar verticalmente.

Você talvez queira saber qual a contagem atual de agentes para um serviço de contêiner antes de dimensionar. Use o comando `azure acs show <resource group> <ACS name>` para retornar a configuração do ACS. Observe o resultado <mark>Contagem</mark>.

#### <a name="see-current-count"></a>Veja a contagem atual
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Dimensionar para a nova a contagem
Como provavelmente já é óbvio, você pode dimensionar o serviço de contêiner chamando `azure acs scale` e fornecendo o **grupo de recursos**, **nome do ACS** e a **contagem de agentes**. Quando você dimensiona um serviço de contêiner, a CLI do Azure retorna uma cadeia de caracteres JSON que representa a nova configuração do serviço de contêiner, incluindo a nova contagem de agentes.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Próximas etapas
* [Implantar um cluster](container-service-deployment.md)




<!--HONumber=Nov16_HO3-->


