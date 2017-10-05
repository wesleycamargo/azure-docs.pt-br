---
title: "Versão prévia do Docker Compose do Azure Service Fabric"
description: "O Azure Service Fabric aceita o formato do Docker Compose para facilitar a orquestração dos contêineres existentes usando o Service Fabric. No momento, esse suporte está na versão prévia."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 519bab9d226f9d00ae0fa21348823d2d6b6cd2c9
ms.contentlocale: pt-br
ms.lasthandoff: 09/26/2017

---
# <a name="docker-compose-application-support-in-azure-service-fabric-preview"></a>Suporte ao aplicativo Docker Compose no Azure Service Fabric (Versão prévia)

O docker usa o arquivo [docker-compose.yml](https://docs.docker.com/compose) para definir aplicativos de vários contêineres. A fim de facilitar para os clientes familiarizados com o Docker orquestrarem aplicativos de contêiner existentes no Azure Service Fabric, incluímos o suporte da versão prévia para o Docker Compose nativamente na plataforma. O Service Fabric pode aceitar a versão 3 e posteriores de arquivos `docker-compose.yml`. 

Como esse suporte está na versão prévia, há suporte para apenas um subconjunto de diretivas do Compose. Por exemplo, não há suporte para atualizações de aplicativo. No entanto, você sempre pode remover e implantar aplicativos em vez de atualizá-los.

Para usar essa versão prévia, crie o cluster com a versão 5.7 ou superior do tempo de execução do Service Fabric por meio do portal do Azure junto com o SDK correspondente. 

> [!NOTE]
> Esse recurso está na versão prévia e não tem suporte.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Implantar um arquivo do Docker Compose no Service Fabric

Os seguintes comandos criam um aplicativo do Service Fabric (nomeado `TestContainerApp`), que você poderá monitorar e gerenciar, como qualquer outro aplicativo do Service Fabric. Você pode usar o nome do aplicativo especificado para consultas de integridade.

### <a name="use-powershell"></a>Usar o PowerShell

Criar uma implantação do Compose do Service Fabric a partir de um arquivo docker-compose.yml executando o seguinte comando no PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` e `RegistryPassword` se referem ao nome de usuário e senha de registro de contêiner. Após concluir a implantação, você poderá verificar o status utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp -GetAllPages
```

Para excluir a implantação do Compose através de PowerShell, utilize o seguinte comando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Usar a CLI do Azure Service Fabric (sfctl)

Como alternativa, você pode usar o seguinte comando da CLI do Service Fabric:

```azurecli
sfctl compose create --application-id TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

Depois de criar o aplicativo, você pode verificar seu status usando o seguinte comando:

```azurecli
sfctl compose status --application-id TestContainerApp [ --timeout ]
```

Para excluir o aplicativo do Compose, use o seguinte comando:

```azurecli
sfctl compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>Diretivas do Compose com suporte

Esta versão prévia dá suporte a um subconjunto das opções de configuração do formato da versão 3 do Compose, incluindo os primitivos a seguir:

* Serviços > Implantar > Réplicas
* Serviços > Implantar > Posicionamento > Restrições
* Serviços > Implantar > Recursos > Limites
    * -cpu-shares
    * -memory
    * -memory-swap
* Serviços > Comandos
* Serviços > Ambiente
* Serviços > Portas
* Serviços > Imagem
* Serviços > Isolamento (somente para Windows)
* Serviços > Registro em log > Driver
* Serviços > Registro em log > Driver > Opções
* Volume e Implantar > Volume

Configure o cluster para impor limites de recursos, conforme descrito na [Governança de recursos do Service Fabric](service-fabric-resource-governance.md). Todas as outras diretivas do Docker Compose não têm suporte nessa versão prévia.

## <a name="servicednsname-computation"></a>Computação de ServiceDnsName

Se o nome do serviço que você especifica em um arquivo do Compose é um nome de domínio totalmente qualificado (isto é, contem um ponto, [.]), o nome DNS registrado pelo Service Fabric será `<ServiceName>` (incluindo o ponto). Caso contrário, cada segmento de caminho no nome de aplicativo se tornará um rótulo de domínio no nome DNS do serviço com o primeiro segmento de caminho se tornando o rótulo de domínio primário.

Por exemplo, se o nome especificado do aplicativo for `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` seria o nome DNS registrado.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Diferenças entre o modelo de aplicativo do Compose (definição de instância) e do Service Fabric (definição de tipo)

Um arquivo docker-compose.yml descreve um conjunto de contêineres implantável, incluindo suas propriedades e configurações.
Por exemplo, o arquivo pode conter variáveis de ambiente e portas. Você também pode especificar parâmetros de implantação, como restrições de posicionamento, limites de recursos e nomes DNS, no arquivo docker-compose.yml.

O [modelo de aplicativo do Service Fabric](service-fabric-application-model.md) usa tipos de serviço e tipos de aplicativo, em que é possível ter várias instâncias de aplicativo do mesmo tipo. Por exemplo, você pode ter uma instância de aplicativo por cliente. Esse modelo baseado em tipo dá suporte a várias versões do mesmo tipo de aplicativo que é registrado com o tempo de execução.

Por exemplo, o cliente A pode ter um aplicativo instanciado com tipo 1.0 do AppTypeA e o cliente B pode ter outro aplicativo instanciado com o mesmo tipo e versão. Você define os tipos de aplicativos nos manifestos do aplicativo e especifica os parâmetros de nome e implantação do aplicativo ao criar o aplicativo.

Embora esse modelo ofereça flexibilidade, também estamos planejando dar suporte a um modelo de implantação baseado em instâncias mais simples em que os tipos são implícitos no arquivo de manifesto. Nesse modelo, cada aplicativo obtém seu próprio manifesto independente. Estamos fazendo a versão prévia desse esforço adicionando suporte para o docker-compose.yml, que é um formato de implantação baseado em instância.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre o [modelo de aplicativo do Service Fabric](service-fabric-application-model.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

